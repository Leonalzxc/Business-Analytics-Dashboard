# Руководство для разработчиков

## 🔧 Настройка окружения

### Backend

```bash
cd backend
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
alembic upgrade head      # Миграции БД
uvicorn app.main:app --reload
```

### Frontend

```bash
cd frontend
npm install
npm start
```

## 📝 Git Workflow

### Создание ветки

```bash
git checkout develop
git pull origin develop
git checkout -b feature/JIRA-XXX-описание
```

### Коммиты (Conventional Commits)

```bash
git commit -m "feat(JIRA-1): add dashboard filter"
git commit -m "fix(JIRA-2): resolve export bug"
git commit -m "test(JIRA-3): add unit tests"
```

**Типы**: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

### Push и Pull Request

```bash
git push origin feature/JIRA-XXX-описание

# В GitHub создайте PR:
# - Title: "JIRA-XXX: описание"
# - Description: четкое описание + скриншоты (если UI)
```

## 🧪 Тестирование

### Backend

```bash
cd backend
pytest                    # Все тесты
pytest -v               # Подробный вывод
pytest --cov           # С покрытием
```

### Frontend

```bash
cd frontend
npm test                # Запуск тестов
npm run test:coverage  # С покрытием
```

## 📊 Требования к коду

- **Покрытие**: ≥ 80% тестами
- **Linting**: ESLint (frontend), Black/Pylint (backend)
- **Типизация**: TypeScript (frontend), Type hints (backend)

## ✅ Перед Pull Request

```bash
# 1. Обновите develop
git checkout develop
git pull origin develop
git checkout feature/JIRA-XXX-описание

# 2. Rebase ветки
git rebase develop

# 3. Запустите тесты локально
npm test          # frontend
pytest            # backend

# 4. Push
git push origin feature/JIRA-XXX-описание
```

## 🔍 Code Review требования

- ✅ 2 одобрения
- ✅ CI/CD зеленый
- ✅ Coverage >= 80%
- ✅ Нет конфликтов с develop
- ✅ PR описание заполнено

## 📚 Полезные команды

```bash
# Docker
docker-compose up -d          # Запуск
docker-compose logs -f        # Логи
docker-compose down           # Остановка

# Git
git log --oneline             # История коммитов
git diff develop              # Изменения
git stash                      # Сохранить изменения
```

**Дата публикации**: 2026-06-02
