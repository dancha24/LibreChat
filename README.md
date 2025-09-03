# 🚀 MetoGPT - LibreChat с коммерческими функциями

[![LibreChat](https://img.shields.io/badge/LibreChat-v0.8.0--rc3-blue.svg)](https://github.com/danny-avila/LibreChat)
[![License](https://img.shields.io/badge/License-ISC-green.svg)](LICENSE)
[![Node.js](https://img.shields.io/badge/Node.js-18+-yellow.svg)](https://nodejs.org/)
[![MongoDB](https://img.shields.io/badge/MongoDB-5.0+-orange.svg)](https://www.mongodb.com/)

> **MetoGPT** - это расширенная версия LibreChat с добавлением коммерческих функций для управления пользователями, ролями и тарификацией AI-моделей.

## 🌟 Основные возможности

### 🎯 **Система ролей и разрешений**
- **Гибкое управление правами** - создание, редактирование и удаление ролей
- **Управление балансами** - разрешения на изменение балансов пользователей
- **Доступ к моделям** - настройка доступных AI-моделей для каждой роли
- **Коэффициенты стоимости** - индивидуальные множители для каждой модели

### 💰 **Система расчета стоимости**
- **Коэффициенты моделей** - настраиваемые множители стоимости токенов
- **Проверка доступа** - автоматическая проверка разрешений на использование моделей
- **Детальная отчетность** - логирование всех транзакций с коэффициентами

### 🔌 **REST API для управления**
- **Управление пользователями** - CRUD операции с балансами и ролями
- **Управление ролями** - полный цикл создания и редактирования ролей
- **Безопасность** - JWT аутентификация и проверка разрешений
- **Валидация** - строгая проверка входных данных

## 🏗️ Архитектура

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   LibreChat     │    │  Commercial API  │    │     MongoDB     │
│   (Port 3080)   │◄──►│   (Port 3000)    │◄──►│   (Port 27017)  │
│                 │    │                  │    │                 │
│ • AI Chat       │    │ • User Mgmt      │    │ • Users         │
│ • Token Billing │    │ • Role Mgmt      │    │ • Roles         │
│ • Model Access  │    │ • Balance Mgmt   │    │ • Transactions  │
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

## 🚀 Быстрый старт

### 1. Клонирование репозитория
```bash
git clone https://github.com/dancha24/MetoGPT.git
cd MetoGPT
```

### 2. Установка зависимостей
```bash
npm install
```

### 3. Настройка окружения
```bash
# Скопируйте .env.example в .env
cp .env.example .env

# Отредактируйте .env файл
# MONGODB_URI=mongodb://localhost:27017/LibreChat
```

### 4. Запуск с Docker (рекомендуется)
```bash
# Запуск всех сервисов
docker-compose up -d

# Проверка статуса
docker-compose ps
```

### 5. Миграция ролей
```bash
npm run migrate:roles:simple
```

## 🌐 Доступные сервисы

После запуска у вас будет доступ к:

- **LibreChat**: http://localhost:3080 - основной AI чат
- **Commercial API**: http://localhost:3000 - управление коммерческими функциями
- **MongoDB**: localhost:27017 - база данных
- **Meilisearch**: localhost:7700 - поиск
- **PostgreSQL**: localhost:5432 - векторная база данных

## 📚 API Endpoints

### Управление пользователями
```bash
GET    /api/admin/users              # Список пользователей
PUT    /api/admin/users/:id/balance  # Обновление баланса
PUT    /api/admin/users/:id/role     # Изменение роли
GET    /api/admin/users/:id/models   # Доступные модели
```

### Управление ролями
```bash
GET    /api/admin/roles              # Список ролей
POST   /api/admin/roles              # Создание роли
PUT    /api/admin/roles/:name        # Обновление роли
DELETE /api/admin/roles/:name        # Удаление роли
```

## 🔧 Примеры использования

### Создание роли "PRO"
```bash
curl -X POST http://localhost:3000/api/admin/roles \
  -H "Content-Type: application/json" \
  -d '{
    "name": "PRO",
    "permissions": {
      "ROLE_MANAGEMENT": {
        "CREATE": false,
        "UPDATE": false,
        "DELETE": false
      },
      "BALANCE_MANAGEMENT": {
        "UPDATE": false
      }
    },
    "modelAccess": {
      "gpt-4": {
        "enabled": true,
        "coefficient": 1.5
      },
      "claude-3": {
        "enabled": true,
        "coefficient": 1.8
      }
    }
  }'
```

### Обновление баланса пользователя
```bash
curl -X PUT http://localhost:3000/api/admin/users/USER_ID/balance \
  -H "Content-Type: application/json" \
  -d '{
    "balance": 5000,
    "reason": "Пополнение баланса"
  }'
```

## 📁 Структура проекта

```
MetoGPT/
├── api/                          # Backend API
│   ├── models/                   # Модели данных
│   │   ├── roleMethods.js       # Методы для работы с ролями
│   │   └── balanceMethods.js    # Методы для работы с балансами
│   └── server/routes/           # API маршруты
│       └── admin.js             # Административные endpoints
├── packages/                     # Пакеты LibreChat
│   ├── data-schemas/            # Схемы данных
│   └── data-provider/           # Провайдеры данных
├── config/                       # Конфигурация
│   └── simple-migrate-roles.js  # Миграция ролей
├── commercial-api-server.js      # Отдельный API сервер
├── docker-compose.yml            # Docker конфигурация
└── README.md                     # Документация
```

## 🛠️ Разработка

### Локальный запуск
```bash
# Запуск MongoDB
docker run -d --name mongodb-local -p 27017:27017 mongo

# Запуск Commercial API
node commercial-api-server.js

# В другом терминале - запуск LibreChat
npm run backend:dev
```

### Тестирование API
```bash
# Проверка здоровья сервера
curl http://localhost:3000/health

# Получение ролей
curl http://localhost:3000/api/admin/roles

# Получение пользователей
curl http://localhost:3000/api/admin/users
```

## 📖 Документация

- [API Documentation](API_DOCUMENTATION.md) - Полное описание API endpoints
- [Commercial Features](COMMERCIAL_FEATURES_README.md) - Детальное описание коммерческих функций
- [LibreChat Docs](https://docs.librechat.ai/) - Официальная документация LibreChat

## 🤝 Вклад в проект

1. Fork репозитория
2. Создайте feature branch (`git checkout -b feature/amazing-feature`)
3. Commit изменения (`git commit -m 'Add amazing feature'`)
4. Push в branch (`git push origin feature/amazing-feature`)
5. Откройте Pull Request

## 📄 Лицензия

Этот проект основан на [LibreChat](https://github.com/danny-avila/LibreChat) и распространяется под лицензией ISC.

## 🙏 Благодарности

- [LibreChat Team](https://github.com/danny-avila/LibreChat) - за отличную основу
- Сообщество LibreChat - за вдохновение и поддержку

## 📞 Поддержка

Если у вас есть вопросы или предложения:

- Создайте [Issue](https://github.com/dancha24/MetoGPT/issues)
- Напишите в [Discussions](https://github.com/dancha24/MetoGPT/discussions)

---

**⭐ Если проект вам понравился, поставьте звездочку!**

**🚀 MetoGPT - LibreChat для бизнеса**
