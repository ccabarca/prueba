SELECT 'DROP SEQUENCE IF EXISTS public.' || quote_ident(c.relname) || ' CASCADE;'
FROM pg_class c
JOIN pg_namespace n ON n.oid = c.relnamespace
WHERE c.relkind = 'S' 
  AND n.nspname = 'public'
  AND c.relname LIKE '%_id_seq'; -- Cambia este filtro según lo que necesites borrar


SELECT 'DROP SEQUENCE IF EXISTS public.' || quote_ident(c.relname) || ' CASCADE;' FROM pg_class c JOIN pg_namespace n ON n.oid = c.relnamespace WHERE c.relkind = 'S' AND n.nspname = 'public' AND c.relname LIKE '%_id_seq';
