# Documentación oficial de implementación de Tesseract Onboarding

## Introducción

Tesseract Onboarding es una herramienta de KYC compuesta por multiples pasos desagregados que tiene como objetivo ayudar a identificar y verificar la identidad de una persona. A continuación se enlistan los pasos:


- OCR del comprobante de domicilio, autocaptura, extracción de metadatos y verificación de comprobantes de domicilio (CFE y Telmex).

- Verifación de correo y/o número telefónico mediante OTPs.

- Consulta de la INE ante INE (con datos personales).

- Verificación de la vigencia del INE(CIC, OCR)

- Verificación de la CURP ante RENAPO.

- Consulta de la información de la CURP ante RENAPO.

- Video testimonial.

- Creación de Matríz de riesgo

- Cruce de matriz de riesgo "Personalizada".

## Componentes
El producto completo consta de una API encargada de la gestión general de los clientes, siendo acompañado por un SDK, nativo para Android y iOS, que brindará las funciones necesarias para facilitar el uso de cada uno de los módulos anteriormente mencionados. 

# Uso

## API 

### Autenticación
El API en cada uno de sus endpoints requiere una autenticación de tipo OAuth2.0, a excepción del endpoint de login.

### Login 
Genera el token de acceso

```http
  POST /onboarding/api/v0/login
```
| Parameter     | Type           | Description                                                              |
| :---------    | :------------- | :----------------------------------------------------------------------- |
| `username`    | `string`       | **Requerido**. Nombre de usuario.                                        |
| `email`       | `string email` | **Requerido**. Correo institucional.                                     |
| `password`    | `string`       | **Requerido**. Contraseña.                                               |
| `licenses_id` | `string`       | **Requerido**.Clave única de la licencia perteneciente a la institución. |

#### Response
| Key           | Type            | Description                         |
| :----------   | :-------------  | :---------------------------------- |
| `status`      | `string`        | Status del token.                   |
| `access_token`| `string`        | Token de acceso.                    |
| `token_type`  | `string`        | Tipo de token.                      |
| `scopes`      | `array`         | Alcances del token.                 |
| `expires_in`  | `number`        | Duración del token.                 |

### Auth
Devuelve la información de autentición registrada en el token.
```http
  GET /onboarding/api/v0/auth
```

#### Response
| Key                 | Type           | Description                                |
| :------------------ | :------------- | :----------------------------------------- |
| `app_id`            | `string`       | Identificador único de la aplicación       |
| `name`              | `string`       | Nombre de la aplicación                    |
| `access_key_id`     | `string`       | Nombre de usuario registrado               |
| `secret_access_key` | `string`       | Contraseña de acceso, siempre es nula      |
| `enabled`           | `boolean`      | Estado actual de la aplicación             |
| `created_at`        | `date`         | Fecha de creación de la aplicación         |
| `updated_at`        | `date`         | Fecha de modificación de la aplicación     |

### Institution

Institución a la que el usuario pertence

```http
  GET /onboarding/api/v0/institution
```

#### Response
| Key                   | Value             | Description                                         |
| :-------------------- | :---------------- | :-------------------------------------------------- |
| `id`                  | `string`          | Identificador único de la institución               |
| `common_name`         | `string`          | Dominio electrónico de la institución               |
| `organization`        | `string`          | Razón social de la institución                      |
| `organization_unit`   | `string`          | Unidad o departamento responsable de la institución |
| `country`             | `string`          | País donde está ubicada su institución              |
| `state`               | `string`          | Estado donde su institución se encuentra ubicada    |
| `locality`            | `string`          | Ciudad donde se encuentra su institución            |
| `email`               | `string-email`    | Correo electrónico del responsable a cargo          |
| `licenses`            | `array_licenses`  | Colección de licencias asignadas a su institución   |
| `created_at`          | `date`            | Fecha de creación de la institución                 |
| `updated_at`          | `date`            | Fecha de modificación de la institución             |


### Apps

Creacion de una nueva aplicación

```http
  POST /onboarding/api/v0/institution/licenses/app
```
donde: 

| Parameter            | Type           | Description                                           |
| :------------------- | :------------- | :---------------------------------------------------- |
| `app_id`             | `string`       | **Requerido**. Identificador único de la aplicación   |
| `name`               | `string`       | **Requerido**. Nombre de la aplicación                |
| `access_key_id`      | `string`       | **Requerido**. Llave unica de acceso                  |
| `secret_access_key`  | `string`       | **Requerido**. Contraseña unica de acceso             |
| `enabled`            | `boolean`      | **Requerido**. Estado de la aplicación                |
| `created_at`         | `date`         | **Requerido**. Fecha de creación de la aplicación     |
| `updated_at`         | `date`         | **Requerido**. Fecha de modificación de la aplicación |


#### Response
| Key                  | Value          | Description                                     |
| :------------------- | :------------- | :---------------------------------------------- |
| `app_id`             | `string`       | Identificador único de la aplicación            |
| `name`               | `string`       | Nombre de la aplicación                         |
| `access_key_id`      | `string`       | Llave unica de acceso                           |
| `secret_access_key`  | `string`       | Contraseña unica de acceso, siempre sera nula   |
| `enabled`            | `boolean`      | Estado actual la aplicación                     |
| `created_at`         | `date`         | Fecha de creación de la aplicación              |
| `updated_at`         | `date`         | Fecha de modificación de la aplicación          |



