# Docker 사용법

## 1. Docker Container WAS 로그를 보려면?
EC2 인스턴스에서 Docker 컨테이너를 사용하여 WAS를 실행할 경우 로그 보는 방법
``` shell
docker logs {Container ID or Container name}
```

로그 파일을 직접 확인할 경우 : 컨테이너와 Host 간 볼륨 마운트 필요