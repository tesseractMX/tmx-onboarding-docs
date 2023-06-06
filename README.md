# Documentación oficial de implementación de Tesseract Onboarding

## Introducción

Tesseract Onboarding es una herramienta de KYC compuesta por 17 pasos desagregados que tiene como objetivo identificar y verificar la identidad de una persona. A continuación se enlistan los pasos.

- Registro único con correo, teléfono y RFC o CURP.

- Toma foto de identificación del INE.

- Auto captura OCR y verificación de la identificación INE.

- OCR del comprobante de domicilio, autocaptura, extracción de metadatos y verificación de comprobantes de domicilio (CFE y Telmex).

- Verifación de correo y/o número telefónico mediante OTPs.

- Consulta de la INE ante INE (con datos personales).

- Verificación de la vigencia del INE(CIC, OCR)

- Verificación de la CURP ante RENAPO.

- Consulta de la información de la CURP ante RENAPO.

- Toma de selfie con gesticulación.

- Comparación de foto selfie con identificación.

- Toma fotografía de huellas dactilares.

- Extracción de malla facial.

- Firmado digital y emisión de constancia avalado por la NOM151.

- Consulta en lista negras (Regulatorias y Personalizadas).

- Grabación de auto entrevista.

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





### Clients

Obtiene el listado de todos los clientes registrados 
```http
  GET /onboarding/api/v0/licenses/{licenses_id}/clients
```
donde: 
| Parameter       | Type            | Description                                                 |
| :-------------- | :-------------- | :---------------------------------------------------------- |
| `licenses_id`   | `string`        | Clave única de la licencia perteneciente a la institución.  |


#### Response
| Key         | Value     | Description                                   |
| :---------  | :-------- | :-------------------------------------------- |
| `_content`  | `array`   | Colección de objetos cliente                  |
| `_links`    | `object`  | Colección de objetos link.                    |
| `_page`     | `object`  | Objeto con la metainformación de paginación.  |

Crea un nuevo usuario 
```http
  POST /onboarding/api/v0/licenses/{licenses_id}/clients
```
donde:
| Parameter       | Type     | Description                                    |
| :-------------- | :------- | :--------------------------------------------- |
| `licenses_id`   | `string` | Clave única de la licencia a la que pertenece. |

#### Body

| Parameter       | Type     | Description                                              |
| :-------------- | :------- | :------------------------------------------------------- |
| `RFC`           | `string` | Registro Federal de Contribuyentes del nuevo cliente.    |
| `CURP`          | `string` | Clave Única de Registro de Población del nuevo cliente.  |
| `email`         | `string` | Correo electrónico                                       |
| `phone_number`  | `string` | Número telefónico                                        |

#### Response
| Key               | Value    | Description                                                                              |
| :---------------- | :------- | :--------------------------------------------------------------------------------------- |
| `id`              | `string` | Identificador único del cliente.                                                         |
| `institution_id`  | `string` | Identificador único de la institución a la que pertenece.                                |
| `name`            | `string` | Nombre del cliente.                                                                      |
| `last_name`       | `string` | Apellido paterno del cliente.                                                            |
| `surname`         | `string` | Apellido materno del cliente.                                                            |
| `gender`          | `string` | Sexo del cliente.                                                                        |
| `RFC`             | `string` | Registro Federal de Contribuyentes del cliente.                                          |
| `CURP`            | `string` | Clave Única de Registro de Población del cliente.                                        |
| `age`             | `number` | Edad del cliente.                                                                        |
| `birthdate`       | `string` | Fecha de nacimiento del cliente.                                                         |
| `nationality`     | `string` | Nacionalidad del cliente.                                                                |
| `person_type`     | `string` | Tipo de cliente.                                                                         |
| `email`           | `string` | Correo electrónico del cliente.                                                          |
| `phone_number`    | `string` | Número telefónico del cliente.                                                           |
| `address`         | `object` | Domicilio del cliente.                                                                   |
| `location`        | `object` | Ubicación del cliente.                                                                   |
| `identifications` | `array`  | Identificaciones del cliente.                                                            |
| `facial_mesh`     | `object` | Malla facial del cliente.                                                                |
| `created_at`      | `string` | Representación de la fecha de creación del cliente en la zona horaria UTC.               |
| `updated_at`      | `string` | Representación de la ultima fecha de actualización del cliente en la zona horaria UTC.   |

Obtiene La información de un cliente específico de la licencia. 
```http
  GET /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}
```
donde: 
| Parameter     | Type     | Description                                                  |
| :------------ | :------- | :----------------------------------------------------------- |
| `licenses_id` | `string` | Clave única de la licencia pertenenciente a la institución.  |
| `client_id`   | `string` | Clave única del cliente perteneciente a la licencia.         |

