docker compose -f docker-compose.yml --env-file .env run --rm backend \
  python scripts/repair_saas_tenant.py \
    --tenant-code INFORDIGITAL \
    --email admin@id.com \
    --password 'InforDigital85!'


docker compose -f docker-compose.yml --env-file .env exec db \
  psql -U postgres -d siesa_OT \
  -c "DELETE FROM login_lockouts;"


docker compose -f docker-compose.yml --env-file .env run --rm backend python scripts/repair_saas_tenant.py --tenant-code INFORDIGITAL --email admin@id.com --password 'InforDigital85!' --company-email admin@id.com


docker compose -f docker-compose.yml --env-file .env exec postgres psql -U postgres -d siesa_OT -c "DELETE FROM login_lockouts WHERE email='admin@id.com';"


docker compose -f docker-compose.yml --env-file .env exec postgres psql -U postgres -d siesa_OT -c "UPDATE companies SET email='admin@id.com' WHERE upper(code)='INFORDIGITAL';"
