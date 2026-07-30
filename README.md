docker compose -f docker-compose.yml --env-file .env run --rm backend \
  python scripts/repair_saas_tenant.py \
    --tenant-code INFORDIGITAL \
    --email admin@id.com \
    --password 'InforDigital123!'


docker compose -f docker-compose.yml --env-file .env exec db \
  psql -U postgres -d siesa_OT \
  -c "DELETE FROM login_lockouts;"
