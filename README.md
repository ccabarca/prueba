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



cd ~/Proyectos-2026

# Si aún no tienes el archivo en el repo, pégalo o usa este one-liner:
sudo docker compose exec -T postgres psql -U postgres -d login_system <<'EOF'
DO $$
DECLARE
    cid INTEGER;
    t TEXT;
    tables TEXT[] := ARRAY[
        'contacts','sites','activos','solicitudes','ordenes_trabajo','inventario',
        'invoices','invoice_items','payments','credit_notes','debit_notes',
        'journal_entries','journal_entry_lines','bank_accounts','bank_statement_lines',
        'accounting_periods','leads','opportunities','crm_activities',
        'maintenance_plans','proyectos','cmms_technicians','cmms_crews',
        'audit_logs','ai_queries'
    ];
BEGIN
    SELECT id INTO cid FROM companies WHERE archived_at IS NULL
    ORDER BY is_default DESC, id LIMIT 1;

    FOREACH t IN ARRAY tables LOOP
        IF to_regclass('public.'||t) IS NULL THEN CONTINUE; END IF;
        IF NOT EXISTS (
            SELECT 1 FROM information_schema.columns
            WHERE table_schema='public' AND table_name=t AND column_name='company_id'
        ) THEN CONTINUE; END IF;
        EXECUTE format('UPDATE %I SET company_id=$1 WHERE company_id IS NULL', t) USING cid;
    END LOOP;

    UPDATE users SET active_company_id = cid
    WHERE active_company_id IS NULL AND archived_at IS NULL;

    INSERT INTO user_allowed_companies (user_id, company_id)
    SELECT u.id, cid FROM users u
    WHERE u.archived_at IS NULL AND COALESCE(u.status,'activo')='activo'
    ON CONFLICT DO NOTHING;
END $$;
EOF

sudo docker compose restart backend



sudo docker compose exec -T postgres psql -U postgres -d login_system -c "
SELECT COUNT(*) FILTER (WHERE company_id IS NULL) AS sin_empresa,
       COUNT(*) FILTER (WHERE company_id = 1) AS empresa_1
FROM activos WHERE archived_at IS NULL;
"
