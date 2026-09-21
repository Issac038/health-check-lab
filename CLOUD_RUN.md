# Cloud Run health checks

The local checks map to Cloud Run as follows:

- `GET /health` is the liveness-style check. It only confirms that the
  process can accept HTTP requests and does not depend on PostgreSQL.
- `GET /ready` is the readiness-style check. It runs `SELECT 1` through the
  PostgreSQL connection pool and returns `503` when the database is
  unreachable.
- In Cloud Run, configure a startup probe for `/health` so traffic is not
  sent before the process is listening. A liveness probe can also use
  `/health` to restart an instance whose process is no longer serving.
- Cloud Run does not provide a Kubernetes-style readiness probe that removes
  an individual instance from service based on an arbitrary dependency
  response. Use `/ready` in deployment or load-balancer checks only where
  that integration is available, and handle database outages with the
  application's normal error handling and retry strategy.

No Cloud Run deployment is required for this lab. Locally, the Compose
healthcheck polls `/ready`, which makes database loss visible as
`(unhealthy)` while `/health` remains available as a process liveness check.
