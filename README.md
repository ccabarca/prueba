
docker compose -f docker-compose.yml --env-file .env exec postgres psql -U postgres -d siesa_OT -c "SELECT setval(pg_get_serial_sequence('companies','id'), GREATEST((SELECT MAX(id) FROM companies),1)); INSERT INTO companies (code, name, legal_name, status, is_default, email) SELECT 'INFORDIGITAL', 'Infor-Digital S.A.', 'Infor-Digital S.A.', 'activo', false, 'admin@id.com' WHERE NOT EXISTS (SELECT 1 FROM companies WHERE upper(code)='INFORDIGITAL'); UPDATE companies SET email='admin@id.com' WHERE upper(code)='INFORDIGITAL';"

git pull
docker compose -f docker-compose.yml --env-file .env run --rm backend python scripts/repair_saas_tenant.py --tenant-code INFORDIGITAL --email admin@id.com --password 'InforDigital85!' --company-email admin@id.com

docker compose -f docker-compose.yml --env-file .env exec postgres psql -U postgres -d siesa_OT -c "SELECT id, code, name, email FROM companies WHERE upper(code)='INFORDIGITAL'; SELECT id, email, rol, active_company_id, status FROM users WHERE email='admin@id.com';"
