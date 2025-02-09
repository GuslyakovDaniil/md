# API Documentation

## Общие замечания

*   Все запросы к API должны отправляться на базовый URL: `${baseUrl()}` (см. admin.js, обычно это \`http://localhost:3000\`).
*   Для изменения данных (POST, PUT, DELETE) необходимо отправлять запросы с заголовком \`Content-Type: application/json\`.
*   В ответах, в случае успеха, возвращается HTTP-код 200 (OK) или 201 (Created) для POST-запросов.  В случае ошибки возвращаются коды 4xx или 5xx с описанием проблемы в теле ответа.
*   Большинство запросов возвращают данные в формате JSON.
*   Для корректной работы с координатами, используется система координат WGS 84 (EPSG:4326).  В базе данных координаты хранятся в формате GEOMETRY(Point, 4326).

## 1. Получение всех объектов

**Endpoint:** `get-objects`

**Method:** `GET`

**Описание:** Возвращает все объекты из базы данных в формате GeoJSON FeatureCollection.

**Request:**

```
GET /get-objects
```

**Response (Success - 200 OK):**

\`\`\`json
{
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "geometry": {
                "type": "Point",
                "coordinates": [
                    "longitude",
                    "latitude"
                ]
            },
            "properties": {
                "object_id": "object_id",
                "iconCaption": "object_name",
                "marker-color": "icon"
            }
        },
        ...
    ]
}
\`\`\`

Описание структуры GeoJSON:

*   \`type\`: \`"FeatureCollection"\` - Тип объекта GeoJSON (FeatureCollection).
*   \`features\`: Массив объектов Feature, каждый из которых представляет собой отдельный объект.
    *   \`type\`: \`"Feature"\` - Тип объекта GeoJSON (Feature).
    *   \`geometry\`: Геометрические данные объекта.
        *   \`type\`: \`"Point"\` - Тип геометрии (точка).
        *   \`coordinates\`: Массив, содержащий долготу и широту точки \`[longitude, latitude]\`.
    *   \`properties\`: Свойства объекта.
        *   \`object_id\`: Уникальный идентификатор объекта в базе данных.
        *   \`iconCaption\`: Название объекта.
        *   \`marker-color\`: Цвет маркера, используемого для отображения объекта на карте.

**Response (Error - 500 Internal Server Error):**

\`\`\`json
{
    "error": "Error fetching data from database."
}
\`\`\`

## 2. Получение содержимого GeoJSON файла

**Endpoint:** \`/geojson/:geojson_filename\`

**Method:** \`GET\`

**Описание:** Возвращает содержимое GeoJSON файла с указанным именем. Файл должен находиться в директории \`geojson\` на сервере.

**Request:**

\`\`\`
GET /geojson/ТИГР.geojson
\`\`\`

**Response (Success - 200 OK):**

Содержимое файла \`ТИГР.geojson\` (например):

\`\`\`json
{
    "type": "FeatureCollection",
    "features": [
        ...
    ]
}
\`\`\`

**Response (Error - 404 Not Found):**

Если файл не найден, возвращается стандартная страница 404 Not Found от express.

## 3. CRUD операции для типов объектов (Categories)

**Endpoint:** \`/objectTypes\`

### 3.1. Получение всех типов объектов

**Method:** \`GET\`

**Описание:** Возвращает список всех типов объектов (категорий) из таблицы \`Categories\`.

**Request:**

\`\`\`
GET /objectTypes
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
[
    {
        "object_type_id": 1,
        "object_type_name": "Порт"
    },
    {
        "object_type_id": 2,
        "object_type_name": "Станция"
    },
    ...
]
\`\`\`

### 3.2. Создание нового типа объекта

**Method:** \`POST\`

**Описание:** Создает новый тип объекта (категорию) в таблице \`Categories\`.

**Request:**

\`\`\`
POST /object-types
Content-Type: application/json

\`\`\`json
{
    "object_type_name": "Новый Тип"
}
\`\`\`

**Response (Success - 201 Created):**

\`\`\`json
{
    "object_type_id": 3,
    "object_type_name": "Новый Тип"
}
\`\`\`

### 3.3. Удаление типа объекта

**Method:** \`DELETE\`

**Описание:** Удаляет тип объекта (категорию) из таблицы \`Categories\`.

**Request:**

\`\`\`
DELETE /object-types/:id
\`\`\`

Example:

\`\`\`
DELETE /object-types/3
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
{
    "message": "Object type deleted"
}
\`\`\`

**Response (Error - 404 Not Found):**

\`\`\`json
{
    "message": "Object type not found"
}
\`\`\`

### 3.4. Обновление типа объекта

**Method:** \`PUT\`

**Описание:** Обновляет существующий тип объекта (категорию) в таблице \`Categories\`.

**Request:**

\`\`\`
PUT /object-types/:id
Content-Type: application/json

\`\`\`json
{
    "object_type_name": "Измененный Тип"
}
\`\`\`

Example:

\`\`\`
PUT /object-types/1
Content-Type: application/json

\`\`\`json
{
    "object_type_name": "Измененный Тип"
}
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
{
    "object_type_id": 1,
    "object_type_name": "Измененный Тип"
}
\`\`\`

**Response (Error - 404 Not Found):**

\`\`\`json
{
    "message": "Object type not found"
}
\`\`\`

## 4. CRUD операции для параметров (Parameter_definitions)

**Endpoint:** \`/parameters\`

### 4.1. Получение всех параметров

**Method:** \`GET\`

**Описание:** Возвращает список всех параметров из таблицы \`Parameter_definitions\`.

**Request:**

\`\`\`
GET /parameters
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
[
    {
        "parameter_id": 1,
        "parameter_name": "Максимальная длина судов",
        "type": "numeric",
        "unit": "м"
    },
    {
        "parameter_id": 2,
        "parameter_name": "Список производимых станцией операций",
        "type": "list",
        "unit": null
    },
    ...
]
\`\`\`

### 4.2. Создание нового параметра

**Method:** \`POST\`

**Описание:** Создает новый параметр в таблице \`Parameter_definitions\`.

**Request:**

\`\`\`
POST /parameters
Content-Type: application/json
\`\`\`

\`\`\`json
{
    "parameter_name": "Новый Параметр",
    "type": "text",
    "unit": "ед."
}
\`\`\`

**Response (Success - 201 Created):**

\`\`\`json
{
    "parameter_id": 3,
    "parameter_name": "Новый Параметр",
    "type": "text",
    "unit": "ед."
}
\`\`\`

### 4.3. Удаление параметра

**Method:** \`DELETE\`

**Описание:** Удаляет параметр из таблицы \`Parameter_definitions\`.

**Request:**

\`\`\`
DELETE /parameters/:id
\`\`\`

Example:

\`\`\`
DELETE /parameters/3
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
{
    "message": "Parameter deleted"
}
\`\`\`

**Response (Error - 404 Not Found):**

\`\`\`json
{
    "message": "Parameter not found"
}
\`\`\`

### 4.4. Обновление параметра

**Method:** \`PUT\`

**Описание:** Обновляет существующий параметр в таблице \`Parameter_definitions\`.

**Request:**

\`\`\`
PUT /parameters/:id
Content-Type: application/json
\`\`\`

\`\`\`json
{
    "parameter_name": "Измененный Параметр",
    "type": "numeric",
    "unit": "тонн"
}
\`\`\`

Example:

\`\`\`
PUT /parameters/1
Content-Type: application/json
\`\`\`

\`\`\`json
{
    "parameter_name": "Измененный Параметр",
    "type": "numeric",
    "unit": "тонн"
}
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
{
    "parameter_id": 1,
    "parameter_name": "Измененный Параметр",
    "type": "numeric",
    "unit": "тонн"
}
\`\`\`

**Response (Error - 404 Not Found):**

\`\`\`json
{
    "message": "Parameter not found"
}
\`\`\`

## 5. CRUD операции для объектов (Objects)

**Endpoint:** \`/objects\`

### 5.1. Получение всех объектов (только ID, name, geometry, icon)

**Method:** \`GET\`

**Описание:** Возвращает список всех объектов из таблицы \`Objects\` с ограниченным набором полей (id, name, geometry, icon). Для получения полной информации об объекте, включая параметры, используйте другие эндпоинты.

**Request:**

\`\`\`
GET /objects
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
[
    {
        "object_id": 1,
        "object_name": "Причал №1",
        "longitude": 37.12345,
        "latitude": 55.67890,
        "object_marker_color": "#FF0000"
    },
    {
        "object_id": 2,
        "object_name": "Станция Сортировочная",
        "longitude": 38.98765,
        "latitude": 54.32109,
        "object_marker_color": "#00FF00"
    },
    ...
]
\`\`\`

### 5.2. Создание нового объекта

**Method:** \`POST\`

**Описание:** Создает новый объект в таблице \`Objects\`.

**Request:**

\`\`\`
POST /objects
Content-Type: application/json
\`\`\`

\`\`\`json
{
    "object_name": "Новый Объект",
    "longitude": 39.11122,
    "latitude": 56.33344,
    "object_marker_color": "#0000FF"
}
\`\`\`

**Response (Success - 201 Created):**

\`\`\`json
{
    "object_id": 3,
    "object_name": "Новый Объект",
    "longitude": 39.11122,
    "latitude": 56.33344,
    "object_marker_color": "#0000FF"
}
\`\`\`

### 5.3. Удаление объекта

**Method:** \`DELETE\`

**Описание:** Удаляет объект из таблицы \`Objects\`. Внимание: Это удалит и все связи с категориями и значениями параметров для этого объекта.

**Request:**

\`\`\`
DELETE /objects/:id
\`\`\`

Example:

\`\`\`
DELETE /objects/3
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
{
    "message": "Object deleted"
}
\`\`\`

**Response (Error - 404 Not Found):**

\`\`\`json
{
    "message": "Object not found"
}
\`\`\`

### 5.4. Обновление объекта

**Method:** \`PUT\`

**Описание:** Обновляет существующий объект в таблице \`Objects\`. Можно обновить имя, координаты и цвет маркера.

**Request:**

\`\`\`
PUT /objects/:id
Content-Type: application/json
\`\`\`

\`\`\`json
{
    "object_name": "Измененный Объект",
    "longitude": 40.55566,
    "latitude": 57.77788,
    "object_marker_color": "#AAAAAA"
}
\`\`\`

Example:

\`\`\`
PUT /objects/1
Content-Type: application/json
\`\`\`

\`\`\`json
{
    "object_name": "Измененный Объект",
    "longitude": 40.55566,
    "latitude": 57.77788,
    "object_marker_color": "#AAAAAA"
}
\`\`\`

Важное замечание о longitude и latitude при PUT запросе:
Если longitude и latitude не указаны или имеют значение null, то они не изменяются. Для сброса значений координат нужно передать null.

**Response (Success - 200 OK):**

\`\`\`json
{
    "object_id": 1,
    "object_name": "Измененный Объект",
    "longitude": 40.55566,
    "latitude": 57.77788,
    "object_marker_color": "#AAAAAA"
}
\`\`\`

**Response (Error - 404 Not Found):**

\`\`\`json
{
    "message": "Object not found"
}
\`\`\`

## 6. Операции для связей объектов и типов (Objects_categories)

**Endpoint:** \`/objectsTypesRelation\`

### 6.1. Получение всех связей объектов и типов

**Method:** \`GET\`

**Описание:** Возвращает список всех связей между объектами и типами объектов (категориями).

**Request:**

\`\`\`
GET /objectsTypesRelation
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
[
    {
        "object_id": 1,
        "object_type_id": 1,
        "object_type_name": "Порт",
        "object_name": "Причал №1"
    },
    {
        "object_id": 2,
        "object_type_id": 2,
        "object_type_name": "Станция",
        "object_name": "Станция Сортировочная"
    },
    ...
]
\`\`\`

### 6.2. Создание новой связи объекта и типа

**Method:** \`POST\`

**Описание:** Создает новую связь между объектом и типом объекта (категорией).

**Request:**

\`\`\`
POST /objects-types-relation
Content-Type: application/json
\`\`\`

\`\`\`json
{
    "object_id": 3,
    "object_type_id": 2
}
\`\`\`

**Response (Success - 201 Created):**

\`\`\`json
{
    "object_id": 3,
    "category_id": 2
}
\`\`\`

### 6.3. Удаление связи объекта и типа

**Method:** \`DELETE\`

**Описание:** Удаляет связь между объектом и типом объекта (категорией).

**Request:**

\`\`\`
DELETE /objects-types-relation/:object_id/:object_type_id
\`\`\`

Example:

\`\`\`
DELETE /objects-types-relation/3/2
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
{
    "message": "Relation deleted"
}
\`\`\`

**Response (Error - 404 Not Found):**

\`\`\`json
{
    "message": "Relation not found"
}
\`\`\`

### 6.4. Обновление связи объекта и типа

**Method:** \`PUT\`

**Описание:** Обновляет существующую связь между объектом и типом объекта (категорией).

**Request:**

\`\`\`
PUT /objects-types-relation/:object_id/:object_type_id
Content-Type: application/json
\`\`\`

\`\`\`json
{
    "new_object_id": 4,
    "new_object_type_id": 1
}
\`\`\`

Example:

\`\`\`
PUT /objects-types-relation/3/2
Content-Type: application/json
\`\`\`

\`\`\`json
{
    "new_object_id": 4,
    "new_object_type_id": 1
}
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
{
    "object_id": 4,
    "category_id": 1
}
\`\`\`

**Response (Error - 404 Not Found):**

\`\`\`json
{
    "message": "Relation not found"
}
\`\`\`

## 7. Работа с параметрами конкретного объекта

**Endpoint:** \`/object/:objectId/parameters\`

### 7.1. Получение параметров объекта

**Method:** \`GET\`

**Описание:** Возвращает список параметров, связанных с конкретным объектом.

**Request:**

\`\`\`
GET /object/:objectId/parameters
\`\`\`

Example:

\`\`\`
GET /object/1/parameters
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
[
    {
        "parameter_value_id": 1,
        "parameter_definition_id": 1,
        "parameter_name": "Максимальная длина судов",
        "units": "м",
        "parameter_type": "numeric",
        "value_text": null,
        "value_numeric": 250,
        "navigation_start_date": null,
        "navigation_end_date": null
    },
    {
        "parameter_value_id": 2,
        "parameter_definition_id": 2,
        "parameter_name": "Список производимых станцией операций",
        "units": null,
        "parameter_type": "list",
        "value_text": "Погрузка, Разгрузка",
        "value_numeric": null,
        "navigation_start_date": null,
        "navigation_end_date": null
    },
    ...
]
\`\`\`

### 7.2. Обновление/Создание параметров объекта

**Method:** \`PUT\`

**Описание:** Обновляет значения существующих параметров объекта или создает новые, если они еще не существуют.

**Request:**

\`\`\`
PUT /object/:objectId/parameters
Content-Type: application/json
\`\`\`

\`\`\`json
[
    {
        "parameter_definition_id": 1,
        "parameter_value_id": 1,
        "value_numeric": 270,
        "parameter_type": "numeric"
    },
    {
        "parameter_definition_id": 2,
        "parameter_value_id": 2,
        "value_text": "Погрузка, Разгрузка, Сортировка",
        "parameter_type": "list"
    },
   {
        "parameter_definition_id": 3,
        "parameter_value_id": null,
        "value_text": "какой то текст",
        "parameter_type": "text"
    },
   {
        "parameter_definition_id": 4,
        "parameter_value_id": null,
        "navigation_start_date": "2024-01-01",
        "navigation_end_date": "2024-01-31",
        "parameter_type": "date"
    }
]
\`\`\`

Example:

\`\`\`
PUT /object/1/parameters
Content-Type: application/json
\`\`\`

\`\`\`json
[
    {
        "parameter_definition_id": 1,
        "parameter_value_id": 1,
        "value_numeric": 270,
        "parameter_type": "numeric"
    },
    {
        "parameter_definition_id": 2,
        "parameter_value_id": 2,
        "value_text": "Погрузка, Разгрузка, Сортировка",
        "parameter_type": "list"
    },
    {
        "parameter_definition_id": 3,
        "parameter_value_id": null,
        "value_text": "какой то текст",
        "parameter_type": "text"
    },
   {
        "parameter_definition_id": 4,
        "parameter_value_id": null,
        "navigation_start_date": "2024-01-01",
        "navigation_end_date": "2024-01-31",
        "parameter_type": "date"
    }
]
\`\`\`

Описание структуры запроса:

*   Массив объектов, каждый из которых представляет собой параметр для обновления/создания.

    *   \`parameter_definition_id\`: ID определения параметра (из таблицы Parameter_definitions). Обязательное поле.
    *   \`parameter_value_id\`: ID значения параметра (из таблицы parameter_values). Если null, то создается новая запись.
    *   \`value_text\`: Текстовое значение параметра (для parameter_type: text или list).
    *   \`value_numeric\`: Числовое значение параметра (для parameter_type: numeric).
    *   \`navigation_start_date\`: Начальная дата (для parameter_type: date). Формат: YYYY-MM-DD.
    *   \`navigation_end_date\`: Конечная дата (для parameter_type: date). Формат: YYYY-MM-DD.
    *   \`parameter_type\`: Тип параметра (text, numeric, list, date). Обязательное поле.

**Response (Success - 200 OK):**

\`\`\`json
{
    "message": "Object parameters updated successfully"
}
\`\`\`

### 7.3. Удаление значения параметра у объекта

**Method:** \`DELETE\`

**Описание:** Удаляет значение параметра для конкретного объекта.

**Request:**

\`\`\`
DELETE /object/:objectId/parameter/:parameterDefinitionId
\`\`\`

Example:

\`\`\`
DELETE /object/1/parameter/2
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
{
    "message": "Parameter value deleted successfully"
}
\`\`\`

**Response (Error - 404 Not Found):**

\`\`\`json
{
    "message": "Parameter value not found"
}
\`\`\`

## 8. Получение списка определений параметров

**Endpoint:** \`/parameter-definitions\`

**Method:** \`GET\`

**Описание:** Возвращает список всех определений параметров (из таблицы Parameter_definitions). Используется для заполнения выпадающих списков при добавлении новых параметров к объектам.

**Request:**

\`\`\`
GET /parameter-definitions
\`\`\`

**Response (Success - 200 OK):**

\`\`\`json
[
    {
        "parameter_definition_id": 1,
        "parameter_name": "Максимальная длина судов",
        "parameter_type": "numeric",
        "units": "м"
    },
    {
        "parameter_definition_id": 2,
        "parameter_name": "Список производимых станцией операций",
        "parameter_type": "list",
        "units": null
    },
    ...
]
\`\`\`

## 9. Фильтрация данных (Не реализовано)

Описание: Функциональность фильтрации не реализована в предоставленном коде. Однако, вот примерная структура API для фильтрации:

**Endpoint:** \`/objects/filter\`

**Method:** \`POST\`

**Описание:** Возвращает список объектов, соответствующих заданным критериям фильтрации.

**Request:**

\`\`\`
POST /objects/filter
Content-Type: application/json
\`\`\`

\`\`\`json
{
    "object_type_id": 1,
    "parameter_id": 2,
    "parameter_value": "Погрузка"
}
\`\`\`

Описание структуры запроса (пример):

*   \`object_type_id\`: ID типа объекта (категории) для фильтрации.
*   \`parameter_id\`: ID параметра для фильтрации.
*   \`parameter_value\`: Значение параметра для фильтрации.

**Response (Success - 200 OK):**

\`\`\`json
[
    {
        "object_id": 1,
        "object_name": "Причал №1",
        "longitude": 37.12345,
        "latitude": 55.67890,
        "object_marker_color": "#FF0000"
    },

]
\`\`\`