### Consultas con claves

Obtiene la información detallada de un cliente a través de su nombre, apellido, fecha de nacimiento, estado y género. 

```http
  POST /onboarding/api/v0/institution/licenses/{license_id}/data_query/get_curp
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `licenses_id`   | `string`        | Clave única de la licencia perteneciente a la institución.  |

#### Body

| Parameter       | Type     | Description                                              |
| :-------------- | :------- | :------------------------------------------------------- |
| `name`          | `string` | Nombre del cliente.                                      |
| `lastname`      | `string` | Apellido paterno del cliente.                            |
| `surname`       | `string` | Apellido materno del cliente.                            |
| `birthdate`     | `string` | Fecha de nacimiento del cliente en formato dd/mm/yyyy.   |
| `state`         | `string` | Estado de nacimiento del cliente en formato de dos letras (por ejemplo, "CH" para Chihahua).  |
| `gender`        | `string` | Género del cliente                                        |


#### Response
| Key           | Value     | Description                                         |
| :---------    | :-------- | :--------------------------------------------       |
| `name`        | `string`  | Nombre del cliente.                                 |
| `lastname`    | `string`  | Apellido paterno del cliente.                       |
| `surname`     | `string`  | Apellido materno del cliente.                       |
| `CURP`        | `string`  | Clave Única de Registro de Población del cliente.   |
| `gender`      | `string`  | Género del cliente                                  |
| `birthdate`   | `string`  | Fecha de nacimiento del cliente                     |
| `nationality` | `string`  | Nacionalidad del cliente.                           |
| `birth_state` | `string`  | Estado de nacimiento del cliente.                   |
| `evidence_doc`| `number`  | Número de documento de evidencia.                   |
| `evidence_doc_data`| `object`  | Datos del documento de evidencia.              |
| `status`      | `number`  | Estatus de la CURP                                  |

donde el objeto `evidence_doc_data` contiene: 

| Key                 | Value     | Description                                         |
| :---------          | :-------- | :--------------------------------------------       |
| `register_state`    | `string`  | Entidad de registro                                 |
| `volume`            | `string`  | El volumen asociado al registro del documento.      |
| `register_town_key` | `string`  | Clave de registro del pueblo o localidad            |
| `register_year`     | `string`  | Año de registro                                     |
| `register_state_year`| `string` | El año de registro en el estado.                    |
| `sheet `            | `string`  | El número de hoja del registro.                     |
| `certificate_number`| `string`  | Numero de acta.                                     |
| `birth_state`       | `string`  | Estado de nacimiento del cliente.                   |
| `book `             | `string`  | Libro relacionado al registro.                      |
| `register_town`     | `string`  | Nombre de la localidad donde se realizó el registro.|



Obtiene el nombre de una persona mediante su RFC

```http
  POST /onboarding/api/v0/institution/licenses/{license_id}/data_query/get_rfc_name
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `licenses_id`   | `string`        | Clave única de la licencia perteneciente a la institución.  |

#### Body

| Parameter       | Type     | Description                                                                      |
| :-------------- | :------- | :----------------------------------------------------------------                |
| `RFC`           | `string` | El Registro Federal de Contribuyentes que se utilizará para buscar información.  |

#### Response
| Key           | Value     | Description                                         |
| :---------    | :-------- | :--------------------------------------------       |
| `rfc`         | `string`  | El Registro Federal de Contribuyentes buscado.      |
| `name`        | `string`  | El nombre asociado con el RFC consultado.           |


Obtiene los datos del Servicio de Administración Tributaria de una persona mediante su RFC Y CIF

```http
  POST /onboarding/api/v0/institution/licenses/{license_id}/data_query/rfc
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `licenses_id`   | `string`        | Clave única de la licencia perteneciente a la institución.  |

#### Body

| Parameter       | Type     | Description                                |
| :-------------- | :------- | :---------------------------------------   |
| `RFC`           | `string` | El Registro Federal de Contribuyentes      |
| `CIF`           | `string` | La Clave de Identificación Fiscal          |


#### Response
| Key                    | Value     | Description                                         |
| :---------             | :-------- | :--------------------------------------------       |
| `rfc`                  | `string`  | El Registro Federal de Contribuyentes buscado.      |
| `cif`                  | `string`  | La Clave de Identificación Fiscal consultada        |
| `id_data`              | `object`  | Datos de identificación relacionados con el RFC.    |
| `location_data`        | `object`  | Datos de ubicación relacionados con el RFC.         |
| `tax-related features` | `array`   | Características Fiscales                            |


Obtiene información detallada de un cliente mediante su CURP

```http
  POST /onboarding/api/v0/institution/licenses/{license_id}/data_query/curp
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `licenses_id`   | `string`        | Clave única de la licencia perteneciente a la institución.  |

#### Body

| Parameter       | Type     | Description                                |
| :-------------- | :------- | :---------------------------------------   |
| `CURP`          | `string` | La Clave Única de Registro de Población    |


