# docker-postgres


docker run  -d --name pms-postgres \
    -e  POSTGRES_USER=postgres \
    -e  POSTGRES_PASSWORD=postgres \
    -e  POSTGRES_DB=pms  \
    -v  ~/ws/ws-postgres/data:/var/lib/postgresql/data \
    -p  5432:5432 \
    postgres:15.2-alpine
    


## Performance Tuning PostgreSQL Containers in a Docker Environment
https://pankajconnect.medium.com/performance-tuning-postgresql-containers-in-a-docker-environment-89ca7090e072


