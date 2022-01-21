# Metricbeat 연동 (Update: 2021-11-04)
#### 진행 상황
```
- 완료    : [Metricbeat → Elasticsearch] metricbeat로 ELK CPU, 메모리, 트래픽(in/outbound) 정보 실시간 모니터링
- 진행중  : 수집한 메트릭으로 대시보드 생성
```

## 1. metricbeat 설정 파일 수정 (`docker-elk\extensions\metricbeat`)
두가지 파일을 수정한다.
- `docker-elk\extensions\metricbeat\config\metricbeat.yml`
- `docker-elk\extensions\metricbeat\metricbeat-compose.yml`   
  
  ### 1) `docker-elk\extensions\metricbeat\config\metricbeat.yml`
  ```
  ## Metricbeat configuration
  ## https://github.com/elastic/beats/blob/master/deploy/docker/metricbeat.docker.yml
  #

  metricbeat.config:
   modules:
     path: ${path.config}/modules.d/*.yml
     # Reload module configs as they change:
     reload.enabled: true
  
  metricbeat.autodiscover:
   providers:
     - type: docker
       hints.enabled: true
  
  metricbeat.modules:
  - module: docker
    metricsets:
     - container
     - cpu
     - diskio
     - healthcheck
     - info
     #- image
     - memory
     - network
   hosts: ['unix:///var/run/docker.sock']
   period: 10s
   enabled: true
  
  processors:
   - add_cloud_metadata: ~
  
  output.elasticsearch:
    hosts: ['http://elasticsearch:9200']
    username: elastic
    password: password #비밀번호 입력
  
  setup.kibana:
    hosts: ['http://kibana:5601']
    username: elastic
    password: password #비밀번호 입력
  
  
  ## HTTP endpoint for health checking
  ## https://www.elastic.co/guide/en/beats/metricbeat/current/http-endpoint.html
  #
  
  http.enabled: true
  http.host: 0.0.0.0
  
  ```

  ### 2) `docker-elk\extensions\metricbeat\metricbeat-compose.yml`
  ```
  version: '3.2'

  services:
    metricbeat:
     container_name: metricbeat
      build:
       context: extensions/metricbeat/
       args:
         ELK_VERSION: $ELK_VERSION
     # Run as 'root' instead of 'metricbeat' (uid 1000) to allow reading
     # 'docker.sock' and the host's filesystem.
     user: root
     command:
         # Log to stderr.
       - -e
         # Disable config file permissions checks. Allows mounting
         # 'config/metricbeat.yml' even if it's not owned by root.
         # see: https://www.elastic.co/guide/en/beats/libbeat/current/config-file-permissions.html
       - --strict.perms=false
         # Mount point of the host’s filesystem. Required to monitor the host
         # from within a container.
       - --system.hostfs=/hostfs
     volumes:
       - type: bind
         source: ./extensions/metricbeat/config/metricbeat.yml
         target: /usr/share/metricbeat/metricbeat.yml
         read_only: true
       - type: bind
         source: /
         target: /hostfs
          read_only: true
       - type: bind
         source: /sys/fs/cgroup
         target: /hostfs/sys/fs/cgroup
         read_only: true
       - type: bind
         source: /proc
         target: /hostfs/proc
         read_only: true
       - type: bind
          source: /var/run/docker.sock
          target: /var/run/docker.sock
          read_only: true
     networks:
       - elk
     depends_on:
       - elasticsearch
       - kibana
  ```

<br>  

## 2. metricbeat 시작하기
  ```
  # docker-elk 클론했던 경로에서 명령어 실행
  PS ...\docker-elk > docker-compose -f docker-compose.yml -f extensions/metricbeat/metricbeat-compose.yml up -d
  
  # 프로세스 동작 확인
  PS ...\docker-elk > docker-compose ps
  ```
  ![image](https://user-images.githubusercontent.com/90135804/140255909-b68ebabf-f4a9-494d-9b26-e1f20f836b3a.png)

<br>  

## 3. 메트릭 확인, View 설정 : Observability > Metrics
1. Show - Docker Containers 선택 (컨테이너 별로 확인하기 위해)
![image](https://user-images.githubusercontent.com/90135804/140251818-482f758f-62c5-464b-9cd9-50e73a2b2514.png)  

2. Group by - Service type 선택 (Docker 그룹화 - 필수 아님)
![image](https://user-images.githubusercontent.com/90135804/140252025-81a40d8a-fe36-4325-a78b-45e380b6b232.png)  

3. Legend Options 설정 - (**Color palette** : Temperature, **calculate range** : minimum 0, maximum 100으로 설정) - Apply
![image](https://user-images.githubusercontent.com/90135804/140258108-2628b19b-2735-4a1c-8f79-3132c5e72e3f.png)

4. Save new view
![image](https://user-images.githubusercontent.com/90135804/140252356-a95a210d-08d2-4a66-ada1-2c84739bfb3e.png)  
  원하는 이름(metricbeat) 입력 후 Save
    ![image](https://user-images.githubusercontent.com/90135804/140252488-3170816d-5265-434a-8e49-63fd60fb6714.png)  

5. Manage views
![image](https://user-images.githubusercontent.com/90135804/140252692-678cc338-0af8-4780-b8b3-38d5b69ce463.png)
  별모양 클릭 -> 생성한 'metricbeat' View를 default view로 설정
    ![image](https://user-images.githubusercontent.com/90135804/140252830-68bdf98c-b600-451c-827c-601e176e99f2.png)

<br>  

## 4. 메트릭 수집 결과 확인
`Observability > Metrics`를 누르면, 저장했던 default view로 바로 확인 가능  
  ![image](https://user-images.githubusercontent.com/90135804/140258542-7d3ac530-2a79-4a7b-bc9e-e48a13f4ffc5.png)  
Auto-refresh를 누르면, 자동으로 리프레시되면서 실시간 메트릭을 볼 수 있다.  
  ![image](https://user-images.githubusercontent.com/90135804/140258623-1a2d66f6-998a-4bb0-8b3b-eaad8465f8ed.png)  

원하는 컨테이너 박스 클릭 > Docker container metrics 를 누르면, 각 컨테이너 별로 시간별 CPU, 메모리 사용량, 네트워크 트래픽(in/outbound) 등을 확인할 수 있다.  
  ![image](https://user-images.githubusercontent.com/90135804/140258688-4e2d74bc-d298-426e-a268-72e70e60b33a.png)  
  ![image](https://user-images.githubusercontent.com/90135804/140258734-f1153248-0218-4a95-89cb-136715726543.png)

