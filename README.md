# PASO 2: Migraciones SQL (bootstrap) — usa python3
docker exec proyectos-2026-backend-1 python3 scripts/bootstrap_database.py --skip-seed

# PASO 3: Migraciones Alembic
docker exec proyectos-2026-backend-1 bash -c 'cd /app && alembic upgrade head'

# PASO 4: Contar tablas — conectar al postgres con usuario correcto
docker exec proyectos-2026-postgres-1 psql -U postgres -d login_system -c "SELECT COUNT(*) as total_tablas FROM information_schema.tables WHERE table_schema='public' AND table_type='BASE TABLE';"

# PASO 5: Reiniciar backend
docker compose restart backend