#### Response
| Key               | Value    | Description                                                                              |
| :---------------- | :------- | :--------------------------------------------------------------------------------------- |
| `id`              | `string` | Identificador único del cliente.                                                         |
| `institution_id`  | `string` | Identificador único de la institución a la que pertenece.                                |
| `name`            | `string` | Nombre del cliente.                                                                      |
| `last_name`       | `string` | Apellido paterno del cliente.                                                            |
| `surname`         | `string` | Apellido materno del cliente.                                                            |
| `gender`          | `string` | Sexo del cliente.                                                                        |
| `RFC`             | `string` | Registro Federal de Contribuyentes del cliente.                                          |
| `CURP`            | `string` | Clave Única de Registro de Población del cliente.                                        |
| `age`             | `number` | Edad del cliente.                                                                        |
| `birthdate`       | `string` | Fecha de nacimiento del cliente.                                                         |
| `nationality`     | `string` | Nacionalidad del cliente.                                                                |
| `person_type`     | `string` | Tipo de cliente.                                                                         |
| `email`           | `string` | Correo electrónico del cliente.                                                          |
| `phone_number`    | `string` | Número telefónico del cliente.                                                           |
| `address`         | `object` | Domicilio del cliente.                                                                   |
| `location`        | `object` | Ubicación del cliente.                                                                   |
| `identifications` | `array`  | Identificaciones del cliente.                                                            |
| `facial_mesh`     | `object` | Malla facial del cliente.                                                                |
| `created_at`      | `string` | Representación de la fecha de creación del cliente en la zona horaria UTC.               |
| `updated_at`      | `string` | Representación de la ultima fecha de actualización del cliente en la zona horaria UTC.   |	

Actualiza La información de un cliente específico de la licencia. 
```http
  PUT /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}
```
donde: 
| Parameter     | Type     | Description                                                  |
| :------------ | :------- | :----------------------------------------------------------- |
| `licenses_id` | `string` | Clave única de la licencia pertenenciente a la institución.  |
| `client_id`   | `string` | Clave única del cliente perteneciente a la licencia.         |

#### Body

| Key               | Value    | Description                                                                              |
| :---------------- | :------- | :--------------------------------------------------------------------------------------- |
| `id`              | `string` | Identificador único del cliente.                                                         |
| `institution_id`  | `string` | Identificador único de la institución a la que pertenece.                                |
| `name`            | `string` | Nombre del cliente.                                                                      |
| `last_name`       | `string` | Apellido paterno del cliente.                                                            |
| `surname`         | `string` | Apellido materno del cliente.                                                            |
| `gender`          | `string` | Sexo del cliente.                                                                        |
| `RFC`             | `string` | Registro Federal de Contribuyentes del cliente.                                          |
| `CURP`            | `string` | Clave Única de Registro de Población del cliente.                                        |
| `age`             | `number` | Edad del cliente.                                                                        |
| `birthdate`       | `string` | Fecha de nacimiento del cliente.                                                         |
| `nationality`     | `string` | Nacionalidad del cliente.                                                                |
| `person_type`     | `string` | Tipo de cliente.                                                                         |
| `email`           | `string` | Correo electrónico del cliente.                                                          |
| `phone_number`    | `string` | Número telefónico del cliente.                                                           |
| `address`         | `object` | Domicilio del cliente.                                                                   |
| `location`        | `object` | Ubicación del cliente.                                                                   |
| `identifications` | `array`  | Identificaciones del cliente.                                                            |
| `facial_mesh`     | `object` | Malla facial del cliente.                                                                |
| `created_at`      | `string` | Representación de la fecha de creación del cliente en la zona horaria UTC.               |
| `updated_at`      | `string` | Representación de la ultima fecha de actualización del cliente en la zona horaria UTC.   |

#### Response
| Key               | Value    | Description                                                                              |
| :---------------- | :------- | :--------------------------------------------------------------------------------------- |
| `id`              | `string` | Identificador único del cliente.                                                         |
| `institution_id`  | `string` | Identificador único de la institución a la que pertenece.                                |
| `name`            | `string` | Nombre del cliente.                                                                      |
| `last_name`       | `string` | Apellido paterno del cliente.                                                            |
| `surname`         | `string` | Apellido materno del cliente.                                                            |
| `gender`          | `string` | Sexo del cliente.                                                                        |
| `RFC`             | `string` | Registro Federal de Contribuyentes del cliente.                                          |
| `CURP`            | `string` | Clave Única de Registro de Población del cliente.                                        |
| `age`             | `number` | Edad del cliente.                                                                        |
| `birthdate`       | `string` | Fecha de nacimiento del cliente.                                                         |
| `nationality`     | `string` | Nacionalidad del cliente.                                                                |
| `person_type`     | `string` | Tipo de cliente.                                                                         |
| `email`           | `string` | Correo electrónico del cliente.                                                          |
| `phone_number`    | `string` | Número telefónico del cliente.                                                           |
| `address`         | `object` | Domicilio del cliente.                                                                   |
| `location`        | `object` | Ubicación del cliente.                                                                   |
| `identifications` | `array`  | Identificaciones del cliente.                                                            |
| `facial_mesh`     | `object` | Malla facial del cliente.                                                                |
| `created_at`      | `string` | Representación de la fecha de creación del cliente en la zona horaria UTC.               |
| `updated_at`      | `string` | Representación de la ultima fecha de actualización del cliente en la zona horaria UTC.   |

