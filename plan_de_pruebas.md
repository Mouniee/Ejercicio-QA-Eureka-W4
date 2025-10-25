# **Plan de Pruebas: APIs de Gestión**

## **1. Introducción y Alcance**

### **1.1. Objetivo General**
Validar la funcionalidad, fiabilidad y robustez de dos API REST desarrolladas con Spring Boot:
* **API Repo 1 (semana1):** Gestiona las entidades "Especificaciones" y "Ofertas".
* **API Repo 2 (solicitudes):** Gestiona "Solicitudes" de empleo y sus "Documentos" asociados.

### **1.2. Alcance de las Pruebas (In-Scope)**
Se probarán todas las funcionalidades expuestas a través de los endpoints definidos para cada API.

| Repositorio | Endpoints a Probar                               | Operaciones                                     |
| :---------- | :----------------------------------------------- | :---------------------------------------------- |
| **Repo 1** | `/api/ofertas` <br> `/api/especificaciones`      | `GET`, `POST`, `DELETE` (para Especificaciones) |
| **Repo 2** | `/api/v1/solicitudes` <br> `/api/v1/documentos`  | `GET`, `POST`, `PUT`, `DELETE` (CRUD completo)  |

### **1.3. Fuera de Alcance (Out-of-Scope)**
* **Pruebas de Interfaz Gráfica (UI):** No aplica para APIs de backend.
* **Pruebas de Carga y Estrés a gran escala:** No se realizarán simulaciones de miles de usuarios concurrentes.
* **Pruebas de Penetración de Seguridad:** No se incluye un análisis exhaustivo de vulnerabilidades.

---

## **2. Estrategia de Pruebas**

La estrategia se centra en la API, verificando la lógica de negocio, el manejo de datos y la gestión de errores a través de peticiones HTTP.

### **2.1. Niveles de Prueba**
* **Pruebas de Integración:** Se enfocan en la interacción entre la API y la base de datos MySQL, verificando que las operaciones (`POST`, `PUT`, `DELETE`) se reflejen correctamente.
* **Pruebas de Sistema:** Se prueba la API como un sistema completo, validando que las respuestas a las peticiones (códigos de estado, JSON) sean las correctas.

### **2.2. Tipos de Prueba**

#### **Pruebas Funcionales**
Estas pruebas se realizarán con **Postman**.
* **Happy Path Testing:** Comprobación de las operaciones CRUD (`GET`, `POST`, `PUT`, `DELETE`) con datos válidos.
* **Negative Testing & Error Handling:** Comprobación de la respuesta del sistema ante datos inválidos, IDs inexistentes y violación de reglas de negocio. Se esperan códigos de estado `400 Bad Request` y `404 Not Found` según corresponda.

#### **Pruebas No Funcionales (Básicas)**
* **Pruebas de Rendimiento:** Medición de tiempos de respuesta de los endpoints `GET` para asegurar que se encuentran dentro de un umbral aceptable (ej. < 1 segundo).
* **Pruebas de Seguridad:** Verificación de que la API no exponga información sensible y de que los accesos a recursos estén protegidos adecuadamente.

---

## **3. Ambiente de Pruebas**

La configuración del ambiente de pruebas difiere para cada proyecto.

### **3.1. Herramientas Comunes**
* **Cliente API:** Postman.
* **Cliente de Base de Datos:** DBeaver, MySQL Workbench o similar.

### **3.2. Configuración del Ambiente**

* **Para Repo 1 (semana1):**
    1.  **Tecnología:** Aplicación y base de datos containerizadas con Docker.
    2.  **Ejecución:** Construcción de la imagen de la aplicación y levantamiento de los contenedores correspondientes.
    3.  **Endpoint Base:** `http://localhost:8080` (o el puerto expuesto en Docker).

* **Para Repo 2 (solicitudes):**
    1.  **Tecnología:** Aplicación local con Maven y base de datos containerizada con Docker Compose.
    2.  **Ejecución:** Iniciar la base de datos con `docker-compose up -d` y la aplicación con `mvn spring-boot:run`.
    3.  **Endpoint Base:** `http://localhost:8080/api/v1/`.

---

## **4. Casos de Prueba**

A continuación se presenta un resumen de los casos de prueba a ejecutar y un ejemplo detallado.

### **4.1. Matriz de Casos de Prueba (Resumen)**

#### **API Repo 1: /api/especificaciones**

