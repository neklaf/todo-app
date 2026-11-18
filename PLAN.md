# Aplicacion de Tareas

## 1. Descripción del Proyecto
Aplicación web completa (Full-Stack) para la gestión de tareas (TODO List) que implementa operaciones CRUD completas. El objetivo es mantener un código limpio, modular, escalable y siguiendo estrictas buenas prácticas de ingeniería de software.

## 2. Stack Tecnológico
- **Backend:** Node.js con Express.js.
- **Base de Datos:** SQLite 3 (persistencia local en archivo).
- **Query Builder:** Knex.js (para migraciones, configuración y consultas SQL seguras).
- **Frontend:** Vanilla JavaScript (ES6+), HTML5 semántico y CSS3 moderno. **Estrictamente prohibido usar frameworks frontend (No React, No Vue, No Angular, No Bootstrap).**
- **Testing:** Jest y Supertest para pruebas funcionales automáticas de la API.

## 3. Arquitectura y Estructura del Proyecto
El backend seguirá una arquitectura en capas (Layered Architecture) para separar responsabilidades:

```text
todo-app/
├── src/
│   ├── config/
│   │   └── db.js            # Instancia y conexión de Knex
│   ├── routes/
│   │   └── task.routes.js   # Definición de endpoints de la API
│   ├── controllers/
│   │   └── task.controller.js # Manejo de Request/Response y códigos HTTP
│   ├── services/
│   │   └── task.service.js  # Lógica de negocio y llamadas a la BD con Knex
│   ├── middlewares/
│   │   └── error.handler.js # Manejo centralizado de errores
│   ├── public/              # FRONTEND (Servido estáticamente)
│   │   ├── index.html
│   │   ├── css/styles.css
│   │   └── js/main.js       # Lógica Fetch API y manipulación del DOM
│   ├── app.js               # Configuración de Express (exportable para tests)
│   └── server.js            # Punto de entrada (app.listen)
├── db/
│   ├── migrations/          # Archivos de migración de Knex
│   └── database.sqlite      # Archivo de BD (ignorado en producción/tests)
├── tests/
│   └── tasks.test.js        # Pruebas funcionales automáticas de los endpoints
├── knexfile.js              # Configuración de entornos de BD (dev, test)
├── package.json
└── PLAN.md
```

## 4. Diseño de Base de Datos (Knex.js y SQLite3)
Se utilizarán migraciones de Knex para crear la tabla `tasks` con la siguiente estructura:
- `id`: Integer, Primary Key, Auto-incremental (`table.increments('id')`).
- `title`: String, No nulo, obligatorio (`table.string('title').notNullable()`).
- `description`: Text, Opcional (`table.text('description')`).
- `completed`: Integer (`0` o `1`). **Nota técnica:** Dado que SQLite3 no tiene tipo booleano nativo, se definirá como entero con valor por defecto `0` (`table.integer('completed').defaultTo(0)`).
- `created_at`: Timestamp, Automático (`table.timestamp('created_at').defaultTo(knex.fn.now())`).

**Regla de transformación de datos (Capa de Servicio):**
- Al leer de SQLite, el servicio debe transformar `completed` de `0/1` a booleano (`false/true`) antes de entregarlo al controlador.
- Al escribir o actualizar en SQLite, el servicio debe transformar el booleano recibido a `0` o `1`.

## 5. Especificación de la API REST (CRUD)
Prefijo base: `/api/v1/tasks`

1. **GET `/`** - Obtener todas las tareas (Código 200). Devuelve array de objetos con `completed` como booleano.
2. **GET `/:id`** - Obtener una tarea por ID (Código 200 / 404 si no existe).
3. **POST `/`** - Crear una nueva tarea. Validar que `title` no esté vacío (Código 201 / 400).
4. **PUT `/:id`** - Actualizar estado (`completed`) o textos de la tarea (Código 200 / 404).
5. **DELETE `/:id`** - Eliminar una tarea (Código 204 / 404).

## 6. Requerimientos del Frontend (Vanilla JS)
- Uso de `fetch()` con `async/await` para consumir la API REST.
- Manipulación limpia del DOM para renderizar la lista de tareas, agregar nuevas, marcar como completadas (tachado visual) y eliminarlas.
- Manejo de errores visual para el usuario (ej. intentar crear tarea vacía).

## 7. Estrategia de Pruebas Funcionales Automáticas
- El archivo `knexfile.js` tendrá un entorno `test` que usará SQLite en memoria (`:memory:`) para pruebas rápidas y aisladas.
- Antes de cada suite de pruebas (`beforeAll`), se ejecutarán las migraciones de Knex.
- Se probarán automáticamente los 5 endpoints del CRUD verificando tanto los casos de éxito (Happy Path) como los errores (ej. buscar un ID que no existe debe devolver 404).


## 8. Plan de Ejecución Paso a Paso (Checklist)
El desarrollo asistido se ejecutará estrictamente en este orden:

- [ ] **Paso 1:** Inicializar proyecto Node (`npm init`) e instalar dependencias (`express`, `knex`, `sqlite3`, `cors`) y dependencias de desarrollo (`jest`, `supertest`, `nodemon`).
- [ ] **Paso 2:** Configurar `knexfile.js` (entornos `development` y `test`) y crear la migración para la tabla `tasks`.
- [ ] **Paso 3:** Implementar la capa de `services` usando Knex para las operaciones CRUD.
- [ ] **Paso 4:** Implementar `controllers`, `routes` y manejo de errores. Configurar `app.js` y `server.js`.
- [ ] **Paso 5:** Escribir y ejecutar las pruebas funcionales automáticas en `tests/tasks.test.js`. (No avanzar al paso 6 hasta que los tests pasen en verde).
- [ ] **Paso 6:** Desarrollar la interfaz en `public/index.html` y `styles.css`.
- [ ] **Paso 7:** Conectar el frontend con el backend mediante `public/js/main.js`.