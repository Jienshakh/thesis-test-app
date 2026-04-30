# Тестовое приложение nginx-test-app

## Структура проекта
```
test-app/
├── Dockerfile
├── nginx.conf
└── html/
└── index.html
```

## Файлы конфигурации

### 1. `nginx.conf`

```nginx
server {
    listen 80;
    server_name _;
    root /usr/share/nginx/html;
    index index.html;
    
    # Отдача статики
    location / {
        try_files $uri $uri/ =404;
    }
    
    # Healthcheck
    location /health {
        return 200 'healthy\n';
        add_header Content-Type text/plain;
    }
    
    # Имитация основного API
    location /api/v1/status {
        return 200 '{"app": "emulator", "status": "ok"}';
        add_header Content-Type application/json;
    }
}

```

### 2. html/index.html

```
<!DOCTYPE html>
<html>
<head>
    <title>Test Application Emulator</title>
</head>
<body>
    <h1>Main Application Emulator</h1>
    <p>Status: Running</p>
    <p>Environment: Test</p>
</body>
</html>
```
### 3. Dockerfile

```
FROM nginx:alpine

COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY html/index.html /usr/share/nginx/html/index.html

```

## Инструкция по запуску

### Сборка образа

```bash
docker build -t nginx-test-app:v1.0 .
```

### Запуск контейнера

```bash
docker run --rm -d -p 8080:80 nginx-test-app:v1.0
```

### Проверка работы

```bash
# Главная страница
curl http://localhost:8080/

# Healthcheck эндпоинт
curl http://localhost:8080/health

# Имитация основного API
curl http://localhost:8080/api/v1/status
```

