# ELK 서버 구축 (with docker compose)
```
OS       : Windows 10
개발환경  : Docker 20.10.8, ELK 7.15.0
```

## 1. 사전 준비

### Docker 설치
도커 설치를 위해 [잘 정리된 글](https://www.lainyzine.com/ko/article/a-complete-guide-to-how-to-install-docker-desktop-on-windows-10/)을 참고하였고, 설치 과정은 [이 문서](https://github.com/K-Shield-Jr/EDR/blob/main/%EC%A0%95%EB%A6%AC/1.%20%ED%99%98%EA%B2%BD%EA%B5%AC%EC%B6%95/1\)%20Docker.md)에 정리되어 있다.  
<br>

### Docker 설치 확인
터미널에서 `docker -v` 명령어를 통해 버전 확인 및 제대로 설치되었는지 확인해본다.  

```powershell
docker -v
```

다음과 같이 버전 정보가 나오면 설치가 되어있는 것이다.  
![image](https://user-images.githubusercontent.com/90135804/148461865-2eac7a6f-def1-43a7-a7f7-ccc4d6d6103a.png)  

---

ELK 서버를 구축할 방법은 Docker compose를 활용한 방법이다.  
- Github Repo : [Elastic stack (ELK) on Docker | github](https://github.com/deviantony/docker-elk)
- 참고 블로그 : [[ELK] Docker를 이용한 엘라스틱서치 스택(ELK) 구축_Window](https://plitche.github.io/database/elk/2021-05-11-installELK/)  
<br>

## 2. docker-elk 다운로드

### Github Repository clone
ELK Github Repository를 클론해서 ELK 3가지를 전부 통합으로 구축할 것이다. (git 설치 필수)  
- Elastic Search
- Logstash
- Kibana  

먼저 docker-elk를 클론하고자 하는 경로로 이동 후, 깃허브 레포지토리를 클론한다.  

```bash
cd C:\Users\User\Desktop\KSJ\ELK

git clone https://github.com/deviantony/docker-elk.git
```  

![image](https://user-images.githubusercontent.com/90135804/148462057-2e346dd9-2c4c-4453-ab83-7a7d52dcb217.png)  
<br>

## 3. setting (설정)
클론한 docker-elk에서 하나씩 설정을 변경해준다. 편집기를 이용해서 다음 경로의 파일들을 열고 내용을 변경한다.  

### 1) `docker-elk/elasticsearch/config/elasticsearch.yml` 파일

<details><summary><b>BEFORE</b></summary>
<p>

```yaml
---
## Default Elasticsearch configuration from Elasticsearch base image.
## https://github.com/elastic/elasticsearch/blob/master/distribution/docker/src/docker/config/elasticsearch.yml
#
cluster.name: "docker-cluster"
network.host: 0.0.0.0

## X-Pack settings
## see https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-xpack.html
#
xpack.license.self_generated.type: trial
xpack.security.enabled: true
xpack.monitoring.collection.enabled: true
```

</p>
</details>  

#### AFTER

```yaml
---
## Default Elasticsearch configuration from Elasticsearch base image.
## https://github.com/elastic/elasticsearch/blob/master/distribution/docker/src/docker/config/elasticsearch.yml
#
cluster.name: "docker-cluster"
network.host: 0.0.0.0
discovery.type: single-node

## X-Pack settings
## see https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-xpack.html
#
#xpack.license.self_generated.type: trial
xpack.security.enabled: true
xpack.monitoring.collection.enabled: true
```

변경사항)  X-pack 관련 설정 중 license 부분 주석 처리  
<br>

### 2) `docker-elk/elasticsearch/Dockerfile` 파일

<details><summary><b>BEFORE</b></summary>
<p>

```yaml
ARG ELK_VERSION

# https://www.docker.elastic.co/
FROM docker.elastic.co/elasticsearch/elasticsearch:${ELK_VERSION}

# Add your elasticsearch plugins setup here
# Example: RUN elasticsearch-plugin install analysis-icu
```

</p>
</details>  

#### AFTER

```yaml
ARG ELK_VERSION

# https://www.docker.elastic.co/
FROM docker.elastic.co/elasticsearch/elasticsearch:${ELK_VERSION}

# Add your elasticsearch plugins setup here
# Example: RUN elasticsearch-plugin install analysis-icu
RUN elasticsearch-plugin install analysis-nori
```  

변경사항)  마지막 줄에 한글 분석기 **nori** 설치 명령 추가  
<br>

### 3) `docker-elk/kibana/config/kibana.yml` 파일

<details><summary><b>BEFORE</b></summary>
<p>

```yaml
---
## Default Kibana configuration from Kibana base image.
## https://github.com/elastic/kibana/blob/master/src/dev/build/tasks/os_packages/docker_generator/templates/kibana_yml.template.ts
#
server.name: kibana
server.host: 0.0.0.0
elasticsearch.hosts: [ "http://elasticsearch:9200" ]
monitoring.ui.container.elasticsearch.enabled: true

## X-Pack security credentials
#
elasticsearch.username: elastic
elasticsearch.password: changeme
```

</p>
</details>  

#### AFTER

```yaml
---
## Default Kibana configuration from Kibana base image.
## https://github.com/elastic/kibana/blob/master/src/dev/build/tasks/os_packages/docker_generator/templates/kibana_yml.template.ts
#
server.name: kibana
server.host: "0.0.0.0"
elasticsearch.hosts: [ "http://elasticsearch:9200" ]
monitoring.ui.container.elasticsearch.enabled: true

## X-Pack security credentials
#
elasticsearch.username: elastic
elasticsearch.password: 비밀번호
```

변경사항)  X-Pack Security 설정에서 elasticsearch 패스워드 변경 (설정하지 않으면, 사용 중 보안 관련 warning이 계속 뜨게 됨)  
변경사항)  `server.host`: 0.0.0.0 -> "0.0.0.0" 변경 (별 차이는 없다)  
<br>

### 4) `docker-elk/logstash/config/logstash.yml` 파일

<details><summary><b>BEFORE</b></summary>
<p>

```yaml
---
## Default Logstash configuration from Logstash base image.
## https://github.com/elastic/logstash/blob/master/docker/data/logstash/config/logstash-full.yml
#
http.host: "0.0.0.0"
xpack.monitoring.elasticsearch.hosts: [ "http://elasticsearch:9200" ]

## X-Pack security credentials
#
xpack.monitoring.enabled: true
xpack.monitoring.elasticsearch.username: elastic
xpack.monitoring.elasticsearch.password: changeme
```

</p>
</details>     

#### AFTER

```yaml
---
## Default Logstash configuration from Logstash base image.
## https://github.com/elastic/logstash/blob/master/docker/data/logstash/config/logstash-full.yml
#
http.host: "0.0.0.0"
xpack.monitoring.elasticsearch.hosts: [ "http://elasticsearch:9200" ]

## X-Pack security credentials
#
xpack.monitoring.enabled: true
xpack.monitoring.elasticsearch.username: elastic
xpack.monitoring.elasticsearch.password: 비밀번호
```

변경사항)  X-Pack Security 설정에서 elasticsearch 패스워드 변경 (설정하지 않으면, 사용 중 보안 관련 warning이 계속 뜨게 됨)  
<br>

