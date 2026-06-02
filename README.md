# Business Analytics Dashboard

Интерактивная BI-панель для анализа и визуализации бизнес-данных.

## Описание

Система позволяет подключаться к различным источникам данных, создавать интерактивные дашборды, анализировать метрики и экспортировать отчеты.

## Технологии

Frontend: React, TypeScript, Material-UI
Backend: FastAPI, Python
Database: PostgreSQL
Deployment: Docker

## Системные требования

Для установки и запуска проекта необходимо:

- Node.js версии 16 и выше
- Python версии 3.10 и выше
- PostgreSQL версии 12 и выше
- Docker и Docker Compose
- Git
- Минимум 4GB памяти RAM
- Минимум 2GB свободного дискового пространства

## Установка и запуск

### Быстрый старт с Docker (рекомендуется)

1. Клонируйте репозиторий

git clone https://github.com/Leonalzxc/Business-Analytics-Dashboard.git
cd Business-Analytics-Dashboard

2. Создайте файл .env (если требуется)

cp .env.example .env

3. Запустите проект с Docker Compose

docker-compose up -d

После этого приложение будет доступно по адресам:
- Frontend: http://localhost:3000
- Backend API: http://localhost:8000
- API документация: http://localhost:8000/docs

4. Остановка проекта

docker-compose down

### Локальная установка

#### Backend (Python)

1. Перейдите в директорию backend

cd backend

2. Создайте виртуальное окружение

python -m venv venv
source venv/bin/activate

На Windows используйте:

venv\Scripts\activate

3. Установите зависимости

pip install -r requirements.txt

4. Примените миграции базы данных

alembic upgrade head

5. Запустите сервер разработки

uvicorn app.main:app --reload

Сервер будет доступен по адресу http://localhost:8000

#### Frontend (React)

1. Перейдите в директорию frontend

cd frontend

2. Установите зависимости

npm install

3. Запустите сервер разработки

npm start

Приложение откроется в браузере по адресу http://localhost:3000

## Структура проекта

frontend/ - React приложение с компонентами и логикой UI
backend/ - FastAPI сервер с API endpoints и бизнес-логикой
docs/ - Документация проекта (требования, архитектура)
docker-compose.yml - Конфигурация для запуска в Docker
.gitignore - Исключаемые файлы для Git
LICENSE - MIT лицензия

## Git процесс разработки

main - ветка production (защищена)
develop - основная ветка разработки
feature/описание - ветки для новых функций

Для разработки новой функции:

git checkout develop
git pull origin develop
git checkout -b feature/JIRA-XXX-описание

После завершения создайте Pull Request в develop.

## Документация

SRS (требования): docs/SRS.md
Архитектура проекта: docs/ARCHITECTURE.md

## Лицензия

Проект лицензирован под MIT License. Подробности в файле LICENSE.

## Контакты

Email: leonalzxc@example.com
GitHub: https://github.com/Leonalzxc/Business-Analytics-Dashboard
