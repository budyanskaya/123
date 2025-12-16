# 123
```
mkdir lab6_business && cd lab6_business
```

```
nano requirements.txt
```

```
Flask==2.0.1
Werkzeug==2.3.7
redis==4.6.0
```

```
nano app.py
```
```
import time
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incrby('hits', 10)  # Увеличиваем на 10
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return '<h1 style="color:blue">Бизнес-стенд "Инновации"</h1><p>Начислено баллов: <strong>{}</strong></p>'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```

```
nano Dockerfile
```

```
FROM python:3.9-alpine
WORKDIR /app
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

```
nano docker-compose.yml
```

```
version: "3.9"
services:
  web:
    build: .
    ports:
      - "8000:5000"
    depends_on:
      - redis
  redis:
    image: "redis:alpine"
    container_name: my-business-db
```

```
docker compose up -d
```
