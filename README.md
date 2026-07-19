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
