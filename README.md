
for f in database/migrations/*.sql; do
  echo "=== Aplicando: $f ==="
  sudo docker compose exec -T postgres psql -U postgres -d login_system -v ON_ERROR_STOP=1 < "$f"
  if [ $? -ne 0 ]; then
    echo "FALLÓ en $f"
    break
  fi
done









mkdir -p backups
sudo docker compose exec -T postgres \
  pg_dump -U postgres -d login_system -Fc \
  > "backups/pre_schema_$(date +%F_%H%M).dump"

  for f in \
  database/migrations/016_runtime_schema_cleanup.sql \
  database/migrations/024_contacts_master_normalization.sql \
  database/migrations/031_operational_company_scope.sql \
  database/migrations/041_contacts_offline_sync_metadata.sql \
  database/migrations/006_maintenance_plans.sql \
  database/migrations/048_preventive_scheduling_source.sql
do
  echo "=== $f ==="
  sudo docker compose exec -T postgres \
    psql -U postgres -d login_system -v ON_ERROR_STOP=1 < "$f" \
    || { echo "FALLÓ $f"; break; }
done

for f in database/migrations/*.sql; do
  echo "=== $f ==="
  sudo docker compose exec -T postgres \
    psql -U postgres -d login_system -v ON_ERROR_STOP=1 < "$f" \
    || { echo "FALLÓ $f"; break; }
done

sudo docker compose restart backend

sudo docker compose exec backend \
  python scripts/diagnose_contacts_500.py
