# Docker 사용법

## 1. Docker Container WAS 로그를 보려면?
EC2 인스턴스에서 Docker 컨테이너를 사용하여 WAS를 실행할 경우 로그 보는 방법
``` shell
docker logs {Container ID or Container name}
```

로그 파일을 직접 확인할 경우 : 컨테이너와 Host 간 볼륨 마운트 필요

## 2. Docker 컨테이너 콘솔 접속 방법
``` shell
docker exec -it {컨테이너 ID} /bin/bash
```

## 3. Docker run command 실행 방법 및 옵션
``` shell
docker run -d -v ~/mini/images:/images -p 8080:8080 -e TZ=Asia/Seoul {Docker hub username}/{Docker hub repository}:{version}

# -d : 백그라운드 컨테이너에서 실행
# -v : 볼륨 마운트 옵션 | Host의 ~/mini/images 경로에 Docker 컨테이너의 /images 경로를 마운트 하겠다는 것임
# -p : 포트 옵션 | {호스트 포트}:{도커 컨테이너 포트}
# -e TZ : Timezone 지정 옵션 | 여기서는 서울로 지정 | -e는 환경변수를 사용한다는 옵션임
```
