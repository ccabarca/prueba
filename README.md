docker compose -f docker-compose.yml exec backend python -c "
from passlib.context import CryptContext
import psycopg2
import os

pwd_context = CryptContext(schemes=['bcrypt'], deprecated='auto')
hashed = pwd_context.hash('Siesapanama')

# Extraer conexión del entorno o usar la URL por defecto
conn = psycopg2.connect(os.getenv('DATABASE_URL', 'postgresql://postgres:postgres@postgres:5432/siesa_OT'))
cur = conn.cursor()
cur.execute('UPDATE users SET password = %s WHERE email = %s', (hashed, 'carloscabarca03@gmail.com'))
conn.commit()
cur.close()
conn.close()
print('Contraseña actualizada con bcrypt exitosamente.')
"
