# Ver ids de empresas
docker compose -f docker-compose.yml --env-file .env exec db \
  psql -U "$POSTGRES_USER" -d "$POSTGRES_DB" \
  -c "SELECT id, code, name FROM companies WHERE archived_at IS NULL ORDER BY id;"

# Vincular (ajusta --company-id al id real)
docker compose -f docker-compose.yml --env-file .env run --rm backend \
  python scripts/link_company_to_tenant.py --tenant-code SIESA --company-id 1