#### Response
| Key           | Value     | Description                                         |
| :---------    | :-------- | :--------------------------------------------       |
| `age`         | `number`  | Edad del cliente.                                   |
| `name`        | `string`  | Nombre del cliente.                                 |
| `lastname`    | `string`  | Apellido paterno del cliente.                       |
| `surname`     | `string`  | Apellido materno del cliente.                       |
| `CURP`        | `string`  | Clave Única de Registro de Población del cliente.   |
| `gender`      | `string`  | Género del cliente                                  |
| `birthdate`   | `string`  | Fecha de nacimiento del cliente                     |
| `nationality` | `string`  | Nacionalidad del cliente.                           |
| `birth_state` | `string`  | Estado de nacimiento del cliente.                   |
| `evidence_doc`| `number`  | Número de documento de evidencia.                   |
| `evidence_doc_data`| `object`  | Datos del documento de evidencia.              |
| `status`      | `number`  | Estatus de la CURP                                  |

donde el objeto `evidence_doc_data` contiene: 

| Key                 | Value     | Description                                         |
| :---------          | :-------- | :--------------------------------------------       |
| `register_state`    | `string`  | Entidad de registro                                 |
| `volume`            | `string`  | El volumen asociado al registro del documento.      |
| `register_town_key` | `string`  | Clave de registro del pueblo o localidad            |
| `register_year`     | `string`  | Año de registro                                     |
| `register_state_year`| `string` | El año de registro en el estado.                    |
| `sheet `            | `string`  | El número de hoja del registro.                     |
| `certificate_number`| `string`  | Numero de acta.                                     |
| `birth_state`       | `string`  | Estado de nacimiento del cliente.                   |
| `book `             | `string`  | Libro relacionado al registro.                      |
| `register_town`     | `string`  | Nombre de la localidad donde se realizó el registro.|



Obtiene información electoral detallada de un cliente mediante su INE

```http
  POST /onboarding/api/v0/institution/licenses/{license_id}/data_query/ine
```

#### Body

| Parameter       | Type     | Description                                |
| :-------------- | :------- | :---------------------------------------   |
| `CIC`           | `string` | La Clave de Identificación Ciudadana       |
| `citizen_id`    | `string` | La Identificación del Ciudadano            |


#### Response
| Key           | Value     | Description                                         |
| :---------    | :-------- | :--------------------------------------------       |
| `key`         | `string`  | Clave del Emisión                                   |
| `issue`       | `string`  | Número de Emisión                                   |
| `ocr`         | `string`  | Valor OCR (Reconocimiento Óptico de Caracteres)     |
| `cic`         | `string`  | La Clave de Identificación Ciudadana                |
| `register_year`| `string` | Año de Registro                                     |
| `issue_year`  | `string`  | Año de Emisión                                      |
| `validity`    | `string`  | Vigencia                                            |



### Video testimonial

Identificación y transcripción de voz de una persona en un video

```http
  POST /onboarding/api/v0/institution/licenses/{{license_id}}/video_statement/
```
donde: 
| Parameter     | Type     | Description                                                  |
| :------------ | :------- | :----------------------------------------------------------- |
| `license_id`   | `string` | Clave única de la licencia.         |

#### Body:

| Parameter | Type             | Description                                                       |
| :-------- | :--------------- | :---------------------------------------------------------------- |
| `file`    | `string binary`  | **Requerido**. El archivo de video que se va a enviar             |
| `img`     | `string binary`  | **Requerido**. Una imagen de la persona a identificar en el video |


#### Response
| Key                 | Value    | Description                                        |
| :------------------ | :------- | :------------------------------------------------- |
| `statement`         | `string` | Cadena de texto reconocida del video.              |
| `face_match_score`  | `string` | Porcentaje de reconocimiento respecto a la imagen. |


### Matriz de riesgo

Crea una nueva matriz de riesgo
```http
  POST /onboarding/api/v0/risk_matrix
```

#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`            | `string`    | **Requerido**. Nombre asociado a la matriz de riesgo. |
| `description`     | `string`    | **Requerido**. Descripción de la matriz de riesgo. |
| `catalog_id`      | `string`    | **Requerido**. ID del catalogo asociado a la matriz de riesgo. |
| `rows`            | `number`    | **Requerido**.  Número de filas en la matriz de riesgo. |
| `columns`         | `number`    | **Requerido**. Número de columnas en la matriz de riesgo. |


#### Response
| Key               | Value     | Description                                                           |
| :---------------- | :---------- | :----------------------------------------------------------------   |
| `status`          | `string`    |  Estado de la petición. Indica el resultado.                        |
| `matrix`          | `object`    |  Objeto que contiene detalles de la matriz de riesgo.               |

El objeto `matrix` contiene la siguiente información:

| Key               | Value      | Description                                          |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único de la matriz de riesgo.           |
| `name`            | `string`  | Nombre de la matriz de riesgo.                        |
| `description`     | `string`  | Descripción de la matriz de riesgo.                   |
| `catalog_id`      | `string`  | Identificador del catálogo asociado.                  |
| `sections`        | `array`   | Secciones de la matriz de riesgo                      |
| `rows`            | `number`  | Número de filas en la matriz de riesgo.               |
| `columns`         | `number`  | Número de columnas en la matriz de riesgo.            |
| `risk_thresholds` | `array`   | Umbrales de riesgo                                    |