Eliminar un cliente específico de la licencia.
```http
  DELETE /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}
```
donde: 
| Parameter     | Type     | Description                                                  |
| :------------ | :------- | :----------------------------------------------------------- |
| `licenses_id` | `string` | Clave única de la licencia pertenenciente a la institución.  |
| `client_id`   | `string` | Clave única del cliente perteneciente a la licencia.         |

### Video testimonial

Identificación y transcripción de voz de una persona en un video

```http
  POST /onboarding/api/v0/client/{client_id}/video_statement
```
donde: 
| Parameter     | Type     | Description                                                  |
| :------------ | :------- | :----------------------------------------------------------- |
| `client_id`   | `string` | Clave única del cliente perteneciente a la licencia.         |

### Body:

| Parameter | Type             | Description                                                       |
| :-------- | :--------------- | :---------------------------------------------------------------- |
| `file`    | `string binary`  | **Requerido**. El archivo de video que se va a enviar             |
| `img`     | `string binary`  | **Requerido**. Una imagen de la persona a identificar en el video |


#### Response
| Key                 | Value    | Description                                        |
| :------------------ | :------- | :------------------------------------------------- |
| `statement`         | `string` | Cadena de texto reconocida del video.              |
| `face_match_score`  | `string` | Porcentaje de reconocimiento respecto a la imagen. |

### Ubicación

Guarda el detalle de la ubicación de un cliente.
```http
  POST /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/location
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |

#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `altitude`      | `number` | **Requerido**. Coordenada que refiere la altitud de la ubicación. |
| `latitude`  | `number` | **Requerido**. Coordenada que refiere la latitud de la ubicaciòn. |
#### Response
| Key               | Value    | Description                                                                              |
| :---------------- | :------- | :--------------------------------------------------------------------------------------- |
| `altitude`              | `number` | Coordenada de altitud de la ubicación del cliente.                |
| `latitude`              | `number` | Coordenada de latitud de la ubicación del cliente.                |
| `road`              | `string` | Calle asociada a la ubicación del cliente.                |
| `city`              | `string` | Ciudad asociada a la ubicación del cliente.                |
| `state`              | `string` | Estado asociado a la ubicación del cliente.                |
| `postcode`              | `string` | Código postal asociado a la ubicación del cliente.                |
| `country`              | `string` | País asociado a de la ubicación del cliente.                |
 

Recupera el detalle de la ubicación asociada a un cliente

```http
  GET /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/location
```

#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |

#### Response
| Key               | Value    | Description                                                                              |
| :---------------- | :------- | :--------------------------------------------------------------------------------------- |
| `altitude`              | `number` | Coordenada de altitud de la ubicación del cliente.                |
| `latitude`              | `number` | Coordenada de latitud de la ubicación del cliente.                |
| `road`              | `string` | Calle asociada a la ubicación del cliente.                |
| `city`              | `string` | Ciudad asociada a la ubicación del cliente.                |
| `state`              | `string` | Estado asociado a la ubicación del cliente.                |
| `postcode`              | `string` | Código postal asociado a la ubicación del cliente.                |
| `country`              | `string` | País asociado a de la ubicación del cliente.                |
 
Elimina el detalle de la ubicación asociada a un cliente

```http
  DELETE /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/location
```

#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |

### Dirección

Guarda el detalle de la dirección de un cliente.
```http
  POST /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/address
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |

#### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `street`      | `string` | **Requerido**. Calle asociada al domicilio de un cliente. |
| `number`      | `number` | **Requerido**. Número asociado al domicilio de un cliente. |
| `city`      | `string` | **Requerido**. Ciudad asociada al domicilio de un cliente. |
| `state`      | `string` | **Requerido**. Estado asociado al domicilio de un cliente. |
| `locality`      | `string` | **Requerido**. Localidad o colonia asociada al domicilio de un cliente. |
| `zipcode`      | `string` | **Requerido**. Código postal asociado al domicilio de un cliente. |
| `country`      | `string` | **Requerido**. País asociado al domicilio de un cliente. |

#### Response
| Key               | Value    | Description                                                      |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `street`      | `string` |  Calle asociada al domicilio de un cliente. |
| `number`      | `number` | Número asociado al domicilio de un cliente. |
| `city`      | `string` |  Ciudad asociada al domicilio de un cliente. |
| `state`      | `string` |  Estado asociado al domicilio de un cliente. |
| `locality`      | `string` | Localidad o colonia asociada al domicilio de un cliente. |
| `zipcode`      | `string` | Código postal asociado al domicilio de un cliente. |
| `country`      | `string` |  País asociado al domicilio de un cliente. |


