# Elasticsearch docker container 설치 가이드


# 1. cluster branch에서 pull 받기

아래 명령어를 통해 repository sofu qkerl
```
git clone -b cluster --single-branch git@github.com:cucuridas/elasticsearch_repo.git
```



# 2. docker-engine 이 켜져있는 상태로 docker continaer run

- docker engine이 켜져있는 상태.
    
    ![https://user-images.githubusercontent.com/65060314/170930063-c2c87fc1-e36c-4640-989e-75012abce725.png](https://user-images.githubusercontent.com/65060314/170930063-c2c87fc1-e36c-4640-989e-75012abce725.png)
    
- vscode에서 "Ctrl + shift + ~" 를 눌러 Comand 창에서 해당 명령어 실행

```bash
docker-compose -f integration-log-compose.yml up -d

```

# 3. 최초 컨테이너 생성 후 노드끼리의 보안 통신을 위한 auth 계정 생성 및 비밀번호 설정

- 모든 container 생성이 완료 된 후 진행

> integration-log-create_certs-1 경우 인증서 생성 후 종료되는 것이 정상입니다
> 

![https://user-images.githubusercontent.com/65060314/170930803-5cf07f4f-abb6-4f75-a932-dfd273f2cf36.png](https://user-images.githubusercontent.com/65060314/170930803-5cf07f4f-abb6-4f75-a932-dfd273f2cf36.png)

## 3-1 cluster 내부 노드 비밀번호 사용자 지정 설정

- 'esmaster01' 컨테이너 내부로 접속

```bash
docker exec -it esmaster01 /bin/bash
```

- 디렉토리 이동 후 비밀번호 설정 명령어 실행

```bash
cd /usr/share/elasticsearch/
./bin/elasticsearch-setup-passwords interactive --url https://localhost:9350

```

> 
> 

## 3-2. cluster 내부 노드 비밀번호 난수로 설정

- 해당 명령어를 vscode Comand 창에서 실행

```bash
docker exec esmaster01 /bin/bash -c "bin/elasticsearch-setup-passwords \\
auto --batch --url https://localhost:9350"

```

- 생성되는 비밀번호는 따로 메모장에 관리 필요
    
    ![https://user-images.githubusercontent.com/65060314/170931448-99b487af-b15e-4062-8631-33cc642f3afa.png](https://user-images.githubusercontent.com/65060314/170931448-99b487af-b15e-4062-8631-33cc642f3afa.png)
    

# 4. 생성한 비밀번호 yml 파일에 추가

- 'integration-log-compose.yml'파일의 service: kibana enviroment 설정 값 변경
    
    ![https://user-images.githubusercontent.com/65060314/170932552-946654e6-1fa0-43af-92c4-3fda6efaeded.png](https://user-images.githubusercontent.com/65060314/170932552-946654e6-1fa0-43af-92c4-3fda6efaeded.png)
    
- 'integration-log-compose.yml'파일의 service: logstash enviroment 설정 값 변경
    
    ![https://user-images.githubusercontent.com/65060314/170932426-41410046-352d-4d3b-bd16-4b98d1dbd603.png](https://user-images.githubusercontent.com/65060314/170932426-41410046-352d-4d3b-bd16-4b98d1dbd603.png)
    
- 'logstash/logstash.conf' 파일의 'output' Password 값을 변경
    
    ![https://user-images.githubusercontent.com/65060314/170932867-19c223cd-66bc-4c15-a918-cb20be29e6f2.png](https://user-images.githubusercontent.com/65060314/170932867-19c223cd-66bc-4c15-a918-cb20be29e6f2.png)
    

# 5. docker container 재실행

- Vscode command 창에서 해당 명령어를 통해 수정된 부분만 recreate 진행

```bash
docker-compose -f integration-log-compose.yml up -d
```

![https://user-images.githubusercontent.com/65060314/170933610-7ca72f5a-8687-4b3e-aad2-6caa3d76887c.png](https://user-images.githubusercontent.com/65060314/170933610-7ca72f5a-8687-4b3e-aad2-6caa3d76887c.png)

# 6. 접속 여부 확인

- [https://localhost:5601](https://localhost:5601/) 접속 후 auth 정보를 "elastic" 으로 넣어 접속.
    
    ![https://user-images.githubusercontent.com/65060314/170934062-0aa03d13-0845-4f02-8fc8-aabe2f501e94.png](https://user-images.githubusercontent.com/65060314/170934062-0aa03d13-0845-4f02-8fc8-aabe2f501e94.png)
    
- docker desktop 에서 'log001' container 클릭 또는 docker logs를 통해 아래와 같은 이미지 출력
    
    ![https://user-images.githubusercontent.com/65060314/170934351-ad0f881b-eb8b-4c4e-85ee-24e96ecda1c2.png](https://user-images.githubusercontent.com/65060314/170934351-ad0f881b-eb8b-4c4e-85ee-24e96ecda1c2.png)
    

```bash
#'log001' container의 log를 확인하는 명령어
docker logs log001
```

![https://user-images.githubusercontent.com/65060314/170934661-c99384fa-aaab-4e8f-9983-85dbe167792a.png](https://user-images.githubusercontent.com/65060314/170934661-c99384fa-aaab-4e8f-9983-85dbe167792a.png)

- elasticsearch node 확인 - 아래의 주소에서 클러스터 내에 노드 갯수 확인 *auth 계정 정보는 'elastic'정보를 사용하시면 됩니다
- [https://localhost:9350/_nodes/stats?pretty](https://localhost:9350/_nodes/stats?pretty)
    
    ![https://user-images.githubusercontent.com/65060314/170935108-f5806bb2-da2c-4c81-84bc-4358547bc34c.png](https://user-images.githubusercontent.com/65060314/170935108-f5806bb2-da2c-4c81-84bc-4358547bc34c.png)
