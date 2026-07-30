docker compose -f docker-compose.yml --env-file .env exec postgres psql -U postgres -d siesa_OT -c "SELECT version_num FROM alembic_version;"