### 5) `docker-elk/logstash/pipeline/logstash.conf` 파일

<details><summary><b>BEFORE</b></summary>
<p>

```conf
input {
  beats {
    port => 5044
  }
  
  tcp {
    port => 5000
  }
}

## Add your filters / logstash plugins configuration here

output {
  elasticsearch {
    hosts => "elasticsearch:9200"
    user => "elastic"
    password => "changeme"
    ecs_compatibility => disabled
  }
}
```

</p>
</details>  

#### AFTER

```conf
input {
	beats {
		port => 5044
	}

	tcp {
		port => 5000
	}
}

# Logstash의 가공한 정보를 어디에 출력할지 설정
# 모든 데이터를 ~~beat-%{+YYYY.MM.dd}라는 이름의 인덱스를 만들어서 Elasticsearch로 보내도록 설정
output {
	elasticsearch {
		hosts => "elasticsearch:9200"
		user => "elastic"
		password => "비밀번호"
		index => "%{[@metadata][beat]}-%{+YYYY.MM.dd}"
		document_type => "%{[@metadata][type]}"
	}
}
```

변경사항)  logstash의 출력으로 사용할 elasticsearch의 패스워드를 바꾸고, `index`, `document_type` 추가  
<br>

### 6) `docker-elk/docker-compose.yml` 파일

<details><summary><b>BEFORE</b></summary>
<p>

