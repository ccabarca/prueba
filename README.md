curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg \
| sudo gpg --dearmor -o /usr/share/keyrings/cloudflare-main.gpg

echo "deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared any main" \
| sudo tee /etc/apt/sources.list.d/cloudflared.list

sudo apt update
sudo apt install cloudflared

cloudflared --version



sudo docker compose exec -T postgres psql -U postgres -d login_system <<'EOF'
UPDATE users SET rol = 'tecnico' WHERE email = 'otro@correo.com';

INSERT INTO user_roles (user_id, role_id)
SELECT u.id, r.id
FROM users u
CROSS JOIN roles r
WHERE u.email = 'otro@correo.com'
  AND r.slug = 'tecnico'
ON CONFLICT DO NOTHING;
EOF



sudo docker compose exec backend python -c "
from apps.api.app.db.session import get_session_factory
from apps.api.app.services.permission_service import ensure_permission_catalog, sync_all_user_roles
db = get_session_factory()()
try:
    ensure_permission_catalog(db)
    sync_all_user_roles(db)
    print('OK RBAC')
finally:
    db.close()
"
sudo docker compose restart backend
