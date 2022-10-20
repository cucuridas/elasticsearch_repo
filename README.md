# docker를 통한 elasticsearch 배포를 위한 레파지토리입니다

## 배포 방식
    * cluster 형태로 container 배포
    * 노드별 배포 후 cluster 구성
<br/>

## 각 브랜치 설명
    * master : 노드별 개별 배포를 위한 elasticsearch master node 레파지토리입니다
    * subnode : 노드별 개별 배포를 위한 elasticsearch master node 레파지토리입니다 
    * cluster : cluster 형태의 묶음 배포를 위한 레파지토리 입니다
<br/>

##  배포를 위한 환경 구축 방법

1. **docker container 환경 구축**

해당 내용을 바탕이 되는 Docker container 환경을 구축합니다 또한 docker-compose File 작성되어진 구조이기 때문에 docker-compose의 추가적인 설치 역시 필요로 하며 해당 내용도 정리 되어있습니다!

1) 도커 다운로드 및 설치 

```bash
curl -fsSl https://get.docker.com/ |sudo sh
```

2) 현재 os user에 대해 docker 권한 추가

> 해당 내용은 글쓴이의 편리를 위한 설정입니다
> 

```bash
usermod -aG docker $USER
```

3)  docker-compose 설치 (v 1.29.2)

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

4) docker-compose 권한 적용

```bash
sudo chmod +x /usr/local/bin/docker-compose
```

5) 정상 설치 확인 (cli 환경 재접속 후 진행)

```bash
docker --version
docker-compose --version

#위의 명령어로 진행되지 않을 시 sudo를 통해 확인
```

6) 도커 로깅 설정 및 service 재시작

> 해당 설정 미진행 시 service를 제공하는 container의 로그가 방대해져 host vm에 자원을 소비하게 됩니다
> 

```bash
sudo vi /etc/docker/daemon.json
```

```json
{
	"log-driver":"json-file",
	"log-opts": {
		"max-size":"20m",
		"max-file": "5"
	}
}
```

```bash
#service 재시작
sudo systemctl daemon-reload
sudo systemctl restart docker
```
