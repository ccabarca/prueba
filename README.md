docker compose -f docker-compose.yml --env-file .env run --rm backend \
  python scripts/repair_saas_tenant.py \
    --tenant-code INFORDIGITAL \
    --email admin@infordigital.com \
    --password 'InforDigital123!'
