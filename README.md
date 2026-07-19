# 1) Quitar el bloqueo
sudo docker compose exec -T postgres psql -U postgres -d login_system -c "
DELETE FROM login_lockouts WHERE email = 'carloscabarca03@gmail.com';
"

# 2) Poner contraseña hasheada = 123456
sudo docker compose exec -T postgres psql -U postgres -d login_system -c "
UPDATE users
SET password = 'scrypt:32768:8:1\$Rp3xbmKzVH9ZFjLY\$761920175c8501818682f88dbbcae3b51b00da8b5ef6378928ca31a73df2084a7123b2776f4746c1bc229f069f01e74e6d42bfefc341d197be518934867802fa',
    status = 'activo',
    must_change_password = false,
    archived_at = NULL
WHERE email = 'carloscabarca03@gmail.com';
"













cd ~/Proyectos-2026
git pull   # para traer 050_*.sql y el cambio del servicio

# Backup
mkdir -p backups
sudo docker compose exec -T postgres \
  pg_dump -U postgres -d login_system -Fc \
  > "backups/pre_050_$(date +%F_%H%M).dump"

# Migración completa de contacts (una sola vez)
sudo docker compose exec -T postgres \
  psql -U postgres -d login_system -v ON_ERROR_STOP=1 \
  < database/migrations/050_contacts_crm_full_sync.sql

# Reiniciar API
sudo docker compose restart backend

sudo docker compose exec postgres \
  psql -U postgres -d login_system -c "\d contacts"

sudo docker compose exec backend \
  python scripts/diagnose_contacts_500.py


sudo docker compose exec -T postgres \
  psql -U postgres -d login_system -v ON_ERROR_STOP=1 \
  < database/migrations/016_runtime_schema_cleanup.sql

sudo docker compose exec -T postgres \
  psql -U postgres -d login_system -v ON_ERROR_STOP=1 \
  < database/migrations/006_maintenance_plans.sql



cd ~/Proyectos-2026
git pull

# Ver diferencias codigo vs DB
sudo docker compose exec backend \
  python scripts/sync_contacts_schema.py

# Opción A — migración SQL completa (recomendada)
sudo docker compose exec -T postgres \
  psql -U postgres -d login_system -v ON_ERROR_STOP=1 \
  < database/migrations/050_contacts_crm_full_sync.sql

# Opción B — el script aplica los ALTER faltantes
sudo docker compose exec backend \
  python scripts/sync_contacts_schema.py --apply

sudo docker compose restart backend

sudo docker compose exec backend python scripts/sync_contacts_schema.py

