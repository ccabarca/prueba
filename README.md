
cd ~/Proyectos-2026 && git pull && docker compose -f docker-compose.yml --env-file .env run --rm backend python scripts/reset_user_password.py --email admin@id.com --password 'InforDigital85!' --tenant-code INFORDIGITAL

docker compose -f docker-compose.yml --env-file .env run --rm backend python scripts/reset_user_password.py --email admin@infordigital.com --password 'InforDigital85!' --tenant-code INFORDIGITAL