```yaml
version: '3.2'

services:
  elasticsearch:
    build:
      context: elasticsearch/
      args:
        ELK_VERSION: $ELK_VERSION
    volumes:
      - type: bind
        source: ./elasticsearch/config/elasticsearch.yml
        target: /usr/share/elasticsearch/config/elasticsearch.yml
        read_only: true
      - type: volume
        source: elasticsearch
        target: /usr/share/elasticsearch/data
    ports:
      - "9200:9200"
      - "9300:9300"
    environment:
      ES_JAVA_OPTS: "-Xmx256m -Xms256m"
      ELASTIC_PASSWORD: changeme
      # Use single node discovery in order to disable production mode and avoid bootstrap checks.
      # see: https://www.elastic.co/guide/en/elasticsearch/reference/current/bootstrap-checks.html
      discovery.type: single-node
    networks:
      - elk

  logstash:
    build:
      context: logstash/
      args:
        ELK_VERSION: $ELK_VERSION
    volumes:
      - type: bind
        source: ./logstash/config/logstash.yml
        target: /usr/share/logstash/config/logstash.yml
        read_only: true
      - type: bind
        source: ./logstash/pipeline
        target: /usr/share/logstash/pipeline
        read_only: true
    ports:
      - "5044:5044"
      - "5000:5000/tcp"
      - "5000:5000/udp"
      - "9600:9600"
    environment:
      LS_JAVA_OPTS: "-Xmx256m -Xms256m"
    networks:
      - elk
    depends_on:
      - elasticsearch

  kibana:
    build:
      context: kibana/
      args:
        ELK_VERSION: $ELK_VERSION
    volumes:
      - type: bind
        source: ./kibana/config/kibana.yml
        target: /usr/share/kibana/config/kibana.yml
        read_only: true
    ports:
      - "5601:5601"
    networks:
      - elk
    depends_on:
      - elasticsearch

networks:
  elk:
    driver: bridge

volumes:
  elasticsearch:
```

</p>
</details>      

#### AFTER

```yaml
version: '3.2'

services:
  elasticsearch:
    container_name: elasticsearch
    build:
      context: elasticsearch/
      args:
        ELK_VERSION: $ELK_VERSION
    volumes:
      - type: bind
        source: ./elasticsearch/config/elasticsearch.yml
        target: /usr/share/elasticsearch/config/elasticsearch.yml
        read_only: true
      - type: volume
        source: elasticsearch
        target: /usr/share/elasticsearch/data
    ports:
      - "9200:9200"
      - "9300:9300"
    environment:
      ES_JAVA_OPTS: "-Xmx1024m -Xms1024m"
      ELASTIC_PASSWORD: 비밀번호
      # Use single node discovery in order to disable production mode and avoid bootstrap checks.
      # see: https://www.elastic.co/guide/en/elasticsearch/reference/current/bootstrap-checks.html
      discovery.type: single-node
    networks:
      - elk

  logstash:
    container_name: logstash
    build:
      context: logstash/
      args:
        ELK_VERSION: $ELK_VERSION
    volumes:
      - type: bind
        source: ./logstash/config/logstash.yml
        target: /usr/share/logstash/config/logstash.yml
        read_only: true
      - type: bind
        source: ./logstash/pipeline
        target: /usr/share/logstash/pipeline
        read_only: true
    ports:
      - "5044:5044"
      - "5000:5000/tcp"
      - "5000:5000/udp"
      - "9600:9600"
    environment:
      LS_JAVA_OPTS: "-Xmx1024m -Xms1024m"
    networks:
      - elk
    depends_on:
      - elasticsearch

  kibana:
    container_name: kibana
    build:
      context: kibana/
      args:
        ELK_VERSION: $ELK_VERSION
    volumes:
      - type: bind
        source: ./kibana/config/kibana.yml
        target: /usr/share/kibana/config/kibana.yml
        read_only: true
    ports:
      - "5601:5601"
    networks:
      - elk
    depends_on:
      - elasticsearch

networks:
  elk:
    driver: bridge

volumes:
  elasticsearch:
```

변경사항)  `services:  elasticsearch:  environment:`에서 elasticsearch 패스워드(`ELASTIC_PASSWORD:`) 변경  
변경사항)  `elasticsearch:`, `logstash:`의 `LS/ES_JAVA_OPTS:` 부분을 256 -> 1024 변경 (용량의 한도를 늘림)  
<br>

### 7) `docker-elk/docker-stack.yml` 파일

<details><summary><b>BEFORE</b></summary>
<p>

