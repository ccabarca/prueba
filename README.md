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



docker compose -f docker-compose.yml exec backend python -c "
from passlib.context import CryptContext
import psycopg2
import os

pwd_context = CryptContext(schemes=['bcrypt'], deprecated='auto')
hashed = pwd_context.hash('Siesapanama')

conn = psycopg2.connect(os.getenv('DATABASE_URL', 'postgresql://postgres:postgres@postgres:5432/siesa_OT'))
cur = conn.cursor()
cur.execute('UPDATE users SET password = %s WHERE email = %s', (hashed, 'carloscabarca03@gmail.com'))
conn.commit()
cur.close()
conn.close()
print('Contraseña actualizada con bcrypt exitosamente.')
"











cat << 'EOF' > update_pass.py
from passlib.context import CryptContext
import psycopg2
import os

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
hashed = pwd_context.hash("Siesapanama")

conn = psycopg2.connect(os.getenv("DATABASE_URL", "postgresql://postgres:postgres@postgres:5432/siesa_OT"))
cur = conn.cursor()
cur.execute("UPDATE users SET password = %s WHERE email = %s", (hashed, "carloscabarca03@gmail.com"))
conn.commit()
cur.close()
conn.close()
print("¡Contraseña actualizada con bcrypt exitosamente!")
EOF

docker compose -f docker-compose.yml exec backend python update_pass.py
rm update_pass.py

docker compose -f docker-compose.yml exec postgres psql -U postgres -d siesa_OT -c "DELETE FROM login_lockouts WHERE email = 'carloscabarca03@gmail.com';"
