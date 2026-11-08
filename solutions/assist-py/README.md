# 🎓 Personal Assistant — учебный проект для практики Docker

Простое FastAPI-приложение, которое используется как основа для лабораторной работы по Docker.

Автор: Карпов Иван, ИСУ 466132
## 📁 Структура проекта

```
simple_example/
├── app/
│   └── main.py              # FastAPI-приложение
├── requirements.txt          # Python-зависимости
└── README.md
```

## 💻 Запуск

```bash
docker build -t karpov-assistant:final -f Dockerfile.optimized .
docker run -d \
--name karpov-assistant-final \
-p 5462:5462 \
-e APP_OWNER=karpov \
-e APP_SECRET=secret \
karpov-assistant:final
```

## 📡 Эндпоинты

| Метод | URL | Описание |
|-------|-----|----------|
| GET | `/` | Главная страница — приветствие и список эндпоинтов |
| GET | `/health` | Healthcheck — статус приложения |
| GET | `/docs` | Swagger UI — интерактивная документация (встроена в FastAPI) |
| GET | `/me` | Информация о владельце (из переменных окружения) |
| GET | `/secret/{key}` | Проверка секретного ключа — убедитесь, что `APP_SECRET` пробрасывается |
| GET | `/weather/{city}` | Текущая погода в городе через Open-Meteo API |
| POST | `/notes` | Создать заметку (тело: `{"text": "..."}`) |
| GET | `/notes` | Получить все заметки |

### Примеры запросов

```bash
# Главная
curl http://localhost:5462/

# Погода в Москве
curl http://localhost:5462/weather/Moscow

# Проверка секретного ключа
curl http://localhost:5462/secret/ваш_секретный_ключ

# Создать заметку
curl -X POST http://localhost:5462/notes \
  -H "Content-Type: application/json" \
  -d '{"text": "Моя первая заметка"}'

# Получить заметки
curl http://localhost:5462/notes
```

## 🔑 Переменные окружения

| Переменная | Описание | Значение по умолчанию |
|------------|----------|-----------------------|
| `APP_OWNER` | Имя владельца приложения | `anonymous` |
| `APP_SECRET` | Секретный ключ для эндпоинта `/secret/{key}` | `default_secret` |


## 🌤 API погоды — важно для пользователей из РФ

Эндпоинт `/weather/{city}` обращается к открытому API **[Open-Meteo](https://open-meteo.com/)** (бесплатный, без ключа):

1. **Геокодинг** — `geocoding-api.open-meteo.com` — получает координаты города
2. **Прогноз** — `api.open-meteo.com` — получает текущую погоду по координатам

### ⚠️ Блокировки РКН

Домены `open-meteo.com` могут быть недоступны из российских сетей из-за блокировок РКН.

Если эндпоинт `/weather/{city}` возвращает ошибку таймаута — **необходимо использовать средства обхода блокировок:**

- **VPN** — включите VPN на машине, где запущен Docker

## ✅ Как проверить, что всё работает

1. `curl http://localhost:5462/` — должен вернуть приветствие с вашим именем
2. `curl http://localhost:5462/me` — должен показать `secret_prefix` (первые 6 символов вашего ключа)
3. `curl http://localhost:5462/secret/ваш_ключ` — должен вернуть `LAB2_SUCCESS_ВАШЕИМЯ`
4. `curl http://localhost:5462/weather/London` — должен вернуть температуру, влажность, ветер
5. `docker inspect --format='{{.State.Health.Status}}' personal-assistant` — должен вернуть `healthy`

