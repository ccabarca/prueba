SELECT 'DROP SEQUENCE IF EXISTS public.' || quote_ident(c.relname) || ' CASCADE;'
FROM pg_class c
JOIN pg_namespace n ON n.oid = c.relnamespace
WHERE c.relkind = 'S' 
  AND n.nspname = 'public'
  AND c.relname LIKE '%_id_seq'; -- Cambia este filtro según lo que necesites borrar


SELECT 'DROP SEQUENCE IF EXISTS public.' || quote_ident(c.relname) || ' CASCADE;' FROM pg_class c JOIN pg_namespace n ON n.oid = c.relnamespace WHERE c.relkind = 'S' AND n.nspname = 'public' AND c.relname LIKE '%_id_seq';



DO $$ 
DECLARE
    r RECORD;
BEGIN
    FOR r IN (
        SELECT c.relname AS seq_name
        FROM pg_class c
        JOIN pg_namespace n ON n.oid = c.relnamespace
        WHERE c.relkind = 'S' 
          AND n.nspname = 'public'
          AND c.relname LIKE '%_id_seq'
          -- AQUÍ PUEDES AGREGAR EXCLUSIONES SI LO NECESITAS:
          -- AND c.relname NOT IN ('users_id_seq', 'inventario_id_seq', 'ordenes_trabajo_id_seq') 
    ) LOOP
        EXECUTE 'DROP SEQUENCE IF EXISTS public.' || quote_ident(r.seq_name) || ' CASCADE;';
    END LOOP;
END $$;