Obtiene una matriz de riesgo
```http
  GET /onboarding/api/v0/risk_matrix/{id}
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único de la matriz de riesgo.                |


#### Response
| Key               | Value    | Description                                            |
| :---------------- | :---------- | :---------------------------------------------------|
| `id`              | `string`  | Identificador único de la matriz de riesgo.           |
| `name`            | `string`  | Nombre de la matriz de riesgo.                        |
| `description`     | `string`  | Descripción de la matriz de riesgo.                   |
| `catalog_id`      | `string`  | Identificador del catálogo asociado.                  |
| `sections`        | `array`   | Secciones de la matriz de riesgo                      |
| `rows`            | `number`  | Número de filas en la matriz de riesgo.               |
| `columns`         | `number`  | Número de columnas en la matriz de riesgo.            |
| `risk_thresholds` | `array`   | Umbrales de riesgo                                    |


Edita una matriz de riesgo existente
```http
  PUT /onboarding/api/v0/risk_matrix/{id}
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único de la matriz de riesgo.                |


#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`            | `string`    | **Requerido**. Nombre asociado a la matriz de riesgo.             |
| `description`     | `string`    | **Requerido**. Descripción de la matriz de riesgo.                |

#### Response
| Key               | Value       | Description                                                         |
| :---------------- | :---------- | :----------------------------------------------------------------   |
| `status`          | `string`    |  Estado de la petición. Indica el resultado.                        |
| `matrix`          | `object`    |  Objeto que contiene detalles de la matriz de riesgo.               |

El objeto `matrix` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único de la matriz de riesgo.           |
| `name`            | `string`  | Nombre de la matriz de riesgo.                        |
| `description`     | `string`  | Descripción de la matriz de riesgo.                   |
| `catalog_id`      | `string`  | Identificador del catálogo asociado.                  |
| `sections`        | `array`   | Secciones de la matriz de riesgo                      |
| `rows`            | `number`  | Número de filas en la matriz de riesgo.               |
| `columns`         | `number`  | Número de columnas en la matriz de riesgo.            |
| `risk_thresholds` | `array`   | Umbrales de riesgo                                    |


Elimina una matriz de riesgo
```http
  DELETE /onboarding/api/v0/risk_matrix/{id}
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único de la matriz de riesgo.                |


### Filas y columnas de una matriz de riesgo

Edita las filas y columnas de una matriz de riesgo
```http
  PUT /onboarding/api/v0/risk_matrix/{id}/rows_columns
```
**Nota:** Cada vez que edites estos valores, los umbrales de riesgo o `risk_thresholds` se eliminarán y deberás crearlos nuevamente.

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único de la matriz de riesgo.                |

#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `columns`         | `number`    |   Número de columnas en la matriz de riesgo.                      |
| `rows`            | `number`    |   Número de filas en la matriz de riesgo.                         |


#### Response
| Key               | Value       | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `status`          | `string`    |  Estado de la petición. Indica el resultado.                      |
| `matrix`          | `object`    |  Objeto que contiene detalles de la matriz de riesgo.             |

El objeto `matrix` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único de la matriz de riesgo.           |
| `name`            | `string`  | Nombre de la matriz de riesgo.                        |
| `description`     | `string`  | Descripción de la matriz de riesgo.                   |
| `catalog_id`      | `string`  | Identificador del catálogo asociado.                  |
| `sections`        | `array`   | Secciones de la matriz de riesgo                      |
| `rows`            | `number`  | Número de filas en la matriz de riesgo.               |
| `columns`         | `number`  | Número de columnas en la matriz de riesgo.            |
| `risk_thresholds` | `array`   | Umbrales de riesgo                                    |


### Umbrales de riesgo

Crea un nuevo umbral de riesgo en la matriz
```http
  POST /onboarding/api/v0/risk_matrix/{id}/thresholds
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único de la matriz de riesgo.                |

#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`        | `string`  | **Requerido**. Nombre del umbral de riesgo.                     |
| `min`         | `number`  | **Requerido**. Valor mínimo del umbral de riesgo.               |
| `max`         | `number`  | **Requerido**. Valor máximo del umbral de riesgo.               |

#### Response
| Key               | Value    | Description                                                          |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `status`          | `string`    |  Estado de la petición. Indica el resultado.                      |
| `matrix`          | `object`    |  Objeto que contiene detalles de la matriz de riesgo.             |

El objeto `matrix` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único de la matriz de riesgo.           |
| `name`            | `string`  | Nombre de la matriz de riesgo.                        |
| `description`     | `string`  | Descripción de la matriz de riesgo.                   |
| `catalog_id`      | `string`  | Identificador del catálogo asociado.                  |
| `sections`        | `array`   | Secciones de la matriz de riesgo                      |
| `rows`            | `number`  | Número de filas en la matriz de riesgo.               |
| `columns`         | `number`  | Número de columnas en la matriz de riesgo.            |
| `risk_thresholds` | `array`   | Umbrales de riesgo                                    |

Cada Umbral de riesgo en `risk_thresholds` contiene:

| Key               | Value     | Description                                                 |
| :---------------- | :-------- | :---------------------------------------------------        |
| `name`            | `string`  |  El nombre asociado al umbral de riesgo.                    |
| `min`             | `number`  |  El valor mínimo del rango de riesgo asociado al umbral.    |
| `max`             | `number`  |   El valor máximo del rango de riesgo asociado al umbral.   |