Recupera el detalle de la dirección de un cliente.
```http
  GET /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/address
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |

#### Response
| Key               | Value    | Description                                                         |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `street`      | `string` | Calle asociada al domicilio de un cliente. |
| `number`      | `number` |  Número asociado al domicilio de un cliente. |
| `city`      | `string` | Ciudad asociada al domicilio de un cliente. |
| `state`      | `string` |  Estado asociado al domicilio de un cliente. |
| `locality`      | `string` | Localidad o colonia asociada al domicilio de un cliente. |
| `zipcode`      | `string` | Código postal asociado al domicilio de un cliente. |
| `country`      | `string` |  País asociado al domicilio de un cliente. |

Elimina el detalle de la dirección de un cliente.
```http
  DELETE /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/address
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |

### Comprobante de domicilio 

Guarda el detalle del comprobante de domicilio de un cliente TELMEX y CFE son aceptados.
```http
  POST /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/address_proof
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |

#### Body TELMEX
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`      | `string` | **Requerido**. Nombre asociado al recibo TELMEX. |
| `address`      | `string` | **Requerido**. Dirección asociada al recibo TELMEX. |
| `date`      | `string` | **Requerido**. Fecha del recibo TELMEX. |
| `bill`      | `string` | **Requerido**. Número de factura del recibo TELMEX. |
| `phone_num`      | `string` | **Requerido**. Número de telefono del recibo TELMEX. |
#### Body CFE
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`      | `string` | **Requerido**. Nombre asociado al recibo CFE. |
| `service_no`      | `string` | **Requerido**. Número de servicio asociado al recibo CFE. |
| `rmu`      | `string` | **Requerido**. RMU del recibo CFE. |
| `date`      | `string` | **Requerido**. Fecha limite de pago del recibo CFE. |
| `wattmeter`      | `string` | **Requerido**. Número de medidor que aparece en el recibo CFE. |
| `rate`      | `string` | **Requerido**. Tarifa del recibo CFE. |
| `address`      | `string` | **Requerido**. Domicilio que asociado al recibo CFE. |
#### Response TELMEX
| Key               | Value    | Description                                                     |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`      | `string` | Nombre asociado al recibo TELMEX. |
| `address`      | `string` | Dirección asociada al recibo TELMEX. |
| `date`      | `string` | Fecha del recibo TELMEX. |
| `bill`      | `string` | Número de factura del recibo TELMEX. |
| `phone_num`      | `string` |  Número de telefono del recibo TELMEX. |
 #### Response CFE
| Key               | Value    | Description                                              |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`      | `string` |  Nombre asociado al recibo CFE. |
| `service_no`      | `string` | Número de servicio asociado al recibo CFE. |
| `rmu`      | `string` | RMU del recibo CFE. |
| `date`      | `string` | Fecha limite de pago del recibo CFE. |
| `wattmeter`      | `string` |  Número de medidor que aparece en el recibo CFE. |
| `rate`      | `string` |  Tarifa del recibo CFE. |
| `address`      | `string` |  Domicilio que asociado al recibo CFE. |
 
Recupera el detalle del comprobante de domicilio de un cliente.
```http
  GET /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/address_proof
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |

#### Response TELMEX
| Key               | Value    | Description                                                        |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`      | `string` |  Nombre asociado al recibo TELMEX. |
| `address`      | `string` |  Dirección asociada al recibo TELMEX. |
| `date`      | `string` | Fecha del recibo TELMEX. |
| `bill`      | `string` |  Número de factura del recibo TELMEX. |
| `phone_num`      | `string` |  Número de telefono del recibo TELMEX. |
#### Response CFE
| Key               | Value    | Description                                                          |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`      | `string` |  Nombre asociado al recibo CFE. |
| `service_no`      | `string` |  Número de servicio asociado al recibo CFE. |
| `rmu`      | `string` |  RMU del recibo CFE. |
| `date`      | `string` | Fecha limite de pago del recibo CFE. |
| `wattmeter`      | `string` | Número de medidor que aparece en el recibo CFE. |
| `rate`      | `string` | Tarifa del recibo CFE. |
| `address`      | `string` | Domicilio que asociado al recibo CFE. |