```yaml
version: '3.3'

services:

  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.15.0
    ports:
      - "9200:9200"
      - "9300:9300"
    configs:
      - source: elastic_config
        target: /usr/share/elasticsearch/config/elasticsearch.yml
    environment:
      ES_JAVA_OPTS: "-Xmx256m -Xms256m"
      ELASTIC_PASSWORD: changeme
      # Use single node discovery in order to disable production mode and avoid bootstrap checks.
      # see: https://www.elastic.co/guide/en/elasticsearch/reference/current/bootstrap-checks.html
      discovery.type: single-node
      # Force publishing on the 'elk' overlay.
      network.publish_host: _eth0_
    networks:
      - elk
    deploy:
      mode: replicated
      replicas: 1

  logstash:
    image: docker.elastic.co/logstash/logstash:7.15.0
    ports:
      - "5044:5044"
      - "5000:5000"
      - "9600:9600"
    configs:
      - source: logstash_config
        target: /usr/share/logstash/config/logstash.yml
      - source: logstash_pipeline
        target: /usr/share/logstash/pipeline/logstash.conf
    environment:
      LS_JAVA_OPTS: "-Xmx256m -Xms256m"
    networks:
      - elk
    deploy:
      mode: replicated
      replicas: 1

  kibana:
    image: docker.elastic.co/kibana/kibana:7.15.0
    ports:
      - "5601:5601"
    configs:
      - source: kibana_config
        target: /usr/share/kibana/config/kibana.yml
    networks:
      - elk
    deploy:
      mode: replicated
      replicas: 1

configs:

  elastic_config:
    file: ./elasticsearch/config/elasticsearch.yml
  logstash_config:
    file: ./logstash/config/logstash.yml
  logstash_pipeline:
    file: ./logstash/pipeline/logstash.conf
  kibana_config:
    file: ./kibana/config/kibana.yml

networks:
  elk:
    driver: overlay
```

</p>
</details>      

#### AFTER

```yaml
version: '3.3'

services:

  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.15.0
    ports:
      - "9200:9200"
      - "9300:9300"
    configs:
      - source: elastic_config
        target: /usr/share/elasticsearch/config/elasticsearch.yml
    environment:
      ES_JAVA_OPTS: "-Xmx1024m -Xms1024m"
      ELASTIC_PASSWORD: 비밀번호
      # Use single node discovery in order to disable production mode and avoid bootstrap checks.
      # see: https://www.elastic.co/guide/en/elasticsearch/reference/current/bootstrap-checks.html
      discovery.type: single-node
      # Force publishing on the 'elk' overlay.
      network.publish_host: _eth0_
    networks:
      - elk
    deploy:
      mode: replicated
      replicas: 1

  logstash:
    image: docker.elastic.co/logstash/logstash:7.15.0
    ports:
      - "5044:5044"
      - "5000:5000"
      - "9600:9600"
    configs:
      - source: logstash_config
        target: /usr/share/logstash/config/logstash.yml
      - source: logstash_pipeline
        target: /usr/share/logstash/pipeline/logstash.conf
    environment:
      LS_JAVA_OPTS: "-Xmx1024m -Xms1024m"
    networks:
      - elk
    deploy:
      mode: replicated
      replicas: 1

  kibana:
    image: docker.elastic.co/kibana/kibana:7.15.0
    ports:
      - "5601:5601"
    configs:
      - source: kibana_config
        target: /usr/share/kibana/config/kibana.yml
    networks:
      - elk
    deploy:
      mode: replicated
      replicas: 1

configs:

  elastic_config:
    file: ./elasticsearch/config/elasticsearch.yml
  logstash_config:
    file: ./logstash/config/logstash.yml
  logstash_pipeline:
    file: ./logstash/pipeline/logstash.conf
  kibana_config:
    file: ./kibana/config/kibana.yml

networks:
  elk:
    driver: overlay
```

변경사항)  `services:  elasticsearch:  environment:`에서 elasticsearch 패스워드(`ELASTIC_PASSWORD:`) 변경  
변경사항)  `elasticsearch:`, `logstash:`의 `LS/ES_JAVA_OPTS:` 부분을 256 -> 1024 변경 (용량의 한도를 늘림)  
<br>

## 4. Docker로 실행하기
이제 클론 받은 폴더 `docker-elk` 로 이동해서 아래 명령어를 입력해준다.  

```bash
docker-compose build && docker-compose up -d
```  

**만약 에러가 난다면 다음 명령어들을 통해 ELK 각각의 이미지들을 다운받고 다시 실행해본다.**  

```bash
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.15.0
docker pull docker.elastic.co/kibana/kibana:7.15.0
docker pull docker.elastic.co/logstash/logstash:7.15.0
```  

![image](https://user-images.githubusercontent.com/90135804/148461723-eacab81b-af88-42bd-a9aa-6804ee61ecb6.png)


