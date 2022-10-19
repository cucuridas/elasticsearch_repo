##  **master node 구축**
    
    1) docker-compose up 명령어를 통해 master node container 배포
    
    cluster 구성의 시작은 최초 배포인 elasticsearch-master를 통해 모든 node_rule을 가진 노드를 하나 생성하여 enrollment-token을 생성할 수 있는 master역할을 가진 노드를 먼저 배포합니다

```bash
    docker-compose -f aws-elasticsearch-master up -d  
```
    
> container가 정상적으로 up 되지 않을 때에는 ‘docker logs {container 이름}’ 을 통해 error 내용을 확인하신 뒤 공식 홈페이지를 참조해서 trouble shooting 하시길 바랍니다
(docker를 통한 elasticsearch 구축[https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html) )
swap memory 제한등을 설정 error = vi /etc/sysctl.conf 파일에서 vm.max_map_count 값 설정 등
> 
    
    2) elasticsearch 비밀번호 설정

    해당 설정은 글쓴이는 docker exec를 통해 container 내부에서 설정하였습니다만….’auto’옵션을 통해 랜덤으로 생성하셔도 특정 user에 대한 비밀번호 설정만하셔도 무관합니다

- Elasticsearch master node container 접속

```bash
docker exec -it {master node container 이름} /bin/bash
```

- elasticsearch password 설정

```bash
./bin/elasticsearch-setup-passwords interactive
```
    
![Sep-19-2022 10-27-58](https://user-images.githubusercontent.com/65060314/196607426-13b33bf2-7a77-4c98-a352-7f754d565899.gif)
    
    3) elasticsearch enrollment-token 발급 
    
    추가적인 노드들이 master node와 같은 cluster에 구성될 수 있도록 token을 발급합니다 
    
> 생성된 token 잃어버려 다시 발급하게되면 기존 toekn을 통해 cluster에 구성된 모든 노드들이 인증서 관련 에러로 인해 연결이 끊기게 됨에 따라 주의가 필요합니다!
> 
    
```bash
#해당 명령어는 container 밖에서 실행합니다
docker exec -it {elasticsearch master node continaer 이름} /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s node
```
    
    - 발급된 토큰의 모습
    
![image](https://user-images.githubusercontent.com/65060314/196607563-ea8f5231-30c4-48de-b08e-30cb91c7ad20.png)
    
* 토큰정보는 따로 보관하여 추가되는 노드의 변수값으로서 활용됩니다