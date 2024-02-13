# docker-postgres

```
docker volume create --driver local \
 --name postgres_data \
 --opt type=none \
 --opt device=/home/hms/data/postgres_data \
 --opt o=bind,uid=70
```
    
```
docker run  -d --name pms-postgres \
    -e  POSTGRES_USER=postgres \
    -e  POSTGRES_PASSWORD=postgres \
    -e  POSTGRES_DB=pms  \
    -v  postgres_data:/var/lib/postgresql/data \
    -p  5432:5432 \
    postgres:15.2-alpine
    

```

 

## timescaledb

```
 docker run -d --name timescaledb -p 5432:5432 -v postgres_data:/var/lib/postgresql/data -e POSTGRES_PASSWORD=password timescale/timescaledb:latest-pg14

```

```
docker exec -it timescaledb /bin/bash
psql -U postgres -h localhost
CREATE database example;
\c example
\dx
```

```
docker inspect volume postgres_data

```

```
findmnt

```

```
sudo blkid

```


## Performance Tuning PostgreSQL Containers in a Docker Environment
https://pankajconnect.medium.com/performance-tuning-postgresql-containers-in-a-docker-environment-89ca7090e072


## postgresql.conf

PostgreSQL의 환경변수를 지정해주는 설정파일. 오라클의 파라미터 파일과 비슷한 역할을 합니다.
postgres.conf 파일 안에는 다양하고 많은 설정 값들이 있는데, 아래의 설정 값 정도만 설정하면 싱글 DB를 운영하는데 있어 크게 문제될 사항은 없습니다.

- listen_addresses = ‘*’ # 로컬 호스트 밖에서의 접속 허용
- shared_buffers = 3GB # 물리 메모리 2/3 ~ 1/4
- checkpoint_segments = 128 # 2GB redo 로그, 9.4 이하에서
- max_wal_size = 2GB # 2GB redo 로그, 9.5 이상에서
- min_wal_size = 2GB # 2GB redo 로그, 9.5 이상에서
- wal_level = logical # 일단 최대 자세하게
- archive_mode = on # 아카이빙 기능은 켜두고,
- archive_command = ‘true’ # 아카이빙을 임시로 사용 안함
- log_destination = ‘stderr’ # pg_log 에 로그 남김
- logging_collector = on # 자체 로그 프로세스 사용
- log_line_prefix = ‘%t %u@%r/%d(%c 또는 %p)’ # 좀 더 자세히
- stats_temp_directory = ‘/run/shm’ # 실시간 통계 정보는 공유 메모리로
- effective_cache_size = 4GB # 물리 메모리 1/2  (9.4 이하)
 
