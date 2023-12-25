# Docker Fix 레퍼런스

## 1. WAS 컨테이너와 Redis 컨테이너를 연결하면서 겪은 이슈
<hr>
WAS(Spring Boot)와 Redis 컨테이너 사이에 Connection이 안돼서 해결하느라 애를 먹었다. 결론부터 얘기하자면, application.yml의 spring.redis.host에 Redis 컨테이너의 이름을 넣어줘야 WAS에서 Redis 컨테이너를 정상적으로 인식한다.

나는 host를 localhost -> drcal-network(Custom create한 네트워크 이름) -> Connection 문제 아닌 줄 알고 Redis 호출 메서드에 StackTrace 추가 -> test-redis(Redis 컨테이너 이름) 과정을 거쳐서 해결했다.
``` java
# application.yml

spring:
  redis:
    host: {Redis Container의 name}
```

이슈를 해결할 때는 test-network로 도커 내부 네트워크를 create 했다. 이때, default network로 네트워크를 설정하고, redis의 host를 맞춰주면 잘 동작할까 하는 의문이 생겼다. 그래서 이번엔 network 파라미터와 옵션을 따로 주지 않고 WAS와 Redis를 다시 실행해 봤다.

--network 옵션을 주지 않으니 다시 500 에러(WAS & Redis Connection 에러)가 발생한다. 왜 그런지 찾아봤다.

Docker의 기본 네트워크를 사용할 때, 컨테이너 간의 연결이 자동으로 설정되는 것은 아니다. GitHub Actions에서 Docker 컨테이너를 실행할 때 네트워크 옵션을 명시적으로 지정하지 않으면, 각 컨테이너는 독립적으로 기본 네트워크(대개는 bridge 네트워크)에 연결된다.
이 경우, 컨테이너 간의 이름 해석이나 네트워크 연결이 자동으로 구성되지 않는다. WAS 컨테이너가 Redis 컨테이너의 이름을 통해 Redis 서버에 접근하는 것이 실패할 수 있다.

이 문제를 해결하기 위해 처음처럼 1. 커스텀 네트워크를 사용하거나, 2. 호스트(EC2)의 네트워크를 사용할 수 있다.
하지만 2번은 호스트의 네트워크 스택에 직접 접근하기 때문에 보안상 위험할 수 있다.

아래는 Github Actions 스크립트의 일부를 가져온 것인데, 이렇게 Custom network create 한 다음, docker run 명령어에서 --network 옵션을 통해 Custom network를 지정하면 된다.

``` shell
- name: check and create network
      run: |
        if ! sudo docker network ls | grep -q drcal-network; then
          echo "no network found | create new docker network"
          sudo docker network create drcal-network
        else
          echo "drcal-network already exists"
        fi
```

## 2. EC2 인스턴스에서 sudo 커맨드와 Docker Hub username 환경변수와의 관련성
<hr>
Github Actions통해 CI/CD를 적용하던 중, 쉘 스크립트에 sudo 커맨드를 사용할 경우 Secrets에 등록된 환경변수를 인식하지 못하는 이슈가 발생했다.

이를 해결하기 위해선 ec2 유저(ubuntu)가 root 권한 없이도 docker 커맨드를 쓸 수 있도록 권한을 수정해 줘야 한다.

``` shell
srw-rw----  1 root    docker     0 Nov  8 14:44 docker.sock
```

위의 정보를 보면, docker.sock 소켓의 소유자는 root, 그룹은 docker 이다. 이때, 이 소켓 파일의 위치는 /var/run/ 경로에 있다. 제3자 유저(ubuntu)가 사용할 수 있도록 docker.sock 파일의 권한을 아래와 같이 수정했다:
``` shell
sudo chmod 666 ./docker.sock
```

권한 변경 후 sudo 없이 docker ps 커맨드를 날리니 정상 작동한다. 이후 Github Actions의 CI/CD 스크립트를 모두 sudo 커맨드를 빼도록 수정한 뒤 다시 테스트해 봤더니 파이프라인이 정상적으로 수행됐다.