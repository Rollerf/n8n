# n8n k3s Project

## Overview
This project provides Kubernetes manifests for running n8n on k3s. It includes a sample workflow and environment settings to facilitate deployment.

## Project Structure
```
n8n-docker-project
├── src
│   └── workflows
│       └── example-workflow.json
├── k8s
│   ├── *.yaml
│   ├── kustomization.yaml
│   └── n8n-config.env.example
├── README.md
```

## Getting Started

### Prerequisites
- Ensure you have k3s running and `kubectl` configured for your cluster.

### k3s Deployment
1. **Prepare local config files**
   Copy `k8s/n8n-config.env.example` to `k8s/n8n-config.env`, set `N8N_DOMAIN` (hostname only, no scheme), and adjust non-domain settings (leave the `DOMAIN` placeholders as-is). Copy `k8s/n8n-secret.example.yaml` to `k8s/n8n-secret.yaml` and set credentials plus the `N8N_ENCRYPTION_KEY`. Adjust PVC sizes in `k8s/n8n-pvc.yaml` and `k8s/postgres-pvc.yaml` if needed.

2. **Apply Manifests**
   ```bash
   kubectl apply -k k8s/
   ```

3. **Check Status**
   ```bash
   kubectl get pods
   kubectl get svc n8n
   ```

4. **Access n8n**
   On k3s, the `n8n` Service is `LoadBalancer` type, which exposes port `5678` on the node IP by default. If you use the Ingress, point the `N8N_DOMAIN` from `k8s/n8n-config.env` to your node IP (or add an `/etc/hosts` entry) and access `https://<domain>`.

### Uninstall (k3s)
Remove all resources created by this repo:
```bash
kubectl delete -k k8s/
```

If you also want to delete data volumes:
```bash
kubectl delete pvc n8n-data postgres-data
kubectl get pv
```

If you deployed into a dedicated namespace, delete it instead:
```bash
kubectl delete namespace <your-namespace>
```

### Data Directory Permissions & DB storage
In k3s, the `n8n` Deployment uses an init container to `chown` the PVC to UID/GID `1000`, and n8n also enforces safe permissions on the generated `config` file automatically via `N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true`.

PostgreSQL 18+ now stores its internal data under `/var/lib/postgresql/<major>`; the Postgres deployment mounts the PVC at `/var/lib/postgresql` (not `/var/lib/postgresql/data`) to follow that expectation. If you have existing data from an older setup, follow the upstream migration notes before switching volumes.

## Database Backup and Restore (k3s)
When applying a backup, scale down n8n first to avoid writes during the restore.

1. Scale n8n down:
   ```bash
   kubectl -n default scale deploy n8n --replicas=0
   ```
2. Find the Postgres pod and password:
   ```bash
   NS=default
   POD=$(kubectl -n "$NS" get pod -l app=n8n-postgres -o jsonpath='{.items[0].metadata.name}')
   DB_PASS=$(kubectl -n "$NS" get secret n8n-secrets -o jsonpath='{.data.DB_POSTGRESDB_PASSWORD}' | base64 -d)
   ```
3. Backup the database (save on your local machine):
   ```bash
   kubectl -n "$NS" exec -i "$POD" -- env PGPASSWORD="$DB_PASS" \
     pg_dump -U n8n_user n8n > n8n_backup.sql
   ```
4. Drop and recreate the database:
   ```bash
   kubectl -n "$NS" exec -i "$POD" -- env PGPASSWORD="$DB_PASS" \
     psql -U n8n_user -d postgres -v ON_ERROR_STOP=1 \
     -c "SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE datname='n8n';" \
     -c "DROP DATABASE IF EXISTS n8n;" \
     -c "CREATE DATABASE n8n;"
   ```
5. Restore the backup:
   ```bash
   kubectl -n "$NS" exec -i "$POD" -- env PGPASSWORD="$DB_PASS" \
     psql -U n8n_user -d n8n -v ON_ERROR_STOP=1 < /path/to/n8n_backup.sql
   ```
6. Scale n8n up:
   ```bash
   kubectl -n "$NS" scale deploy n8n --replicas=1
   ```

## Backup (Docker Compose)
For the Docker setup in `docker/docker-compose.yml`, back up the Postgres database, the n8n data directory, and your `.env` (especially `N8N_ENCRYPTION_KEY`).

1. Stop n8n to avoid writes during backup:
   ```bash
   docker compose -f docker/docker-compose.yml stop n8n
   ```
2. Dump the Postgres database:
   ```bash
   mkdir -p backups
   docker compose -f docker/docker-compose.yml exec -T db \
     sh -c 'PGPASSWORD="$POSTGRES_PASSWORD" pg_dump -U "$POSTGRES_USER" "$POSTGRES_DB"' \
     > backups/n8n_db_backup.sql
   ```
3. Archive the n8n data directory:
   ```bash
   tar -czf backups/n8n_data.tar.gz -C docker data
   ```
4. Back up your `.env` file (contains `N8N_ENCRYPTION_KEY`):
   ```bash
   cp .env backups/n8n_env_backup.env
   ```
5. Start n8n again:
   ```bash
   docker compose -f docker/docker-compose.yml start n8n
   ```

## Workflow Example
The project includes a sample workflow located at `src/workflows/example-workflow.json`. This file defines the nodes and connections for automation tasks.

## Configuration
- `k8s/kustomization.yaml` generates the ConfigMap from `k8s/n8n-config.env`, replaces `DOMAIN` placeholders in `k8s/n8n-config.env` and `k8s/n8n-ingress.yaml` using `N8N_DOMAIN`, and applies `k8s/n8n-secret.yaml` for credentials and the `N8N_ENCRYPTION_KEY`.
- `k8s/n8n-config.env` is gitignored, so you can change the domain once without touching tracked files.
- The manifests in `k8s/` define the deployments, services, and PVCs for n8n and PostgreSQL.

## Contributing
Feel free to submit issues or pull requests for improvements or bug fixes.

## License
This project is licensed under the MIT License.