Elimina un umbral de riesgo de la matriz
```http
  DELETE /onboarding/api/v0/risk_matrix/{id}/thresholds?name={umbral}
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único de la matriz de riesgo.                |
| `umbral`        | `string`    |  Nombre del umbral de riesgo a eliminar.                        |

#### Response
| Key               | Value    | Description                                                          |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `status`          | `string`    |  Estado de la petición. Indica el resultado.                      |
| `matrix`          | `object`    |  Objeto que contiene detalles de la matriz de riesgo.             |

El objeto `matrix` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único de la matriz de riesgo.           |
| `name`            | `string`  | Nombre de la matriz de riesgo.                        |
| `description`     | `string`  | Descripción de la matriz de riesgo.                   |
| `catalog_id`      | `string`  | Identificador del catálogo asociado.                  |
| `sections`        | `array`   | Secciones de la matriz de riesgo                      |
| `rows`            | `number`  | Número de filas en la matriz de riesgo.               |
| `columns`         | `number`  | Número de columnas en la matriz de riesgo.            |
| `risk_thresholds` | `array`   | Umbrales de riesgo                                    |



### Secciones de una matriz de riesgo

Crea una nueva sección en la matriz de riesgo
```http
  POST /onboarding/api/v0/risk_matrix/{id}/sections
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único de la matriz de riesgo.                |

#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`            | `string`  | **Requerido**. Nombre de la sección                                   |
| `weighting`       | `number`  | **Requerido**. Peso o ponderación de la sección sobre la matriz de riesgo. **Este valor debe estar entre 0 y 1** |

#### Response
| Key               | Value    | Description                                                      |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `status`          | `string`    |  Estado de la petición. Indica el resultado. |
| `matrix`          | `object`    |  Objeto que contiene detalles de la matriz de riesgo. |

El objeto `matrix` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único de la matriz de riesgo.           |
| `name`            | `string`  | Nombre de la matriz de riesgo.                        |
| `description`     | `string`  | Descripción de la matriz de riesgo.                   |
| `catalog_id`      | `string`  | Identificador del catálogo asociado.                  |
| `sections`        | `array`   | Secciones de la matriz de riesgo                      |
| `rows`            | `number`  | Número de filas en la matriz de riesgo.               |
| `columns`         | `number`  | Número de columnas en la matriz de riesgo.            |
| `risk_thresholds` | `array`   | Umbrales de riesgo                                    |

Cada sección en `sections` contiene:

| Key               | Value     | Description                                                 |
| :---------------- | :-------- | :---------------------------------------------------        |
| `id`              | `string`  | Identificador único de la sección en la matriz ede riesgo.  |
| `name`            | `number`  | Nombre de la matriz de sección.                             |
| `weighting`       | `string`  | Peso o ponderación de la sección sobre la matriz de riesgo. |
| `fields`          | `array`   | Lista de campos en la sección.                              |

Cada campo en `fields` contiene:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `catalog_field_id`| `string`  | Identificador único de un campo de un catálogo.       |
| `weighting`       | `any`     | Peso o ponderación del campo sobre la sección         |



Edita una seccion 
```http
  PUT /onboarding/api/v0/risk_matrix/{id}/sections/{section_id}
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único de la matriz de riesgo.                |
| `section_id`    | `string`        |  Identificador único de la seccion                          |

#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`            | `string`  | **Requerido**. Nombre de la sección                     |
| `weighting`       | `number`  | **Requerido**. Peso o ponderación de la sección sobre la matriz de riesgo          |

#### Response
| Key               | Value    | Description                                                      |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `status`          | `string`    |  Estado de la petición. Indica el resultado. |
| `matrix`          | `object`    |  Objeto que contiene detalles de la matriz de riesgo. |

El objeto `matrix` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único de la matriz de riesgo.           |
| `name`            | `string`  | Nombre de la matriz de riesgo.                        |
| `description`     | `string`  | Descripción de la matriz de riesgo.                   |
| `catalog_id`      | `string`  | Identificador del catálogo asociado.                  |
| `sections`        | `array`   | Secciones de la matriz de riesgo                      |
| `rows`            | `number`  | Número de filas en la matriz de riesgo.               |
| `columns`         | `number`  | Número de columnas en la matriz de riesgo.            |
| `risk_thresholds` | `array`   | Umbrales de riesgo                                    |

Cada sección en `sections` contiene:

| Key               | Value     | Description                                                 |
| :---------------- | :-------- | :---------------------------------------------------        |
| `id`              | `string`  | Identificador único de la sección en la matriz ede riesgo.  |
| `name`            | `number`  | Nombre de la matriz de sección.                             |
| `weighting`       | `string`  | Peso o ponderación de la sección sobre la matriz de riesgo. |
| `fields`          | `array`   | Lista de campos en la sección.                              |

Cada campo en `fields` contiene:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `catalog_field_id`| `string`  | Identificador único de un campo de un catálogo.       |
| `weighting`       | `any`     | Peso o ponderación del campo sobre la sección         |



