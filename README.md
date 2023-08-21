# log-monitor

## install plugin loki
```docker plugin install grafana/loki-docker-driver:latest --alias loki```


## Using docker-compose
Initial setup or Dockerfile is changed, make sure to update the containers with `docker-compose up -d` after a successful build.

```
docker-compose build
```

To start the containers and view the logs. CTRL+C to stop the containers.

```
docker-compose up
```

Starting the containers in detach mode.

```
docker-compose up -d
```

To view containers logs, we can also specify service here.

```
docker-compose logs -f
```

To stop and remove the containers.

```
docker-compose down
```

To stop|start|restart containers, we can also specify service here. NOTE: restart will not update the container's image.

```
docker-compose stop|start|restart
```

## Example: in node project

```
version: "3.4"
services:
  app:
    container_name: beautibly-service
    restart: always
    healthcheck:
      test: ["CMD-SHELL", "curl --fail http://localhost:3000/health || exit 1"]
      interval: 30s
      timeout: 10s
      retries: 3
    build:
      context: .
      target: dev
    volumes:
      - ./src:/app/src
      - postgres:/var/lib/postgresql/data
    ports:
      - "8080:3000"
    depends_on:
      - db
    links:
      - db
    logging:
      driver: loki
      options:
        loki-url: ${LOKI_URL}
        loki-external-labels: job=dockerlogs,owner=beautibly-svc,environment=development
  db:
    container_name: pg-beautibly
    image: postgis/postgis:13-3.3-alpine
    ports:
      - "5433:5432"
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
      - POSTGRES_DB=beautibly
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  postgres:
  pgdata:
```

where,
LOKI_URL is the url of running loki. Example: http://localhost:3100/loki/api/v1/push


## Testing Example

Envionment: [Log Montioring Test.mp4](example/log-monitor.mp4)