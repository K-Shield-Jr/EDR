메트릭비트 대시보드(수정중)

Analytics > Dashboard


드롭박스 All types > Aggregation based > Goal 선택

![image](https://user-images.githubusercontent.com/51702223/152738602-dd994022-f8c1-4def-996b-036c6857e81c.png)



index pattern 생성했던 metricbeat 선택
![image](https://user-images.githubusercontent.com/51702223/152738919-951f1893-c603-497e-8c7c-a6209ea2bf45.png)


![image](https://user-images.githubusercontent.com/51702223/152740397-803c26a7-e97b-443a-8a7c-a582a64fc39d.png)

![image](https://user-images.githubusercontent.com/51702223/152846151-4a7381d7-135a-42e5-b302-e7128b3c60d1.png)


.블로그참고
system.memory.actual.used.pct 항목이 어떤건지 공식문서에서 확인하셔야 합니다.  공식 문서에 따르면 사용된 메모리를 퍼센트로 표현한 값입니다.

Options 탭으로 가셔서 Percentage mode를 선택하고, Ranges 의 값을 아래와 같이 변경합니다(퍼센트로 시각화하기 위해서). 그리고 Update를 누르세요

process.pid
![image](https://user-images.githubusercontent.com/51702223/152849333-cd872b8f-daac-4e3b-8df8-5d04785c1f3a.png)

container.id
![image](https://user-images.githubusercontent.com/51702223/152849532-c669f6c8-f0cc-4adf-8416-970f60db2f91.png)

필드명 검색 참고
https://www.elastic.co/guide/en/beats/metricbeat/current/exported-fields-system.html
