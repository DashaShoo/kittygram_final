# Kittygram - Социальная сеть для котиков

## Описание проекта

**Kittygram** — это социальная сеть для любителей котиков, где пользователи могут публиковать фотографии своих питомцев, добавлять информацию о них и делиться с сообществом.

### Основные функции:

- Регистрация и аутентификация пользователей
- Создание и редактирование карточек котиков с фотографиями
- Просмотр ленты с котиками других пользователей
- Загрузка медиа-файлов
- REST API для интеграции с фронтенд-приложением
- Административная панель Django для управления контентом

## Стек технологий

### Backend:

- **Python 3.9** - язык программирования
- **Django 3.2.3** - веб-фреймворк
- **Django REST Framework 3.12.4** - для создания REST API
- **PostgreSQL 13** - база данных
- **Gunicorn 20.1.0** - WSGI HTTP сервер
- **Djoser 2.1.0** - пакет для аутентификации и управления пользователями
- **Pillow 9.0.0** - работа с изображениями

### Frontend:

- **React** - фреймворк для создания пользовательского интерфейса
- **Node.js** - среда выполнения JavaScript

### DevOps:

- **Docker** - контейнеризация приложения
- **Docker Compose** - оркестрация контейнеров
- **Nginx 1.22.1** - веб-сервер и обратный прокси
- **GitHub Actions** - автоматизация тестирования и деплоя

## Архитектура приложения

```
┌─────────────────────────────────────────────────────┐
│                    Frontend                          │
│                   (React App)                        │
│                   :3000/9000                         │
└──────────────────────┬──────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────┐
│          Nginx Gateway (kittygram_gateway)           │
│          Reverse Proxy & Static Files                │
│                   :9000                              │
│     ┌───────────────────────────────────────┐       │
│     │ /api/     → backend:8000/api/         │       │
│     │ /admin/   → backend:8000/admin/       │       │
│     │ /static/  → /static/ (volume)         │       │
│     │ /media/   → /media/ (volume)          │       │
│     └───────────────────────────────────────┘       │
└──────────────────────┬──────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────┐
│         Django Backend (kittygram_backend)           │
│           REST API & Admin Panel                     │
│                :8000                                 │
└──────────────────────┬──────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────┐
│         PostgreSQL Database (db)                     │
│              :5432                                   │
└──────────────────────────────────────────────────────┘
```

## Быстрый старт

### Предварительные требования

- Docker и Docker Compose установлены на вашей машине
- Git для клонирования репозитория

### Установка и запуск локально

1. **Клонируйте репозиторий:**

   ```bash
   git clone <repository-url>
   cd kittygram_final
   ```

2. **Создайте файл `.env`:**

   ```bash
   cp .env.example .env
   ```

3. **Отредактируйте `.env` файл** и установите необходимые переменные:

   ```
   POSTGRES_DB=kittygram
   POSTGRES_USER=kittygram_user
   POSTGRES_PASSWORD=kittygram_password
   DB_HOST=db
   DB_PORT=5432
   SECRET_KEY=your-secret-key-here
   DEBUG=True
   ALLOWED_HOSTS=localhost,127.0.0.1
   ```

4. **Запустите контейнеры:**

   ```bash
   docker-compose up -d
   ```

5. **Выполните миграции базы данных:**

   ```bash
   docker-compose exec backend python manage.py migrate
   ```

6. **Создайте суперпользователя:**

   ```bash
   docker-compose exec backend python manage.py createsuperuser
   ```

7. **Соберите статические файлы:**

   ```bash
   docker-compose exec backend python manage.py collectstatic --noinput
   ```

8. **Откройте приложение в браузере:**
   ```
   http://localhost:9000
   ```

## Конфигурация переменных окружения

Используйте файл `.env.example` как шаблон. Основные переменные:

| Переменная          | Описание              | Значение по умолчанию |
| ------------------- | --------------------- | --------------------- |
| `POSTGRES_DB`       | Имя базы данных       | kittygram             |
| `POSTGRES_USER`     | Пользователь БД       | kittygram_user        |
| `POSTGRES_PASSWORD` | Пароль БД             | kittygram_password    |
| `DB_HOST`           | Хост БД               | db                    |
| `DB_PORT`           | Порт БД               | 5432                  |
| `SECRET_KEY`        | Django секретный ключ | -                     |
| `DEBUG`             | Режим отладки         | False                 |
| `ALLOWED_HOSTS`     | Разрешенные хосты     | localhost,127.0.0.1   |

