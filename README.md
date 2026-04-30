# Effective Mobile — Тестовое задание DevOps

Проект демонстрирует навыки работы с Docker, Docker Compose и Nginx в роли reverse proxy.

## Архитектура

[Хост] :80 → [nginx] → [backend:8080]

- backend — Python HTTP-сервер (порт 8080), отвечает на / текстом Hello from Effective Mobile!
- nginx — официальный образ, принимает запросы на порт 80 и проксирует их в контейнер backend
- сеть — внутренняя docker-сеть effective-mobile-net, backend недоступен с хоста напрямую

## Используемые технологии

- Docker, Docker Compose
- Python 3.11-alpine (http.server)
- Nginx 1.25-alpine (reverse proxy)
- Alpine Linux

## Структура проекта

effective-mobile-devops/  <br>
├── backend/  <br>
│   ├── Dockerfile  <br>
│   └── app.py  <br>
├── nginx/  <br>
│   └── nginx.conf  <br>
├── docker-compose.yml  <br>
├── .env  <br>
├── .gitignore  <br>
└── README.md

## Запуск
### 1. Клонируйте репозиторий
```bash
git clone https://github.com/<user>/effective-mobile-devops.git
cd effective-mobile-devops
```
### 2. Убедитесь, что порт 80 свободен
```bash
#На Windows:
netstat -ano | findstr :80

#На Linux:
sudo lsof -i :80
```
Если порт занят, остановите мешающую службу.

### 3. Запустите контейнеры
```bash
docker-compose up -d
```
## Проверка работоспособности
```bash
curl http://localhost
```
Ожидаемый ответ:
Hello from Effective Mobile!

## Просмотр логов
```bash
docker logs effective-mobile-backend
docker logs effective-mobile-nginx
docker-compose logs -f
```
## Особенности реализации

- Безопасность: backend контейнер работает от непривилегированного пользователя (appuser)
- Healthcheck: настроен мониторинг состояния backend
- Проксирование заголовков: nginx передаёт Host, X-Real-IP, X-Forwarded-For
- Сетевая изоляция: наружу проброшен только порт 80 (nginx), backend только во внутренней сети
- Понятные имена: effective-mobile-backend, effective-mobile-nginx
- Отдельная docker-сеть: effective-mobile-net
- Volume для конфига: nginx.conf монтируется с хоста, не зашит в образ
