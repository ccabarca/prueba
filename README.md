# Contar tablas (debe acercarse a ~155)
docker compose -f docker-compose.yml exec postgres \
  psql -U postgres -d siesa_OT -tAc \
  "SELECT COUNT(*) FROM information_schema.tables WHERE table_schema='public' AND table_type='BASE TABLE'"
