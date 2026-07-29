
cd ~/Proyectos-2026

# Paso 1: Backup
docker exec proyectos-2026-postgres-1 pg_dump \
  -U postgres -d login_system -Fc \
  > ~/backup_pre_migracion_$(date +%F_%H%M).dump

echo "✅ Backup listo"

# Paso 2: Aplicar migraciones SQL (bootstrap)
docker exec proyectos-2026-backend-1 \
  python scripts/bootstrap_database.py --skip-seed

# Paso 3: Aplicar migraciones Alembic
docker exec proyectos-2026-backend-1 \
  sh -c 'cd /app && alembic upgrade head'

# Paso 4: Verificar tablas
docker exec proyectos-2026-postgres-1 psql \
  -U postgres -d login_system \
  -c "SELECT COUNT(*) as total_tablas FROM information_schema.tables \
      WHERE table_schema='public' AND table_type='BASE TABLE';"

# Paso 5: Reiniciar backend
docker compose restart backend