| ID Caso | Título                                                   | Pasos                                                                | Resultado Esperado                                                      | Tipo       |
|:--------|:---------------------------------------------------------|:---------------------------------------------------------------------|:------------------------------------------------------------------------|:-----------|
| TC-E01  | Crear una especificación con datos válidos               | 1. Enviar `POST` con un JSON válido.                                 | Código `201 Created`. <br> El nuevo registro existe en la BBDD.         | Happy Path |
| TC-E02  | Intentar crear una especificación sin un campo requerido | 1. Enviar `POST` con un JSON al que le falta un campo obligatorio.   | Código `400 Bad Request`. <br> Mensaje de error descriptivo.             | Negative   |
| TC-E03  | Eliminar una especificación existente                    | 1. Enviar `DELETE` a `/api/especificaciones/{id}` con un ID válido.  | Código `204 No Content`. <br> El registro ya no existe en la BBDD.       | Happy Path |
| TC-E04  | Intentar obtener una especificación con ID inexistente   | 1. Enviar `GET` a `/api/especificaciones/999` (ID no existe).        | Código `404 Not Found`.                                                 | Negative   |

#### **API Repo 2: /api/v1/solicitudes**

| ID Caso | Título                                           | Pasos                                                                                | Resultado Esperado                                                              | Tipo       |
|:--------|:-------------------------------------------------|:-------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------|:-----------|
| TC-S01  | Actualizar una solicitud con datos válidos       | 1. Enviar `PUT` a `/solicitudes/{id}` con un JSON válido.                              | Código `200 OK`. <br> Los datos se actualizan correctamente en la BBDD.          | Happy Path |
| TC-S02  | Obtener la lista completa de solicitudes         | 1. Enviar `GET` a `/solicitudes`.                                                      | Código `200 OK`. <br> El JSON de respuesta contiene un array de solicitudes.      | Happy Path |
| TC-S03  | Poblar datos y verificar la inserción (opcional) | 1. Seguir los pasos del `README.md` para poblar las tablas. <br> 2. Hacer `GET` a `/solicitudes`. | Código `200 OK` y la respuesta contiene los datos insertados.                   | Funcional  |

### **4.2. Detalle de Caso de Prueba**

| ID del Caso de Prueba | TC-E01                                                                                                                                                                                                |
| :-------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Proyecto** | API Repo 1 (semana1)                                                                                                                                                                                  |
| **Endpoint** | `POST /api/especificaciones`                                                                                                                                                                          |
| **Título** | Crear una especificación con datos válidos.                                                                                                                                                           |
| **Resumen** | Verificar que se puede crear una nueva especificación enviando un cuerpo de solicitud con todos los campos requeridos y válidos.                                                                      |
| **Precondiciones** | 1. El servicio de la API (Repo 1) está en ejecución y accesible.<br>2. La base de datos MySQL está en línea y conectada.<br>3. Se conoce el `id` de una `oferta` existente para la asociación (ej. `ofertaId: 1`). |
| **Pasos de Ejecución** | 1. Configurar una petición `POST` en Postman al endpoint `/api/especificaciones`.<br>2. En `Headers`, añadir `Content-Type: application/json`.<br>3. En `Body` (raw, JSON), insertar el siguiente payload: `{ "nombre": "Analista QA Jr", "descripcion": "Requiere 1 año de experiencia", "ofertaId": 1 }`<br>4. Enviar la petición. |
| **Resultados Esperados**| 1. El código de estado de la respuesta HTTP es **`201 Created`**.<br>2. El cuerpo de la respuesta contiene el objeto de la especificación recién creada, incluyendo un `id` generado por el sistema.<br>3. **Verificación en BBDD:** Una consulta `SELECT * FROM especificaciones` muestra el nuevo registro con los datos enviados. |
| **Post-condiciones** | Un nuevo registro de "especificación" existe en la base de datos.                                                                                                                                      |
| **Prioridad** | Alta  
| **Riesgo** | Alta                                                                                                                                                                                                  |

---

## **5. Gestión de Defectos**

### **5.1. Proceso de Reporte**
Los defectos identificados durante la ejecución de las pruebas serán reportados y gestionados a través de una herramienta de seguimiento designada (ej. Jira, Trello).

### **5.2. Estructura del Reporte de Defectos**
Cada reporte deberá contener la siguiente información:
* **Título:** Corto y descriptivo.
* **Descripción:** Detalle del problema y su impacto.
* **Pasos para Reproducir:** Lista numerada y clara para replicar el error.
* **Resultado Actual:** Comportamiento observado en la aplicación.
* **Resultado Esperado:** Comportamiento que la aplicación debería tener según los requisitos.
* **Severidad/Prioridad:** Clasificación del impacto y urgencia del defecto.