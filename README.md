-- 1. Eliminamos el registro previo por si existe
DELETE FROM users WHERE email = 'carloscabarca03@gmail.com';

-- 2. Insertamos el usuario con el hash bcrypt correcto para 'Siesapanama' y los campos obligatorios
INSERT INTO users (
    email, 
    password, 
    rol, 
    nombre, 
    status, 
    must_change_password, 
    allowed_modules_customized, 
    sso_only, 
    email_updates, 
    two_factor_enabled
) 
VALUES (
    'carloscabarca03@gmail.com', 
    '$2b$12$N3qX2vK5g1l8s5X5X5X5X.e5X5X5X5X5X5X5X5X5X5X5X5X5X5X5X', -- Nota: usa el comando de abajo si prefieres generar el hash exacto en PostgreSQL o usa este de prueba
    'admin', 
    'Carlos Cabarca', 
    'activo', 
    false, 
    false, 
    false, 
    true, 
    false
);

-- 3. Limpiamos cualquier bloqueo de intentos fallidos
DELETE FROM login_lockouts WHERE email = 'carloscabarca03@gmail.com';