Elimina una seccion de una matriz de riesgo 
```http
  DELETE /onboarding/api/v0/risk_matrix/{id}/sections/{section_id}
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único de la matriz de riesgo.                |
| `section_id`    | `string`        |  Identificador único de la seccion                          |


#### Response
| Key               | Value    | Description                                                      |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `status`          | `string`    |  Estado de la petición. Indica el resultado. |
| `matrix`          | `object`    |  Objeto que contiene detalles de la matriz de riesgo. |

El objeto `matrix` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único de la matriz de riesgo.           |
| `name`            | `string`  | Nombre de la matriz de riesgo.                        |
| `description`     | `string`  | Descripción de la matriz de riesgo.                   |
| `catalog_id`      | `string`  | Identificador del catálogo asociado.                  |
| `sections`        | `array`   | Secciones de la matriz de riesgo                      |
| `rows`            | `number`  | Número de filas en la matriz de riesgo.               |
| `columns`         | `number`  | Número de columnas en la matriz de riesgo.            |
| `risk_thresholds` | `array`   | Umbrales de riesgo                                    |

Cada sección en `sections` contiene:

| Key               | Value     | Description                                                 |
| :---------------- | :-------- | :---------------------------------------------------        |
| `id`              | `string`  | Identificador único de la sección en la matriz ede riesgo.  |
| `name`            | `number`  | Nombre de la matriz de sección.                             |
| `weighting`       | `string`  | Peso o ponderación de la sección sobre la matriz de riesgo. |
| `fields`          | `array`   | Lista de campos en la sección.                              |

Cada campo en `fields` contiene:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `catalog_field_id`| `string`  | Identificador único de un campo de un catálogo.       |
| `weighting`       | `any`     | Peso o ponderación del campo sobre la sección         |


### Campos de una seccion

Crea un nuevo campo en la sección
```http
  POST /onboarding/api/v0/risk_matrix/sections/{section_id}/fields
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `section_id`    | `string`        |  Identificador único de la sección                          |

#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `catalog_field_id` | `string`  | **Requerido**. Identificador único de un campo de un catálogo.                 |
| `weighting`        | `number` | **Requerido**. Peso o ponderación de la sección sobre la matriz de riesgo  **Este valor debe estar entre 0 y 1**        |

**Nota:** El valor de `catalog_field_id` debe corresponder a un campo (ID) existente en el catálogo asociado a la matriz de riesgo pertinente a esta sección.
 Para llevar a cabo este proceso, se recomienda crear primero un campo en el catalogo asociado a la matriz para obtener su identificador único (ID) y luego generar un campo dentro de la sección correspondiente utilizando dicho ID. Este enfoque garantizará la coherencia y la integridad de los datos en la matriz de riesgo.  


#### Response
| Key               | Value    | Description                                                      |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `status`          | `string`    |  Estado de la petición. Indica el resultado. |
| `matrix`          | `object`    |  Objeto que contiene detalles de la matriz de riesgo. |

El objeto `matrix` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único de la matriz de riesgo.           |
| `name`            | `string`  | Nombre de la matriz de riesgo.                        |
| `description`     | `string`  | Descripción de la matriz de riesgo.                   |
| `catalog_id`      | `string`  | Identificador del catálogo asociado.                  |
| `sections`        | `array`   | Secciones de la matriz de riesgo                      |
| `rows`            | `number`  | Número de filas en la matriz de riesgo.               |
| `columns`         | `number`  | Número de columnas en la matriz de riesgo.            |
| `risk_thresholds` | `array`   | Umbrales de riesgo                                    |

Cada sección en `sections` contiene:

| Key               | Value     | Description                                                 |
| :---------------- | :-------- | :---------------------------------------------------        |
| `id`              | `string`  | Identificador único de la sección en la matriz ede riesgo.  |
| `name`            | `number`  | Nombre de la matriz de sección.                             |
| `weighting`       | `string`  | Peso o ponderación de la sección sobre la matriz de riesgo. |
| `fields`          | `array`   | Lista de campos en la sección.                              |

Cada campo en `fields` contiene:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `catalog_field_id`| `string`  | Identificador único de un campo de un catálogo.       |
| `weighting`       | `any`     | Peso o ponderación del campo sobre la sección         |


Elimina un campo de una sección
```http
  DELETE /onboarding/api/v0/risk_matrix/sections/{section_id}/fields?catalog_field_id={catalog_field_id}
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `section_id`    | `string`        |  Identificador único de la sección                          |
| `catalog_field_id` | `string`     |  Identificador único de un campo de un catálogo asociado a la sección a eliminar          |


### Catalogos

Crea un nuevo catalogo
```http
  POST /onboarding/api/v0/risk_matrix/catalogs
```

#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`            | `string`    | **Requerido**. Nombre del catalogo                                |

#### Response
| Key               | Value       | Description                                                          |
| :---------------- | :---------- | :----------------------------------------------------------------    |
| `status`          | `string`    | Estado de la petición. Indica el resultado.                          |
| `catalog`         | `object`    | Objeto que contiene detalles del catálogo.                           |

El objeto `catalog` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único del catálogo.                     |
| `name`            | `string`  | Nombre del catálogo.                                  |
| `fields`          | `array`   | Lista de campos del catálogo.                         |


