# О проекте
##  Сервис предоставляет дополнительный уровень защиты для критических действий пользователей.

## Функциональные возможности:
Инициация защищенных операций - пользователи могут создавать операции, требующие подтверждения

Генерация уникальных кодов - система автоматически создает одноразовые пароли для каждой операции

Мультиканальная доставка - поддержка нескольких способов отправки кодов:

СМС-сообщения (с использованием эмулятора для тестирования)

Электронная почта (работает как с тестовыми, так и с реальными адресами)

Telegram-бот (интеграция через Telegram Bot API)

Верификация кодов - проверка введенных пользователем кодов на валидность

Гибкие настройки - администратор может конфигурировать:

Срок действия кодов

Длину генерируемых паролей

В основе решения лежат принципы TOTP (Time-based One-Time Password), обеспечивающие высокий уровень безопасности.

## Техническая реализация
Технологический стек:
Backend: Spring Boot приложение

База данных: PostgreSQL 17 в Docker-контейнере

## Инфраструктура:

Тестовый SMTP-сервер (Docker)

Эмулятор SMPP-сервера (Docker)

Telegram Bot API (HTTP)

## Аутентификация:
JWT-токены (требуются для всех запросов)

Ролевая модель (обычные пользователи/администраторы)

## Запуск системы:
bash
docker-compose up --build -d
После запуска доступны:

Swagger UI: http://localhost:8080/swagger-ui/index.html#/

Spring Actuator: http://localhost:8080/actuator

PGAdmin: http://localhost:5050/browser/ (exam_db, admin/admin)

MailCatcher: http://localhost:1080/

SMPPSim: http://localhost:8884/

## Принцип работы
Инициализация:

Создание администратора по умолчанию (при первом запуске)

Загрузка базовой конфигурации

Фоновые процессы:

Планировщик проверяет просроченные коды каждую минуту

Рабочий процесс:

Регистрация/аутентификация пользователя

Получение JWT-токена (обязателен для всех запросов)

Создание OTP-кода для операции

Активация кода (до истечения срока действия)

Безопасность:

Коды хранятся в зашифрованном виде

Только администраторы могут назначать права

Пользователи работают только со своими кодами

## Архитектура приложения
Основные компоненты:
Конфигурация: otp.simple.project.backend.configuration

DTO: otp.simple.project.backend.domain.dto

Модели: otp.simple.project.backend.domain.model

Исключения: LogicException

Репозитории: otp.simple.project.backend.repository (DAO слой)

Сервисы: otp.simple.project.backend.service (бизнес-логика)

Уведомления: otp.simple.project.backend.service.notification (Email/SMS/Telegram)

Контроллеры: otp.simple.project.backend.rest (REST API)

Точка входа: SpringDataApplication

API endpoints
Аутентификация:
POST /auth/sign/up - регистрация (возвращает JWT)

POST /auth/sign/in - вход (возвращает JWT)

POST /auth/password/change - смена пароля

Управление пользователями:
PUT /user/edit - редактирование профиля

GET /user/all - список пользователей (требуются права admin)

PUT /user/{id}/set-admin - назначение администратора

DELETE /user/{id}/delete - удаление пользователя

OTP-операции:
PUT /otp/configuration/update - обновление настроек (admin)

GET /otp/configuration/get - получение настроек (admin)

POST /otp/create - создание OTP-кода

GET /otp/{id}/get - информация о коде

POST /otp/{id}/activate - активация кода

GET /otp/all - все OTP-коды (admin)

DELETE /otp/{id}/delete - удаление кода (admin)
