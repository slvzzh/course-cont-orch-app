# 1. Базовый образ (рекомендуем: python:3.11-slim)
FROM python:3.11-slim
# 2. Метаданные - ОБЯЗАТЕЛЬНО укажите себя как мейнтейнера!
LABEL maintainer="<karpov>@lab.local"
LABEL version="1.0"
ENV PORT=5462
# 3. Рабочая директория
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 5462
# 8. Запуск приложения с вашим портом
CMD uvicorn app.main:app --host 0.0.0.0 --port ${PORT}