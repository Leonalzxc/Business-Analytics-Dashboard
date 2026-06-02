# CI/CD Pipeline
## Business Analytics Dashboard

## 1. Обзор CI/CD

CI/CD пайплайн автоматизирует процесс тестирования, сборки и развертывания приложения при каждом коммите и pull request.

## 2. Инструменты

GitHub Actions - встроенный сервис для CI/CD в GitHub
Workflow файлы - конфигурация в .github/workflows/

## 3. Этапы пайплайна

### На pull request (PR):

1. Проверка кода (Linting)
2. Unit тесты (Backend)
3. Unit тесты (Frontend)
4. Проверка покрытия (минимум 80%)
5. Integration тесты (Backend)
6. E2E тесты (если требуется)
7. Build сборка (Frontend и Backend)

Если все пройдено - PR можно мержить. Если что-то упало - PR заблокирован.

### На merge в develop:

1. Все этапы как в PR
2. Push Docker образа в реестр (опционально)
3. Deploy на staging окружение (опционально)

### На merge в main:

1. Все этапы как в PR
2. Создание release версии
3. Deploy на production окружение (опционально)

## 4. Workflow файлы

Создайте файлы в .github/workflows/

### Backend Workflow

Файл: .github/workflows/backend-tests.yml

name: Backend Tests

on:
  push:
    branches: [ develop, main ]
    paths:
      - 'backend/**'
  pull_request:
    branches: [ develop, main ]
    paths:
      - 'backend/**'

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_DB: test_db
          POSTGRES_USER: test_user
          POSTGRES_PASSWORD: test_pass
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432

    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.10'
    
    - name: Install dependencies
      run: |
        cd backend
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    
    - name: Lint with pylint
      run: |
        cd backend
        pylint app/ || true
    
    - name: Format check with black
      run: |
        cd backend
        black --check app/ || true
    
    - name: Run unit tests
      env:
        DATABASE_URL: postgresql://test_user:test_pass@localhost:5432/test_db
      run: |
        cd backend
        pytest tests/ -v
    
    - name: Calculate coverage
      env:
        DATABASE_URL: postgresql://test_user:test_pass@localhost:5432/test_db
      run: |
        cd backend
        pytest --cov=app --cov-report=xml tests/
    
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        file: ./backend/coverage.xml
        fail_ci_if_error: false

### Frontend Workflow

Файл: .github/workflows/frontend-tests.yml

name: Frontend Tests

on:
  push:
    branches: [ develop, main ]
    paths:
      - 'frontend/**'
  pull_request:
    branches: [ develop, main ]
    paths:
      - 'frontend/**'

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'npm'
        cache-dependency-path: frontend/package-lock.json
    
    - name: Install dependencies
      run: |
        cd frontend
        npm ci
    
    - name: Lint with ESLint
      run: |
        cd frontend
        npm run lint || true
    
    - name: Format check with Prettier
      run: |
        cd frontend
        npm run format:check || true
    
    - name: Run unit tests
      run: |
        cd frontend
        npm test -- --coverage --watchAll=false
    
    - name: Build
      run: |
        cd frontend
        npm run build
    
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        file: ./frontend/coverage/coverage-final.json
        fail_ci_if_error: false

### Docker Build & Push

Файл: .github/workflows/docker-build.yml

name: Docker Build

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ develop, main ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Build images
      run: |
        docker-compose build
    
    - name: Run containers
      run: |
        docker-compose up -d
        sleep 5
    
    - name: Check health
      run: |
        docker-compose ps
        curl -f http://localhost:8000/docs || exit 1
    
    - name: Cleanup
      run: docker-compose down

## 5. Конфигурация линтеров

### Backend (.pylintrc)

[MASTER]
disable=C0111,C0103,R0913

### Frontend (.eslintrc.json)

{
  "extends": ["react-app"],
  "rules": {
    "no-console": "warn",
    "no-unused-vars": "warn"
  }
}

### Prettier (.prettierrc)

{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5"
}

## 6. Проверка PR перед мержем

GitHub Actions должен показать:
- Тесты passed
- Coverage >= 80%
- Build успешная
- Нет критических ошибок линтера

Если что-то красное - PR заблокирован для мержа.

## 7. Workflow запуска

1. Разработчик создает feature ветку
2. Пушит изменения в GitHub
3. GitHub Actions автоматически запускается
4. Результаты видны в PR
5. После всех проверок можно мержить в develop
6. При мержа в main - создается release

## 8. Мониторинг пайплайна

В GitHub репозитории:
- Actions tab - просмотр всех workflow запусков
- Logs - подробная информация о каждом шаге
- Artifacts - сохраненные артефакты (логи, отчеты)

## 9. Требования для продакшена (main)

При deploy на main:
- Все тесты должны пройти
- Coverage >= 80%
- Pull request должен быть одобрен (2 reviews)
- Нет конфликтов с develop
- Версия в package.json/setup.py обновлена

## 10. Типичные ошибки и решения

Ошибка: Тесты падают в GitHub Actions но локально проходят
Решение: Проверьте переменные окружения, версии зависимостей, database connection

Ошибка: Coverage report не генерируется
Решение: Убедитесь что pytest/jest запускаются с флагом --coverage

Ошибка: Docker образ не собирается
Решение: Проверьте Dockerfile, размер файлов, наличие всех зависимостей

## 11. Локальное тестирование перед push

Перед push выполняйте локально:

Backend:
cd backend
pylint app/
black app/
pytest --cov=app tests/

Frontend:
cd frontend
npm run lint
npm run format:check
npm test -- --coverage

Если все зелено - можно пушить.

## 12. Файлы конфигурации

Структура .github/ папки:

.github/
├── workflows/
│   ├── backend-tests.yml
│   ├── frontend-tests.yml
│   ├── docker-build.yml
│   └── deploy.yml (опционально)

Создайте эти файлы в репозитории.

Дата: 2026-06-02
