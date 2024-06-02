# Builder stage. Lighter, but need some additional packages for psycopg2 build
FROM python:3.10-slim as builder

RUN apt-get update && \
    apt-get install -y \
    libpq-dev \
    gcc # gcc will be deleted after build

# Create the virtual env that will be copied in Main stage
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# Install dependencies
COPY requirements.txt requirements.txt
RUN pip install --upgrade pip && \
    pip install --no-cache-dir -r requirements.txt

# Main stage
FROM python:3.10-slim

RUN apt-get update && \
    apt-get install -y \
    libpq-dev && \
    rm -rf /var/lib/apt/lists/*

# Copy the virtual env from Builder stage
COPY --from=builder /opt/venv /opt/venv

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    APP_HOME="/home/app" \
    PATH="/opt/venv/bin:$PATH"

# Create the service account and run on it
RUN addgroup --system app && \
    adduser --shell /bin/false --disabled-password --system --group app && \
    mkdir -p $APP_HOME/media

WORKDIR $APP_HOME
# Copy project files
COPY ./django $APP_HOME
# chown all the files to the app user
RUN chown -R app:app $APP_HOME
# change to the app user
USER app

EXPOSE 8000

#CMD ["gunicorn", "digital_twin.wsgi:application", "--bind", "0.0.0.0:8000", "--workers=1"]