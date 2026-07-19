curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg \
| sudo gpg --dearmor -o /usr/share/keyrings/cloudflare-main.gpg

echo "deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared any main" \
| sudo tee /etc/apt/sources.list.d/cloudflared.list

sudo apt update
sudo apt install cloudflared

cloudflared --version



sudo docker compose exec -T postgres psql -U postgres -d login_system <<'EOF'
SELECT email, rol, active_company_id
FROM users
WHERE rol ILIKE '%admin%' OR email ILIKE '%@%'
ORDER BY id;

SELECT
  COUNT(*) AS total,
  COUNT(*) FILTER (WHERE company_id IS NULL) AS sin_empresa,
  COUNT(*) FILTER (WHERE company_id IS NOT NULL) AS con_empresa
FROM activos
WHERE archived_at IS NULL;

SELECT id, name FROM companies WHERE archived_at IS NULL;
EOF


sudo docker compose exec -T postgres psql -U postgres -d login_system <<'EOF'
-- 1) Asignar la empresa a los activos que no la tienen
UPDATE activos
SET company_id = (SELECT id FROM companies WHERE archived_at IS NULL ORDER BY is_default DESC, id LIMIT 1)
WHERE company_id IS NULL
  AND archived_at IS NULL;

-- 2) Que todos los admin usen esa misma empresa (o NULL para ver todo)
UPDATE users
SET active_company_id = (SELECT id FROM companies WHERE archived_at IS NULL ORDER BY is_default DESC, id LIMIT 1)
WHERE LOWER(COALESCE(rol, '')) IN ('admin', 'administrador');
EOF
