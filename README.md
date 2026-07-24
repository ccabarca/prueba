echo "from passlib.context import CryptContext; import psycopg2, os; pwd = CryptContext(schemes=['bcrypt'], deprecated='auto').hash('Siesapanama'); conn = psycopg2.connect(os.getenv('DATABASE_URL', 'postgresql://postgres:postgres@postgres:5432/siesa_OT')); cur = conn.cursor(); cur.execute('UPDATE users SET password = %s WHERE email = %s', (pwd, 'carloscabarca03@gmail.com')); conn.commit(); cur.close(); conn.close(); print('OK')" > update_pass.py

docker compose -f docker-compose.yml exec backend python update_pass.py

rm update_pass.py

docker compose -f docker-compose.yml exec postgres psql -U postgres -d siesa_OT -c "DELETE FROM login_lockouts WHERE email = 'carloscabarca03@gmail.com';"


docker compose -f docker-compose.yml exec backend python -c "
from passlib.context import CryptContext
import psycopg2, os
pwd = CryptContext(schemes=['bcrypt'], deprecated='auto').hash('Siesapanama')
conn = psycopg2.connect(os.getenv('DATABASE_URL', 'postgresql://postgres:postgres@postgres:5432/siesa_OT'))
cur = conn.cursor()
cur.execute('UPDATE users SET password = %s WHERE email = %s', (pwd, 'carloscabarca03@gmail.com'))
conn.commit()
cur.close()
conn.close()
print('¡CONTRASEÑA ACTUALIZADA CON EXITO!')
"
