# Timescale Processing Test Assignment

[Русский](#ru) | [English](#en)

<span id="ru"></span>
## Русский

### Описание

Timescale Processing - это WebAPI приложение для работы с результатами обработки из CSV-файлов. Приложение принимает CSV-файл, выполняет валидацию, сохраняет данные в PostgreSQL, рассчитывает интегральные показатели и предоставляет методы для получения сохраненных результатов.

### Возможности

- Загрузка CSV-файла через REST API.
- Валидация данных файла.
- Сохранение строк CSV в таблицу Values.
- Расчет интегральных результатов и сохранение в таблицу Results.
- Перезапись данных, если файл с таким именем уже существует.
- Получение списка результатов с фильтрами.
- Получение последних 10 значений по имени файла.
- Swagger для документации API.
- SPA для вызова методов API и просмотра результатов.
- Запуск через Docker Compose.

### Формат CSV

CSV-файл должен иметь заголовок и разделитель `;`.

Пример:

```csv
Date;ExecutionTime;Value
2026-08-08T10:15:30.0000Z;12.5;3.14
2026-08-08T10:16:00.0000Z;7.25;5.55
```

Поля:

| Поле | Тип | Описание |
|---|---|---|
| Date | datetime | Время начала операции |
| ExecutionTime | number | Время выполнения в секундах |
| Value | number | Показатель в виде числа с плавающей запятой |

### Валидация

Файл считается невалидным, если выполняется хотя бы одно условие:

- Дата позже текущего времени.
- Дата раньше 2000-01-01.
- Время выполнения меньше 0.
- Значение показателя меньше 0.
- Количество строк меньше 1.
- Количество строк больше 10000.
- Значение не соответствует ожидаемому типу.
- В записи отсутствует одно из значений.

Если файл невалидный, изменения не сохраняются, транзакция откатывается, пользователю возвращается ошибка.

### Расчет результатов

После успешной обработки файла рассчитываются следующие показатели:

| Показатель | Описание |
|---|---|
| Дельта времени Date в секундах | Максимальное Date минус минимальное Date |
| Минимальная дата и время | Момент запуска первой операции |
| Среднее время выполнения | Среднее значение ExecutionTime |
| Среднее значение показателя | Среднее значение Value |
| Медиана показателя | Медиана по значениям Value |
| Максимальное значение показателя | Максимальное значение Value |
| Минимальное значение показателя | Минимальное значение Value |

Результаты сохраняются в таблицу Results.

### API

| Метод | Маршрут                             | Описание                                       |
| ----- | ----------------------------------- | ---------------------------------------------- |
| POST  | /api/files/import                   | Загрузка и обработка CSV-файла                 |
| GET   | /api/results                        | Получение результатов с фильтрами              |
| GET   | /api/files/{fileName}/values/latest | Получение последних 10 значений по имени файла |
|       |                                     |                                                |

#### Загрузка CSV

```http
POST /api/files/import
```

Запрос выполняется как `multipart/form-data` и содержит файл.

#### Получение результатов

```http
GET /api/results
```

#### Получение последних значений

```http
GET /api/files/{fileName}/values/latest
```

Пример:

```http
GET /api/files/data.csv/values/latest
```

Метод возвращает последние 10 значений, отсортированных по полю Date.

### Технологии

Backend:

- .NET 8
- ASP.NET Core Web API
- EF Core
- PostgreSQL
- Swagger

Frontend:

- Angular 21

Инфраструктура:

- Docker
- Docker Compose

### Структура проекта

```text
.
├── src/
│   ├── Api/
│   ├── Application/
│   ├── Domain/
│   └── Infrastructure/
├── tests/
│   ├── UnitTests/
│   └── IntegrationTests/
├── spa/
├── Dockerfile.api
├── Dockerfile.spa
├── docker-compose.yml
└── README.md
```

### Запуск

Клонирование репозитория:

```bash
git clone https://github.com/username/timescale-processing.git
cd timescale-processing
```

Запуск через Docker Compose:

```bash
docker compose up --build
```

Сервисы:

| Сервис | Описание | Адрес |
|---|---|---|
| db | PostgreSQL | localhost:5432 |
| api | WebAPI | http://localhost:8080 |
| spa | Angular SPA | http://localhost:4200 |

Swagger:

```text
http://localhost:8080/swagger
```

### Тесты

Запуск тестов:

```bash
dotnet test
```

### SPA

SPA позволяет:

- Загружать CSV-файл.
- Просматривать результат загрузки.
- Просматривать ошибки валидации.
- Просматривать список результатов.
- Фильтровать результаты.
- Просматривать последние 10 значений по выбранному файлу.

Локальный запуск SPA:

```bash
cd spa
npm install
npm start
```

После запуска SPA будет доступен по адресу:

```text
http://localhost:4200
```

<span id="en"></span>
## English

### Description

Timescale Processing is a WebAPI application for working with processing results stored in CSV files. The application receives a CSV file, validates it, stores data in PostgreSQL, calculates summary metrics, and provides endpoints for retrieving saved results.

### Features

- CSV file upload through REST API.
- File data validation.
- Saving CSV rows to the Values table.
- Calculating summary results and saving them to the Results table.
- Overwriting data if a file with the same name already exists.
- Retrieving results with filters.
- Retrieving the last 10 values by file name.
- Swagger API documentation.
- SPA for calling API methods and viewing results.
- Docker Compose support.

### CSV Format

The CSV file must contain a header and use `;` as a delimiter.

Example:

```csv
Date;ExecutionTime;Value
2026-08-08T10:15:30.0000Z;12.5;3.14
2026-08-08T10:16:00.0000Z;7.25;5.55
```

Fields:

| Field | Type | Description |
|---|---|---|
| Date | datetime | Operation start time |
| ExecutionTime | number | Execution time in seconds |
| Value | number | Floating point metric value |

### Validation

The file is invalid if at least one of the following conditions is true:

- Date is later than the current time.
- Date is earlier than 2000-01-01.
- Execution time is less than 0.
- Value is less than 0.
- Row count is less than 1.
- Row count is greater than 10000.
- A value does not match the expected type.
- A record is missing one of the values.

If the file is invalid, changes are not saved, the transaction is rolled back, and an error is returned to the user.

### Calculated Results

After successful file processing, the following metrics are calculated:

| Metric | Description |
|---|---|
| Date delta in seconds | Maximum Date minus minimum Date |
| Minimum date and time | First operation start time |
| Average execution time | Average value of ExecutionTime |
| Average value | Average value of Value |
| Median value | Median of Value |
| Maximum value | Maximum value of Value |
| Minimum value | Minimum value of Value |

The results are stored in the Results table.

### API

| Method | Route | Description |
|---|---|---|
| POST | /api/files/import | Upload and process a CSV file |
| GET | /api/results | Get results with filters |
| GET | /api/files/{fileName}/values/latest | Get the last 10 values by file name |

#### Upload CSV

```http
POST /api/files/import
```

The request is sent as `multipart/form-data` and contains a file.

#### Get Results

```http
GET /api/results
```

#### Get Last Values

```http
GET /api/files/{fileName}/values/latest
```

Example:

```http
GET /api/files/data.csv/values/latest
```

The endpoint returns the last 10 values sorted by Date.

### Technologies

Backend:

- .NET 8
- ASP.NET Core Web API
- EF Core
- PostgreSQL
- Swagger

Frontend:

- Angular 21

Infrastructure:

- Docker
- Docker Compose

### Project Structure

```text
.
├── src/
│   ├── Api/
│   ├── Application/
│   ├── Domain/
│   └── Infrastructure/
├── tests/
│   ├── UnitTests/
│   └── IntegrationTests/
├── spa/
├── Dockerfile.api
├── Dockerfile.spa
├── docker-compose.yml
└── README.md
```

### Running

Clone the repository:

```bash
git clone https://github.com/username/timescale-processing.git
cd timescale-processing
```

Run with Docker Compose:

```bash
docker compose up --build
```

Services:

| Service | Description | Address |
|---|---|---|
| db | PostgreSQL | localhost:5432 |
| api | WebAPI | http://localhost:8080 |
| spa | Angular SPA | http://localhost:4200 |

Swagger:

```text
http://localhost:8080/swagger
```

### Tests

Run tests:

```bash
dotnet test
```

### SPA

The SPA allows users to:

- Upload a CSV file.
- View upload result.
- View validation errors.
- View the list of results.
- Filter results.
- View the last 10 values for a selected file.

Run SPA locally:

```bash
cd spa
npm install
npm start
```

After startup, the SPA is available at:

```text
http://localhost:4200
```