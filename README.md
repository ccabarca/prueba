cd ~/Proyectos-2026
git pull
docker compose -f docker-compose.yml --env-file .env up -d --force-recreate backend

docker compose -f docker-compose.yml --env-file .env run --rm backend python scripts/repair_saas_tenant.py --tenant-code INFORDIGITAL --email admin@infordigital.com --password 'TuPasswordSegura!' --clear-lockout