Obtener un catalogo existente
```http
  GET /onboarding/api/v0/risk_matrix/catalogs/{id}
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único del catalogo                           |

#### Response
| Key               | Value    | Description                                                          |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `status`          | `string`  | Estado de la petición. Indica el resultado.                         |
| `catalog`         | `object`  | Objeto que contiene detalles del catálogo.                          |

El objeto `catalog` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único del catálogo.                     |
| `name`            | `string`  | Nombre del catálogo.                                  |
| `fields`          | `array`   | Lista de campos del catálogo.                         |


Editar un catalogo existente
```http
  PUT /onboarding/api/v0/risk_matrix/catalogs/{id}
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único del catalogo                           |

#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`            | `string`    |  Nombre del catalogo                                              |

#### Response
| Key               | Value    | Description                                                          |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `status`          | `string`  | Estado de la petición. Indica el resultado.                         |
| `catalog`         | `object`  | Objeto que contiene detalles del catálogo.                          |        

El objeto `catalog` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único del catálogo.                     |
| `name`            | `string`  | Nombre del catálogo.                                  |
| `fields`          | `array`   | Lista de campos del catálogo.                         |


Elimina un catalogo existente
```http
  DELETE /onboarding/api/v0/risk_matrix/catalogs/{id}
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único del catalogo                           |


### Campos de un catalogo

Crea un nuevo campo en el catalogo
```http
  POST /onboarding/api/v0/risk_matrix/catalogs/{id}/fields
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único del catalogo                           |


#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`            | `string`    | **Requerido**. Nombre del campo                                   |
| `value_type`      | `string`    | **Requerido**. Tipo de dato del campo (ej. "INTEGER").            |
| `min_range`       | `number`    | Valor mínimo aceptado para el campo.                              |
| `max_range`       | `number`    | Valor máximo aceptado para el campo.                              |
| `accepted_values` | `array`     | Lista de valores aceptados para el campo.                         |
| `regex_pattern`   | `string`    | Patrón de expresión regular para el campo                         |

**Tipos de Datos Permitidos:** ("INTEGER", "FLOAT", "STRING", "ENUM", "BOOLEAN")

| Datatype          | min_range / max_range | accepted_values | regex_pattern   |
| :---------------- | :-------------------- | :-------------- | --------------  |
| `INTEGER`         | APLICA                | NO APLICA       | NO APLICA       |
| `FLOAT`           | APLICA                | NO APLICA       | NO APLICA       |
| `STRING`          | APLICA                | NO APLICA       | APLICA          |
| `ENUM`            | NO APLICA             | APLICA          | NO APLICA       |
| `BOOLEAN`         | NO APLICA             | NO APLICA       | NO APLICA       |

#### Response
| Key               | Value       | Description                                                         |
| :---------------- | :---------- | :----------------------------------------------------------------   |
| `status`          | `string`    | Estado de la petición. Indica el resultado.                         |
| `catalog`         | `object`    | Objeto que contiene detalles del catálogo.                          |        

El objeto `catalog` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único del catálogo.                     |
| `name`            | `string`  | Nombre del catálogo.                                  |
| `fields`          | `array`   | Lista de campos del catálogo.                         |

Cada campo en `fields` contiene:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`    | Identificador único del campo del catálogo.         |
| `name`            | `string`    | Nombre del campo                                    |
| `value_type`      | `string`    | Tipo de dato del campo                              |
| `min_range`       | `number`    | Valor mínimo aceptado para el campo.                |
| `max_range`       | `number`    | Valor máximo aceptado para el campo.                |
| `accepted_values` | `array`     | Lista de valores aceptados para el campo.           |
| `regex_pattern`   | `string`    | Patrón de expresión regular para el campo           |


Edita un campo en el catalogo
```http
  PUT /onboarding/api/v0/risk_matrix/catalogs/fields/{id_field}
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id_field`      | `string`        |  Identificador único de un campo del catalogo               |

#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`            | `string`    | Nombre del campo                                                  |
| `value_type`      | `string`    | Tipo de dato del campo (ej. "INTEGER").                           |
| `min_range`       | `number`    | Valor mínimo aceptado para el campo.                              |
| `max_range`       | `number`    | Valor máximo aceptado para el campo.                              |
| `accepted_values` | `array`     | Lista de valores aceptados para el campo.                         |
| `regex_pattern`   | `string`    | Patrón de expresión regular para el campo                         |

#### Response
| Key               | Value    | Description                                                          |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `status`          | `string`  | Estado de la petición. Indica el resultado.                         |
| `catalog`         | `object`  | Objeto que contiene detalles del catálogo.                          |        

El objeto `catalog` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único del catálogo.                     |
| `name`            | `string`  | Nombre del catálogo.                                  |
| `fields`          | `array`   | Lista de campos del catálogo.                         |

