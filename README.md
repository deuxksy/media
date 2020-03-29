# media

docker 를 이용한 media 서버 만들기
samba, torrent, dlna 3개를 docker-compose 를 이용해서 사용 하기

## docker 초기화

docker system prune --volumes

## [minidlna](https://github.com/vladgh/docker_base_images/tree/master/minidlna)

설치시 문재 발생

```bash
minidlna   | monitor.c:218: warn: WARNING: Inotify max_user_watches [8192] is low or close to the number of used watches [3] and I do not have permission to increase this limit.  Please do so manually by writing a higher value into /proc/sys/fs/inotify/max_user_watches.
```

파일 개수 문제로 인해서 해당 컨테이너 이미지로 sh 로 들어가서

```bash
docker exec -u 0 -it ${container_name_or_id} /bin/sh
```

아래 파일을 수정해준다

```bash
vi /etc/sysctl.d/00-alpine.conf

fs.inotify.max_user_watches=65536
```

## [samba](https://github.com/dperson/samba)

samba 사용자 생성 하면서 시작 하기
docker 단독 실행시 -p 로 시작 하는 인자값을 넘기는 것을 docker-compose 에서는 어떻게 하는지 확인 해서 아래와 같이 실행 해보자

```bash
docker run -it -p 139:139 -p 445:445 --name smb -d dperson/samba -p \
            -u "${user};{password}" \
            -s "example1 private share;/example1;yes;no;no;{user}"
```

## [torrent](https://github.com/mondediefr/docker-rutorrent)

로그인이 필수로 사용하기

```bash
docker exec -it rutorrent gen-http-passwd
Username: ${username}
Password: ${password}
Verifying - Password: ${password}
Password was generated for the http user: torrent
```

## 남은일

3개의 docker 를 단순 docker-compose 를 모았지만 이것를 실제 1개의 docker 파일로 만들어 보자

## 참조
