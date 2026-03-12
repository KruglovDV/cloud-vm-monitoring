# Cloud VM Monitoring

Docker Compose конфигурация для отправки метрик с виртуальной машины в [Cloud.ru](https://cloud.ru) через [vmagent](https://docs.victoriametrics.com/vmagent/) (VictoriaMetrics).

vmagent скрейпит метрики с указанных таргетов и отправляет их в Cloud.ru Monitoring API с OAuth2-авторизацией.

## Требования

- Docker Compose v2.23.1+
- Приложение должно отдавать метрики в формате Prometheus по эндпоинту `http://<host>:<port>/metrics`

## Быстрый старт

1. Скопируйте файл с переменными окружения и заполните его:

```bash
cp .env.example .env
```

2. Заполните `.env` (см. [Переменные окружения](#переменные-окружения))

3. Проверьте конфиг (dry run):

```bash
docker compose run --rm vmagent \
  -promscrape.config=/etc/prometheus/prometheus.yml \
  -dryRun
```

4. Запустите:

```bash
docker compose up -d
```

## Переменные окружения

| Переменная | Обязательная | Описание |
|---|---|---|
| `PROJECT_ID` | да | ID проекта в Cloud.ru |
| `CLIENT_ID` | да | Client ID сервисного аккаунта для OAuth2-авторизации |
| `CLIENT_SECRET` | да | Client Secret сервисного аккаунта |
| `JOB_NAME` | нет | Имя job в конфиге Prometheus (по умолчанию `vmagent`) |
| `SCRAPE_TARGETS` | нет | Адрес таргета для скрейпинга метрик (по умолчанию `localhost:8429`) |
| `SCRAPE_INTERVAL` | нет | Интервал сбора метрик (по умолчанию `10s`) |

`PROJECT_ID`, `CLIENT_ID` и `CLIENT_SECRET` можно получить в консоли Cloud.ru.

## Проверка работы

Веб-интерфейс vmagent доступен на порту `8429`:

- `http://<ip>:8429/targets` — статус таргетов (UP/DOWN)
- `http://<ip>:8429/service-discovery` — отладка обнаружения таргетов
- `http://<ip>:8429/metrics` — собственные метрики vmagent

Проверка отправки метрик в Cloud.ru:

```bash
curl -s http://localhost:8429/metrics | grep vmagent_remotewrite
```

Если счётчик `vmagent_remotewrite_requests_total` растёт и нет `_errors_total` — данные успешно отправляются.

## Документация

- [Отправка метрик через vmagent в Cloud.ru](https://cloud.ru/docs/service-monitoring/ug/topics/guides__metrics__send__vmagent?source-platform=Evolution)
- [vmagent — VictoriaMetrics](https://docs.victoriametrics.com/vmagent/)

---

> Проект создан с помощью [Claude Code](https://claude.ai/code)