### Для GitHub Actions (добавьте секреты в репозитории):

| Секрет            | Описание                |
| ----------------- | ----------------------- |
| `DOCKER_USERNAME` | Логин на Docker Hub     |
| `DOCKER_PASSWORD` | Пароль на Docker Hub    |
| `TELEGRAM_TOKEN`  | Токен Telegram бота     |
| `TELEGRAM_TO`     | ID чата для уведомлений |

## Работа с Docker

### Основные команды:

```bash
# Запуск контейнеров
docker-compose up -d

# Остановка контейнеров
docker-compose down

# Просмотр логов
docker-compose logs -f backend

# Выполнение команды в контейнере
docker-compose exec backend python manage.py <command>

# Пересборка образов
docker-compose build --no-cache

# Удаление всех данных
docker-compose down -v
```

## CI/CD с GitHub Actions

### Workflow процесс:

1. **При пуше в ветку main** запускается автоматический workflow:
   - ✅ Проверка кода с помощью Ruff, Pycodestyle
   - ✅ Запуск тестов backend и frontend
   - ✅ Сборка Docker образов
   - ✅ Загрузка образов на Docker Hub
   - 🔔 Отправка уведомления в Telegram

2. **Образы на Docker Hub:**
   - `{username}/kittygram_backend`
   - `{username}/kittygram_frontend`
   - `{username}/kittygram_gateway`

## Структура проекта

```
kittygram_final/
├── .github/
│   └── workflows/
│       └── main.yml                 # GitHub Actions workflow
├── backend/
│   ├── Dockerfile                   # Docker образ backend
│   ├── .dockerignore
│   ├── requirements.txt              # Python зависимости
│   ├── manage.py
│   ├── cats/                         # Django приложение
│   │   ├── models.py
│   │   ├── views.py
│   │   ├── serializers.py
│   │   ├── urls.py
│   │   └── ...
│   └── kittygram_backend/
│       ├── settings.py               # Django конфигурация
│       ├── urls.py
│       └── wsgi.py
├── frontend/
│   ├── Dockerfile                   # Docker образ frontend
│   ├── .dockerignore
│   ├── public/
│   ├── src/
│   │   ├── components/              # React компоненты
│   │   ├── pages/
│   │   └── App.js
│   └── package.json
├── nginx/
│   ├── Dockerfile                   # Docker образ gateway
│   └── nginx.conf                   # Конфигурация nginx
├── docker-compose.yml               # Оркестрация контейнеров
├── .env                             # Переменные окружения
├── .env.example                     # Пример переменных окружения
└── README.md                        # Этот файл
```

## Тестирование

### Запуск тестов локально:

```bash
# Backend тесты
cd backend
python manage.py test

# Frontend тесты
cd frontend
npm test -- --watchAll=false

# Проверка кода с Ruff
ruff check backend

# Проверка форматирования
ruff format backend --check

# Проверка с Pycodestyle
pycodestyle backend --max-line-length=127
```

## Управление базой данных

```bash
# Создание миграций
docker-compose exec backend python manage.py makemigrations

# Применение миграций
docker-compose exec backend python manage.py migrate

# Создание суперпользователя
docker-compose exec backend python manage.py createsuperuser

# Сбор статических файлов
docker-compose exec backend python manage.py collectstatic --noinput
```

## Доступ к приложению

После запуска приложение доступно по адресам:

- **Главная страница:** http://localhost:9000
- **Администраторская панель:** http://localhost:9000/admin/
- **API документация:** http://localhost:9000/api/
- **Админ-панель Django:** http://localhost:9000/admin/

## Проблемы и решения

### Ошибка подключения к БД

- Убедитесь, что контейнер `db` запущен: `docker-compose ps`
- Проверьте переменные окружения в файле `.env`
- Очистите данные: `docker-compose down -v` и перезапустите

### Статические файлы не отображаются

- Выполните: `docker-compose exec backend python manage.py collectstatic --noinput`
- Перезагрузите nginx: `docker-compose restart gateway`

### Ошибки при загрузке фото

- Увеличьте `client_max_body_size` в `nginx.conf` (уже установлено на 10M)
- Проверьте права доступа к volume `media`

## Автор

Проект создан как финальное задание для спринта по Docker и GitHub Actions.

## Лицензия

MIT License

## Ссылки

- [Django документация](https://docs.djangoproject.com/)
- [Django REST Framework](https://www.django-rest-framework.org/)
- [Docker документация](https://docs.docker.com/)
- [GitHub Actions](https://docs.github.com/en/actions)
