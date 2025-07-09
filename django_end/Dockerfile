# Use official Python image
FROM python:3.10-slim

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

# Set working directory
WORKDIR /app

# ✅ ติดตั้ง build dependencies สำหรับ mysqlclient
RUN apt-get update && apt-get install -y \
    gcc \
    pkg-config \
    default-libmysqlclient-dev \
    libmariadb-dev \
    python3-dev \
    libjpeg-dev \
    zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

# Install Python dependencies
COPY requirements.txt .
RUN pip install --upgrade pip && pip install -r requirements.txt

# Copy project files
COPY . .

# Create necessary directories
RUN mkdir -p /home/constructor/django_end/static
RUN mkdir -p /mnt/data/media

# ✅ สร้างสคริปต์เพื่อรัน migration และ collectstatic
RUN echo '#!/bin/bash\n\
echo "Waiting for database..."\n\
python manage.py makemigrations\n\
python manage.py migrate\n\
python manage.py collectstatic --noinput\n\
echo "Starting Gunicorn..."\n\
gunicorn django_end.wsgi:application --bind 0.0.0.0:8000' > /app/start.sh

RUN chmod +x /app/start.sh

EXPOSE 8000

CMD ["/app/start.sh"]