Modifica el detalle del comprobante de domicilio de un cliente.
```http
  PUT /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/address_proof
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |

#### Body TELMEX
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `data`      | `json` | **Requerido**. Clave que contiene todos los datos a modificar, el resto de las claves descritas en este apartado deben ir dentro de este valor. |
| `name`      | `string` | **Requerido**. Nombre asociado al recibo TELMEX. |
| `address`      | `string` | **Requerido**. Dirección asociada al recibo TELMEX. |
| `date`      | `string` | **Requerido**. Fecha del recibo TELMEX. |
| `bill`      | `string` | **Requerido**. Número de factura del recibo TELMEX. |
| `phone_num`      | `string` | **Requerido**. Número de telefono del recibo TELMEX. |
#### Body CFE
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `data`      | `json` | **Requerido**. Clave que contiene todos los datos a modificar, el resto de las claves descritas en este apartado deben ir dentro de este valor. |
| `name`      | `string` | **Requerido**. Nombre asociado al recibo CFE. |
| `service_no`      | `string` | **Requerido**. Número de servicio asociado al recibo CFE. |
| `rmu`      | `string` | **Requerido**. RMU del recibo CFE. |
| `date`      | `string` | **Requerido**. Fecha limite de pago del recibo CFE. |
| `wattmeter`      | `string` | **Requerido**. Número de medidor que aparece en el recibo CFE. |
| `rate`      | `string` | **Requerido**. Tarifa del recibo CFE. |
| `address`      | `string` | **Requerido**. Domicilio que asociado al recibo CFE. |
#### Response TELMEX
| Key               | Value    | Description                                                     |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`      | `string` |  Nombre asociado al recibo TELMEX. |
| `address`      | `string` |  Dirección asociada al recibo TELMEX. |
| `date`      | `string` |  Fecha del recibo TELMEX. |
| `bill`      | `string` | Número de factura del recibo TELMEX. |
| `phone_num`      | `string` | Número de telefono del recibo TELMEX. |

 #### Response CFE
| Key               | Value    | Description                                              |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`      | `string` |  Nombre asociado al recibo CFE. |
| `service_no`      | `string` | Número de servicio asociado al recibo CFE. |
| `rmu`      | `string` | RMU del recibo CFE. |
| `date`      | `string` | Fecha limite de pago del recibo CFE. |
| `wattmeter`      | `string` |  Número de medidor que aparece en el recibo CFE. |
| `rate`      | `string` | Tarifa del recibo CFE. |
| `address`      | `string` | Domicilio que asociado al recibo CFE. |

Elimina el detalle del comprobante de domicilio de un cliente.
```http
  DELETE /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/address_proof
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |

### Identificaciones 

Guarda el detalle de las identificaciones de un cliente INE de tipo C, DEF y GH son aceptados.
```http
  POST /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/id
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |

#### Body INE tipo C
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `lastname`      | `string` | **Requerido**. Apellido paterno que aparece en la INE del cliente. |
| `surname`      | `string` | **Requerido**. Apellido materno que aparece en la INE del cliente. |
| `name`      | `string` | **Requerido**. Nombre que aparece en la INE del cliente. |
| `address`      | `string` | **Requerido**. Domicilio que aparece en la INE del cliente. |
| `gender`      | `string` | **Requerido**. Sexo que aparece en la INE del cliente. |
| `key`      | `string` | **Requerido**. Clave de elector del cliente. |
| `curp`      | `string` | **Requerido**. CURP que aparece en la INE del cliente. |
| `regDate`      | `string` | **Requerido**. Fecha de registro del cliente ante INE. |
| `age`      | `string` | **Requerido**. Edad del cliente a la fecha de emisión de la identificación. |
| `section`      | `string` | **Requerido**. Sección a la que pertenece la INE del cliente. |
| `validity`      | `string` | **Requerido**. Año de validación de la INE. |
| `state`      | `string` | **Requerido**. Estado al que pertenece la INE del cliente. |
| `town`      | `string` | **Requerido**. Ciudad a la que pertenece la INE del cliente. |
| `locality`      | `string` | **Requerido**. Localidad a la que pertenece la INE del cliente. |
| `issue`      | `string` | **Requerido**. Fecha de emisión al que pertenece la INE del cliente. |
| `folio`      | `string` | **Requerido**. Folio asociado a la INE del cliente. |
    
#### Body INE tipo DEF
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `lastname`      | `string` | **Requerido**. Apellido paterno que aparece en la INE del cliente. |
| `surname`      | `string` | **Requerido**. Apellido materno que aparece en la INE del cliente. |
| `name`      | `string` | **Requerido**. Nombre que aparece en la INE del cliente. |
| `address`      | `string` | **Requerido**. Domicilio que aparece en la INE del cliente. |
| `gender`      | `string` | **Requerido**. Sexo que aparece en la INE del cliente. |
| `key`      | `string` | **Requerido**. Clave de elector del cliente. |
| `curp`      | `string` | **Requerido**. CURP que aparece en la INE del cliente. |
| `regDate`      | `string` | **Requerido**. Fecha de registro del cliente ante INE. |
| `birthdate`      | `string` | **Requerido**. Fecha de nacimiento del cliente. |
| `section`      | `string` | **Requerido**. Sección a la que pertenece la INE del cliente. |
| `validity`      | `string` | **Requerido**. Año de validación de la INE. |
| `state`      | `string` | **Requerido**. Estado al que pertenece la INE del cliente. |
| `town`      | `string` | **Requerido**. Ciudad a la que pertenece la INE del cliente. |
| `locality`      | `string` | **Requerido**. Localidad a la que pertenece la INE del cliente. |
| `issue`      | `string` | **Requerido**. Fecha de emisión al que pertenece la INE del cliente. |

#### Body INE tipo GH
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `lastname`      | `string` | **Requerido**. Apellido paterno que aparece en la INE del cliente. |
| `surname`      | `string` | **Requerido**. Apellido materno que aparece en la INE del cliente. |
| `name`      | `string` | **Requerido**. Nombre que aparece en la INE del cliente. |
| `address`      | `string` | **Requerido**. Domicilio que aparece en la INE del cliente. |
| `gender`      | `string` | **Requerido**. Sexo que aparece en la INE del cliente. |
| `key`      | `string` | **Requerido**. Clave de elector del cliente. |
| `curp`      | `string` | **Requerido**. CURP que aparece en la INE del cliente. |
| `regDate`      | `string` | **Requerido**. Fecha de registro del cliente ante INE. |
| `birthdate`      | `string` | **Requerido**. Fecha de nacimiento del cliente. |
| `section`      | `string` | **Requerido**. Sección a la que pertenece la INE del cliente. |
| `validity`      | `string` | **Requerido**. Año de validación de la INE. |
#### Response INE
| Key        | Type           | Description                                                      |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `identification_id`      | `string` | ID unico asociado a la identificación. |
| `type`      | `string` | Tipo de identificacioón. |
| `front`      | `json` |  Conjunto de información que aparece en la parte frontal de la INE. |
| `back`      | `json` |  Conjunto de información que aparece en la parte trasera de la INE. |
| `created_at`        | `date`         | Fecha de creación de la aplicación         |
| `updated_at`        | `date`         | Fecha de modificación de la aplicación     |


Recupera el detalle de todas las identificaciones de un cliente.
```http
  GET /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/id
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |

#### Response 
| Key        | Type           | Description                                                      |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `identification_id`      | `string` | ID unico asociado a la identificación. |
| `type`      | `string` |  Tipo de identificacioón. |
| `front`      | `json` |  Conjunto de información que aparece en la parte frontal de la INE. |
| `back`      | `json` | Conjunto de información que aparece en la parte trasera de la INE. |
| `created_at`        | `date`         | Fecha de creación de la aplicación         |
| `updated_at`        | `date`         | Fecha de modificación de la aplicación     |

Recupera el detalle de una identificación de un cliente.
```http
  GET /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/id/{identification_id}
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |
| `identification_id`  | `string` | Clave única de la identificación perteneciente al cliente.      |

#### Response
| Key        | Type           | Description                                                      |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `identification_id`      | `string` |  ID unico asociado a la identificación. |
| `type`      | `string` | Tipo de identificacioón. |
| `front`      | `json` | Conjunto de información que aparece en la parte frontal de la INE. |
| `back`      | `json` | Conjunto de información que aparece en la parte trasera de la INE. |
| `created_at`        | `date`         | Fecha de creación de la aplicación         |
| `updated_at`        | `date`         | Fecha de modificación de la aplicación     |


Guarda el detalle de la parte trasera de la identificacioón de un cliente.
```http
  PUT /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/id/{identification_id}/back
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |
| `identification_id`  | `string` | Clave única de la identificación perteneciente al cliente.      |
#### Body tipo DEF y GH
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `data`      | `json` | **Requerido**. Clave que contiene todos los datos a modificar, el resto de las claves descritas en este apartado deben ir dentro de este valor. |
| `lastname`      | `string` | **Requerido**. Apellido paterno que aparece en la INE del cliente. |
| `surname`      | `string` | **Requerido**. Apellido materno que aparece en la INE del cliente. |
| `name`      | `string` | **Requerido**. Nombre que aparece en la INE del cliente. |
| `birth_date`      | `string` | **Requerido**. Fecha de nacimiento del cliente. |
| `gender`      | `string` | **Requerido**. Sexo que aparece en la INE del cliente. |
| `expiry_date`      | `string` | **Requerido**. Fecha de expiración de la INE del cliente. |
| `nationality`      | `string` | **Requerido**. Nacionalidad del cliente. |
| `issue_number`      | `string` | **Requerido**. Número de expedición de la INE del cliente. |
| `FUAR`      | `string` | **Requerido**. FUAR de la INE del cliente. |
| `check_digit`      | `string` | **Requerido**. Dígito de verificación la INE del cliente. |
| `type`      | `string` | **Requerido**. Tipo de documento. |
| `country`      | `string` | **Requerido**. País de expedición de la INE. |
| `cic`      | `string` | **Requerido**. CIC de la INE. |
| `cic_id`      | `string` | **Requerido**. ID perteneciente al CIC de la INE. |
| `ocr`      | `string` | **Requerido**. OCR de la INE. |
| `citizenID`      | `string` | **Requerido**. Clave de ciudadano asociado a la INE. |
| `section`      | `string` | **Requerido**. Sección a la que pertenece el cliente. |
#### Body tipo C
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `data`      | `json` | **Requerido**. Clave que contiene todos los datos a modificar, el resto de las claves descritas en este apartado deben ir dentro de este valor. |
| `ocr`      | `string` | **Requerido**. OCR asociado a la INE del cliente. |