Cada campo en `fields` contiene:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`    | Identificador único del campo del catálogo.         |
| `name`            | `string`    | Nombre del campo                                    |
| `value_type`      | `string`    | Tipo de dato del campo                              |
| `min_range`       | `number`    | Valor mínimo aceptado para el campo.                |
| `max_range`       | `number`    | Valor máximo aceptado para el campo.                |
| `accepted_values` | `array`     | Lista de valores aceptados para el campo.           |
| `regex_pattern`   | `string`    | Patrón de expresión regular para el campo           |


Elimina un campo en el catalogo
```http
  DELETE /onboarding/api/v0/risk_matrix/catalogs/fields/{id_field}
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id_field`      | `string`        |  Identificador único de un campo del catalogo               |

#### Response
| Key               | Value    | Description                                                          |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `status`          | `string`  | Estado de la petición. Indica el resultado.                         |
| `catalog`         | `object`  | Objeto que contiene detalles del catálogo.                          |        

El objeto `catalog` contiene la siguiente información:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`  | Identificador único del catálogo.                     |
| `name`            | `string`  | Nombre del catálogo.                                  |
| `fields`          | `array`   | Lista de campos del catálogo.                         |

Cada campo en `fields` contiene:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `id`              | `string`    | Identificador único del campo del catálogo.         |
| `name`            | `string`    | Nombre del campo                                    |
| `value_type`      | `string`    | Tipo de dato del campo                              |
| `min_range`       | `number`    | Valor mínimo aceptado para el campo.                |
| `max_range`       | `number`    | Valor máximo aceptado para el campo.                |
| `accepted_values` | `array`     | Lista de valores aceptados para el campo.           |
| `regex_pattern`   | `string`    | Patrón de expresión regular para el campo           |


### Evaluacion de una matriz

Elimina un campo en el catalogo
```http
  POST /onboarding/api/v0/risk_matrix/{id}/evaluation
```

donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `id`            | `string`        |  Identificador único de la matriz de riesgo.                |

#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `values`          | `array`     | Array con los valores a evaluar                                   |

El array `values` debe de contener la siguiente información:

| Campo             | Tipo      | Descripción                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `name`            | `string`  | Nombre del campo                                      |
| `value`           | `any`     | Valor a evaluar                                       |

#### JSON de Ejemplo
```json
{
  "values": [
    {
      "name": "EDAD",
      "value": 25
    },
    {
      "name": "NUM. DE HIJOS",
      "value": 1
    }
  ]
}
```

Es importante tener en cuenta lo siguiente:

- **Nombre:** El nombre debe ser el nombre del campo del catálogo que ingresaste en cada campo de una sección (campo `catalog_field_id`).

#### Response
| Key               | Value       | Description                                                          |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `risk`            | `number`    | Riesgo general asociado con la evaluación.                        |
| `threshold`       | `string`    | Umbral de riesgo general de la evaluación.                        |    
| `sections`        | `array`     | Lista de secciones evaluadas en la evaluación.                    | 

Cada sección en `sections` contiene:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `name`            | `string`  | Nombre de la sección evaluada.                        |
| `risk`            | `number`  | Riesgo asociado con la sección evaluada.              |
| `threshold`       | `string`  | Umbral de riesgo de la sección evaluada.              |
| `fields`          | `array`   | Lista de campos evaluados en la sección.              |

Cada campo en `fields` contiene:

| Key               | Value     | Description                                           |
| :---------------- | :-------- | :---------------------------------------------------  |
| `name`            | `string`  | Nombre del campo evaluado.                            |
| `value`           | `any`     | Valor del campo evaluado.                             |
| `risk`            | `number`  | Riesgo asociado con el campo evaluado.                |
| `threshold`       | `string`  | Umbral de riesgo del campo evaluado.                  |

# Evaluación de Riesgo: Qué se evalúa en cada tipo de dato

En el proceso de evaluación de riesgo, se realizan diferentes validaciones y cálculos en función del tipo de dato proporcionado por el usuario y las restricciones definidas en el campo de un catalogo. A continuación, se detalla qué se evalúa en cada tipo de dato:

## Tipo de Dato `INTEGER` y `FLOAT`

Para los tipos de dato numéricos `INTEGER` y `FLOAT`, se evalúan los siguientes aspectos:

- **Rango de Valores:** Si el valor proporcionado está dentro del rango permitido definido por los campos `min_range` y `max_range` en el campo del catalogo.
- **Cálculo de Riesgo:** Se calcula el riesgo en función de la posición del valor en el rango de valores permitidos. Los valores fuera del rango tienen un riesgo alto.

## Tipo de Dato `STRING`

Para el tipo de dato `STRING`, se evalúan los siguientes aspectos:

- **Longitud del Valor:** Si la longitud del valor proporcionado está dentro de los límites definidos por los campos `min_range` y `max_range` en el campo del catalogo.
- **Expresión Regular:** Si el valor proporcionado coincide con el patrón de expresión regular (`regex_pattern`) definido en el campo del catalogo.

## Tipo de Dato `ENUM`

Para el tipo de dato `ENUM`, se evalúa lo siguiente:

- **Valor en Enumeración:** Si el valor proporcionado está presente en la lista de valores aceptados (`accepted_values`) definidos en el campo del catalogo .
- **Cálculo de Riesgo:** Se calcula el riesgo en función de la posición del valor en la enumeración. Los valores no presentes en la enumeración tienen un riesgo alto.

## Tipo de Dato `BOOLEAN`

Para el tipo de dato `BOOLEAN`, se evalúa lo siguiente:

- **Valor Booleano:** Si el valor proporcionado es un valor booleano (verdadero o falso).
- **Cálculo de Riesgo:** Los valores `False` tienen un riesgo alto, mientras que los valores `True` tienen un riesgo bajo.
