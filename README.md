# Health Check Lab

## Setup
```bash
docker compose up -d
```

## Verify Service
```bash
curl localhost:3000/orders
```

## Verify Health
`/health` only checks that the API process is alive. `/ready` checks that
PostgreSQL is reachable.

```bash
curl -i localhost:3000/health
curl -i localhost:3000/ready
docker compose ps
docker inspect --format '{{json .State.Health}}' $(docker compose ps -q orders-api)
```

## Stop Database
```bash
docker compose stop db
```

## Observe Readiness and Recovery
```bash
curl -i localhost:3000/ready
docker compose ps
docker compose start db
docker compose ps
curl -i localhost:3000/ready
```

The API healthcheck polls `/ready`, so `docker compose ps` reports
`(healthy)` when the database is reachable and `(unhealthy)` after the
database is stopped. The `unless-stopped` restart policy restarts the API
if its process exits; Docker does not restart a running container solely
because its healthcheck becomes unhealthy.