#### Response 
| Key        | Type           | Description                                                      |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `identification_id`      | `string` | ID unico asociado a la identificación. |
| `type`      | `string` |  Tipo de identificacioón. |
| `front`      | `json` | Conjunto de información que aparece en la parte frontal de la INE. |
| `back`      | `json` |  Conjunto de información que aparece en la parte trasera de la INE. |
| `created_at`        | `date`         | Fecha de creación de la aplicación         |
| `updated_at`        | `date`         | Fecha de modificación de la aplicación     |

Modifica el detalle de las identificaciones de un cliente.
```http
  PUT /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/id/{identification_id}
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |
| `identification_id`  | `string` | Clave única de la identificación perteneciente al cliente.      |
### Body
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `front`      | `json` | Coleccion de parámetros a editar de los datos pertenecientes a la parte frontal de la INE, soporta INE tipo C, DEF y GH.          |
| `back`  | `json` | Coleccion de parámetros a editar de los datos pertenecientes a la parte frontal de la INE, soporta INE tipo C, DEF y GH.          |

#### Body INE tipo C frontal
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `lastname`      | `string` | **Requerido**. Apellido paterno que aparece en la INE del cliente. |
| `surname`      | `string` | **Requerido**. Apellido materno que aparece en la INE del cliente. |
| `name`      | `string` | **Requerido**. Nombre que aparece en la INE del cliente. |
| `address`      | `string` | **Requerido**. Domicilio que aparece en la INE del cliente. |
| `gender`      | `string` | **Requerido**. Sexo que aparece en la INE del cliente. |
| `key`      | `string` | **Requerido**. Clave de elector del cliente. |
| `curp`      | `string` | **Requerido**. CURP que aparece en la INE del cliente. |
| `regDate`      | `string` | **Requerido**. Fecha de registro del cliente ante INE. |
| `age`      | `string` | **Requerido**. Edad del cliente a la fecha de emisión de la identificación. |
| `section`      | `string` | **Requerido**. Sección a la que pertenece la INE del cliente. |
| `validity`      | `string` | **Requerido**. Año de validación de la INE. |
| `state`      | `string` | **Requerido**. Estado al que pertenece la INE del cliente. |
| `town`      | `string` | **Requerido**. Ciudad a la que pertenece la INE del cliente. |
| `locality`      | `string` | **Requerido**. Localidad a la que pertenece la INE del cliente. |
| `issue`      | `string` | **Requerido**. Fecha de emisión al que pertenece la INE del cliente. |
| `folio`      | `string` | **Requerido**. Folio asociado a la INE del cliente. |
    
#### Body INE tipo DEF frontal
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `lastname`      | `string` | **Requerido**. Apellido paterno que aparece en la INE del cliente. |
| `surname`      | `string` | **Requerido**. Apellido materno que aparece en la INE del cliente. |
| `name`      | `string` | **Requerido**. Nombre que aparece en la INE del cliente. |
| `address`      | `string` | **Requerido**. Domicilio que aparece en la INE del cliente. |
| `gender`      | `string` | **Requerido**. Sexo que aparece en la INE del cliente. |
| `key`      | `string` | **Requerido**. Clave de elector del cliente. |
| `curp`      | `string` | **Requerido**. CURP que aparece en la INE del cliente. |
| `regDate`      | `string` | **Requerido**. Fecha de registro del cliente ante INE. |
| `birthdate`      | `string` | **Requerido**. Fecha de nacimiento del cliente. |
| `section`      | `string` | **Requerido**. Sección a la que pertenece la INE del cliente. |
| `validity`      | `string` | **Requerido**. Año de validación de la INE. |
| `state`      | `string` | **Requerido**. Estado al que pertenece la INE del cliente. |
| `town`      | `string` | **Requerido**. Ciudad a la que pertenece la INE del cliente. |
| `locality`      | `string` | **Requerido**. Localidad a la que pertenece la INE del cliente. |
| `issue`      | `string` | **Requerido**. Fecha de emisión al que pertenece la INE del cliente. |

#### Body INE tipo GH frontal
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `lastname`      | `string` | **Requerido**. Apellido paterno que aparece en la INE del cliente. |
| `surname`      | `string` | **Requerido**. Apellido materno que aparece en la INE del cliente. |
| `name`      | `string` | **Requerido**. Nombre que aparece en la INE del cliente. |
| `address`      | `string` | **Requerido**. Domicilio que aparece en la INE del cliente. |
| `gender`      | `string` | **Requerido**. Sexo que aparece en la INE del cliente. |
| `key`      | `string` | **Requerido**. Clave de elector del cliente. |
| `curp`      | `string` | **Requerido**. CURP que aparece en la INE del cliente. |
| `regDate`      | `string` | **Requerido**. Fecha de registro del cliente ante INE. |
| `birthdate`      | `string` | **Requerido**. Fecha de nacimiento del cliente. |
| `section`      | `string` | **Requerido**. Sección a la que pertenece la INE del cliente. |
| `validity`      | `string` | **Requerido**. Año de validación de la INE. |
#### Body tipo DEF y GH trasera
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `lastname`      | `string` | **Requerido**. Apellido paterno que aparece en la INE del cliente. |
| `surname`      | `string` | **Requerido**. Apellido materno que aparece en la INE del cliente. |
| `name`      | `string` | **Requerido**. Nombre que aparece en la INE del cliente. |
| `birth_date`      | `string` | **Requerido**. Fecha de nacimiento del cliente. |
| `gender`      | `string` | **Requerido**. Sexo que aparece en la INE del cliente. |
| `expiry_date`      | `string` | **Requerido**. Fecha de expiración de la INE del cliente. |
| `nationality`      | `string` | **Requerido**. Nacionalidad del cliente. |
| `issue_number`      | `string` | **Requerido**. Número de expedición de la INE del cliente. |
| `FUAR`      | `string` | **Requerido**. FUAR de la INE del cliente. |
| `check_digit`      | `string` | **Requerido**. Dígito de verificación la INE del cliente. |
| `type`      | `string` | **Requerido**. Tipo de documento. |
| `country`      | `string` | **Requerido**. País de expedición de la INE. |
| `cic`      | `string` | **Requerido**. CIC de la INE. |
| `cic_id`      | `string` | **Requerido**. ID perteneciente al CIC de la INE. |
| `ocr`      | `string` | **Requerido**. OCR de la INE. |
| `citizenID`      | `string` | **Requerido**. Clave de ciudadano asociado a la INE. |
| `section`      | `string` | **Requerido**. Sección a la que pertenece el cliente. |
#### Body tipo C trasera
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `ocr`      | `string` | **Requerido**. OCR asociado a la INE del cliente. |

#### Response 
| Key        | Type           | Description                                                      |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `identification_id`      | `string` |ID unico asociado a la identificación. |
| `type`      | `string` | Tipo de identificacioón. |
| `front`      | `json` |  Conjunto de información que aparece en la parte frontal de la INE. |
| `back`      | `json` | Conjunto de información que aparece en la parte trasera de la INE. |
| `created_at`        | `date`         | Fecha de creación de la aplicación         |
| `updated_at`        | `date`         | Fecha de modificación de la aplicación     |

Elimina el detalle de una identificación de un cliente.
```http
  DELETE /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/id/{identification_id}
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |
| `identification_id`  | `string` | Clave única de la identificación perteneciente al cliente.      |

