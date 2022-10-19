# Elasticsearch 8.3.3(docker container)

# Topology

![https://user-images.githubusercontent.com/65060314/183786844-2ad9d81d-0c1c-46e9-a88c-1bb3cdbd738a.png](https://user-images.githubusercontent.com/65060314/183786844-2ad9d81d-0c1c-46e9-a88c-1bb3cdbd738a.png)

- 환경 내용

OS : Ubuntu 20.04, docker container 기반(docker-compose)

Network : docker network 환경을 통한 노드 구성

Volume : 호스트 VM의 volume mount

- Elasticsearch  설정 내용

> Master node 와 Data node의 설정은 node.role 제외하고 동일하게 설정 됩니다 DataPath등 과 같은 설정은 Docker 설정을 통해 진행 됩니다
> 

- 클러스터 설정 :
    - 클러스터 이름( [cluster.name](http://cluster.name) )
    - 노드 이름( [node.name](http://node.name) )
    - 노드 역할( node.role )
- 네트워크 설정 :
    - HTTP port( http.port )
- path 설정
    - Cluster 백업 시 사용하게 될 data path( path.repo )
- Node Discovery 설정
    - master node 후보 지정 ( cluster.inital_master_nodes )
    - cluster에 추가 시킬 노드 Host IP ( discovery.seed_hosts )
- 보안설정
    - 보안 설정 유무 ( xpack.security.enabled )
    - 라이센스 발급시 종류 설정 ( xpack.license.self_generated.type )
    - HTTP SSL 설정
        - https 설정 유무 ( xpack.security.http.ssl.enabled )
        - https 접속을 위해 사용 할 인증서 key path ( xpack.security.http.ssl.key )
        - https 통신 시 사용하게 될 ca인증서 파일의 Path ( xpack.security.http.ssl.certificate_authorities )
        - https 통신 사 사용하게 될 crt 인증서 파일 path 값 ( xpack.security.http.ssl.certificate )
    - Transport SSL 설정 [ TLS 설정 ]
        - TLS 설정 유무 ( xpack.security.transport.ssl.enabled )
        - TLS 통신 시 설정 옵션 (certificate, none 등) ( xpack.security.transport.ssl.verification_mode=certificate )
        - TLS 통신 시 사용하게 될 CA 인증서 파일의 path 값 ( xpack.security.transport.ssl.certificate_authorities )
        - TLS 통신 시 사용하게 될 crt 인증서 파일 path 값 ( xpack.security.transport.ssl.certificate )
        - TLS 통신 시 사용하게 될 key 인증서 파일 path 값 ( xpack.security.transport.ssl.key )
        
- Kibana 설정
    - network 설정
        - 도메인 설정 ( SERVERNAME )
    - Elasticsearch connection 설정
        - 연결하고자 하는 Elasticsearch master node 주소 ( ELASTICSEARCH_URL, ELASTICSEARCH_HOSTS )
        - Elasticsearch 로 접근할때 사용할 auth 계정 정보 ( ELASTICSEARCH_USERNAME )
        - 비밀번호의 경우 보안설정 프로세스에 따라 진행하여 해당 값을 채워주어야 함 ( ELASTICSEARCH_PASSWORD )
    - Kibana HTTPS 통신을 위한 설정
        - HTTPS 통신 설정 여부 ( SERVER_SSL_ENABLED )
        - HTTPS 통신 설정을 위한 CA 인증서 path 값 ( ELASTICSEARCH_SSL_CERTIFICATEAUTHORITIES: $CERTS_DIR/ca/ca.crt )
        - HTTPS 통신 설정을 위한 key path 값 ( SERVER_SSL_KEY )
        - HTTPS 통신 설정을 위한 crt 파일 path 값 ( SERVER_SSL_CERTIFICATE )
        - 보안 인증서 관련 디렉토리 ( CERTS_DIR )
    
- Logstash 설정

> Logstash 설정은 ‘서비스 자체에 대한 설정’ 과  ‘Pipeline’ 내용 관련 설정으로 나누어 집니다 ‘Pipeline’ 내용 관련 설정은 Logstash 가 데이터에 대한 전처리를 어떻게 진행 할 것인지에 대한 내용입니다
> 

- 모니터링 설정
    - 모니터링 설정 여부 ( xpack.monitoring.enabled )
    - 모니터링 정보를 전달할 Master Node의 주소 ( xpack.monitoring.elasticsearch.host )
    - 모니터링 정보를 elasticsearch 전달할 때 사용하게 될 elasticsearch 계정 ( xpack.monitoring.elasticsearch.username )
    - 모니터링 정보를 elasticsearch 전달할 때 사용하게 될 elasticsearch 패스워드 ( xpack.monitoring.elasticsearch.password )
    - elasticsearch 통신 시 사용할 crt 파일 path 값 ( xpack.monitoring.elasticsearch.ssl.certificate.authority )
- Pipeline 내용
    - input : filebeat 또는 수집기 agent 가 보내주는 호스트 IP:PORT를 값으로 받게 됩니다
    
    ![https://user-images.githubusercontent.com/65060314/183791398-a4e40bbc-b7d2-48d1-b850-8e4e392edf7a.png](https://user-images.githubusercontent.com/65060314/183791398-a4e40bbc-b7d2-48d1-b850-8e4e392edf7a.png)
    
    - fileter : ‘input’ 을 통해 전달 받은 데이터를 전처리하는 과정을 거칩니다 (해당 전처리 작업은 정규식을 통해 이루어집니다)
    
    ![https://user-images.githubusercontent.com/65060314/183791696-6b7eb991-9c7c-4b40-91b4-b74fb3b15de2.png](https://user-images.githubusercontent.com/65060314/183791696-6b7eb991-9c7c-4b40-91b4-b74fb3b15de2.png)
    
    - output : 모든 작업이 끝난 데이터를 적재하는 App으로 전달합니다
        - elasticsearch 전달 관련 설정
            - elasticsearch host 정보 : ( hosts )
            - 적재할 index의 이름 : ( index )
            - 전달할 때 사용하게 될 elasticsearch username ( user )
            - 전달할 때 사용하게 될 elasticsearch password ( password )
            - cacert 위치 정보 ( cacert )

![https://user-images.githubusercontent.com/65060314/183791870-3a7e654b-d722-4f98-b014-229f531fc099.png](https://user-images.githubusercontent.com/65060314/183791870-3a7e654b-d722-4f98-b014-229f531fc099.png)

- Docker 관련 설정 내용

> Docker volume 마운트 설정, Docker network 설정 , Memory lock 설정, Port binding 설정 등으로 이루어 집니다
> 

- Docker Network 설정
    
    docker network bridge 설정을 통해 내부 컨테이너끼리의 통신 설정 ( networks ) 
    
    ```yaml
    networks:
      eknetwork:
        #network이름
        name: test-eknetwork 
        #container 내부끼리의 통신을 지원여부에 대한 설정
        driver_opts:
          com.docker.network.bridge.enable_icc: "true"
    ```
    
    service에 네트워크 할당 시 (해당 구문 추가)
    
    ```yaml
    networks:
    - eknetwork
    ```
    

- Container memory limit 설정
    
    > ulimit 설정을 통해 Memory limit를 설정 해당 설정을 진행해야만 swap memory 미사용( bootstrap.memory_lock ) 설정을 할 수 있음
    > 
    
    ```yaml
    ulimits:
          memlock:
            soft: -1
            hard: -1
    ```
    
- Docker volume mount 설정
    - Elasticsearch Master Node
        
        > Master Node는 Data path가 필요하지 않으며 log 관련 path도 필요하지 않기에 Config관련 설정을 위해서 docker volume 을 통해 가상 volume을 마운트 하였습니다
        > 
        - docker volume 생성
        
        ```yaml
        volumes:
          #master node config에 대한 정보가 mount
          masterConfig01:
            driver: local
        ```
        
        - docker volume mount 설정
        
        ```yaml
        service:
        ...
        ...
        ...
        	volumes: 
        	      - masterConfig01:/usr/share/elasticsearch/config
        				# cert volume 마운트
        	      - certs:$CERTS_DIR
        ```
        
    
    - Elasticsearch Data Node
        
        > Data Node는 Data path와 Logs path에 대해서 필요하며 물리적인 volume으로 마운트 되어야 함으로 docker 가상 volume이 아닌 Host PC Volume을 바인딩 하도록 설정 하였습니다 
        ‘주의사항’ : 해당 path에 대한 권한이 없거나 해당 path에 directory가 존재하지 않을 경우 container service가 다운됩니다
        > 
        - Host volume binding
        
        ```yaml
        - type: bind
                source: ./data_node1/data #호스트 파일시스템 path 값
                target: /usr/share/elasticsearch/data #conatiner 내부 path 값       
              - type: bind
                source: ./data_node1/logs #호스트 파일시스템 path 값
                target: /usr/share/elasticsearch/logs #conatiner 내부 path 값
        ```
        
    
    - Kiabana
        
        > 설정관련 파일에대해서 관리하기 위해 docker 가상 volume을 마운트 하였습니다
        > 
        - volume 생성
        
        ```yaml
        volumes:
        	kibana:
            driver: local
        ```
        
        - volume 마운트
        
        ```yaml
        service:
        ...
        ...
        ...
        	volumes:
        	      - kibana:/usr/share/kib
        	      - certs:$CERTS_DIR
        ```
        
    
    - Logstash
    
    > 설정관련 파일에대해서 관리하기 위해 docker 가상 volume을 마운트 하였습니다
    > 
    - volume 생성
    
    ```yaml
    volumes:
    	logstash:
        driver: local
    ```
    
    - volume 마운트
    
    ```yaml
    service:
    ...
    ...
    ...
    	volumes:
    	      - logstash:/usr/share/logstash
    	      - certs:$CERTS_DIR
    ```
    

- Docker port binding 설정
    - Elasticsearch Master Node
        - master node 1
            
            ```yaml
            service:
            ...
            ...
            	ports:
            	      - 9350:9350
            ```
            
        - master node 2
            
            ```yaml
            service:
            ...
            ...
            	ports:
            	      - 9360:9360
            ```
            
    
    - Elasticsearch Data Node
        - Data node 2
            
            ```yaml
            service:
            ...
            ...
            	ports:
            	      - 9550:9550
            ```
            
        - Data node 2
            
            ```yaml
            service:
            ...
            ...
            	ports:
            	      - 9560:9560
            ```
            
        - Data node 2
            
            ```yaml
            service:
            ...
            ...
            	ports:
            	      - 9570:9
            ```
            
    
    - Kiabana
        
        ```yaml
        service:
        ...
        ...
        	ports:
              - 5601:5601
        ```
        
    
    - Logstash
        
        > Logstash는 시스템 운영을 위한 port와 Data 전달 받기 위한 port가 따로 구성되어져 있습니다 이에 따라 두개의 포트를 바인딩해주었습니다
        > 
        
        ```yaml
        service:
        ...
        ...
        	ports:
              - "5100:5100" # 'beats'를 통해 데이터를 수집하기 위한 포트
              - "9600:9600" # logstash continaer로 접속하기 위한 포트
        ```
        
    

[Elasticsearch 설정 및 관리 가이드](https://www.notion.so/Elasticsearch-5de8b5b8b70d403f8dd450c60fd83a84)

[Elasticsearch docker container 설치 가이드](https://www.notion.so/Elasticsearch-docker-container-562df422966846d89bdb77b812063a71)
