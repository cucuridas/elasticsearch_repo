1. **노드 추가**
    
    1) 생성되어진 token을 “ENROLLMENT_TOKEN” 의 값으로 입력해 줍니다
    
    (글쓴이는 .env파일에 명시하여 입력하였습니다)
    
    ![Untitled (1)](https://user-images.githubusercontent.com/65060314/196608310-fd2db15e-9656-4600-929d-e170e8be6bc5.png)

    2) docker-compose 명령어를 통해 node 배포
    
    master node 배포할 때와 마찬가지로 docker-compose up 명령어를 사용하여 배포하여줍니다
    
    ```bash
    docker exec -it {추가 node container 이름} /bin/bash
    ```
    
    > node가 정상적으로 cluster에서 구성되진 못할 때에는 방화벽에 transport가 오픈되어있는지를 확인하여야합니다 master node에 설정되어진 정보와 추가할 node의 port가 os 방화벽 혹은 물리적인 방화벽 때문에 통신이 정상적인지 여부를 꼭 확인하시길 바래요
    > 

1.  **cluster 정상 구동 확인**
    
    cluster node 정보 조회 API
    
    ```bash
    https://10.0.0.216:9360/_cat/nodes
    ```
    
    ![https://user-images.githubusercontent.com/65060314/190939361-f696cabc-59e8-482f-9536-212b5a29df28.png](https://user-images.githubusercontent.com/65060314/190939361-f696cabc-59e8-482f-9536-212b5a29df28.png)
    
    > **해당 API로 조회했을 때 node_name과 다른 이름으로 출력되는 점**
    cluster node 정보를 조회했을 때 나오는 이름은 config에 설정했던 node의 이름이 아닌 host pc(vm) host파일에 등록되어진 host name을 출력하게 됩니다 이는 docker network를 사용하지 않고 host로 네트워크를 잡았기 때문인데요 docker-network로 구축되어진 container는 namespace를 따라가게되어 container이름 과 동일한 이름으로 출력됩니다
    > 
    

## 3.배포시 수정해야할 부분

1. 같은 host pc(vm)에서 docker-compose 파일로 배포를 할 시  .env파일을 통해값을 참조하게 될 떄에 생기는 문제 : ‘**COMPOSE_PROJECT_NAME’**의 값을 변경없이 사용할 경우 같은 project로 판단하여 기존 프로젝트 위에 덮어씌워지기가 진행
2. token life cycle :