### OCR

Recupera la información de identificaciones (soportadas INE de tipo C, DEF y GH, parte frontal y trasera) y comprobantes de domicilio (soportados TELMEX Y CFE) y los asocia a un cliente.
```http
  POST /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}/ocr/{ocr_type}
```
#### Donde:
| Parameter         | Type        | Description                                                       |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `licenses_id`      | `string` | Clave única de la licencia perteneciente a la institución.          |
| `client_id`  | `string` | Clave única del cliente perteneciente a la licencia.      |
| `ocr_type`  | `string` | **bill** para comprobantes de domicio e **id** para identificaciones  |
### body
| Parameter | Type             | Description                                                       |
| :-------- | :--------------- | :---------------------------------------------------------------- |
| `file`    | `string binary`  | **Requerido**. El archivo (imagen o PDF) de donde se desea extraer la información            |
#### Response para tipo id
| Key        | Type           | Description                                                      |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `identification_id`      | `string` | ID unico asociado a la identificación. |
| `type`      | `string` |  Tipo de identificacioón. |
| `front`      | `json` | Conjunto de información que aparece en la parte frontal de la INE. |
| `back`      | `json` | Conjunto de información que aparece en la parte trasera de la INE. |
| `created_at`        | `date`         | Fecha de creación de la aplicación         |
| `updated_at`        | `date`         | Fecha de modificación de la aplicación     |

#### Response tipo bill TELMEX
| Key               | Value    | Description                                                     |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`      | `string` |  Nombre asociado al recibo TELMEX. |
| `address`      | `string` |  Dirección asociada al recibo TELMEX. |
| `date`      | `string` |  Fecha del recibo TELMEX. |
| `bill`      | `string` | Número de factura del recibo TELMEX. |
| `phone_num`      | `string` |  Número de telefono del recibo TELMEX. |

#### Response tipo bill CFE
 
| Key               | Value    | Description                                              |
| :---------------- | :---------- | :---------------------------------------------------------------- |
| `name`      | `string` | Nombre asociado al recibo CFE. |
| `service_no`      | `string` | Número de servicio asociado al recibo CFE. |
| `rmu`      | `string` |  RMU del recibo CFE. |
| `date`      | `string` | Fecha limite de pago del recibo CFE. |
| `wattmeter`      | `string` |  Número de medidor que aparece en el recibo CFE. |
| `rate`      | `string` |  Tarifa del recibo CFE. |
| `address`      | `string` |  Domicilio que asociado al recibo CFE. |



