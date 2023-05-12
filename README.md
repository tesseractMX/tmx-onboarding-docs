# Documentación oficial de implementación de Tesseract Onboarding

## Introducción

Tesseract Onboarding es una herramienta de KYC compuesta por 17 pasos desagregados que tiene como objetivo identificar y verificar la identidad de una persona, a continuación se enlistan los pasos.

-  Registro único con correo, teléfono y RFC o CURP.

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

-  Grabación de auto entrevista.

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
| Parameter  | Type           | Description                         |
| :--------- | :------------- | :---------------------------------- |
| `username` | `string email` | **Requerido**. Correo institucional |
| `password` | `string`       | **Requerido**. Contraseña           |

#### Response
| Key  | Type           | Description                         |
| :--------- | :------------- | :---------------------------------- |
| `username` | `string email` | **Requerido**. Correo institucional |
| `password` | `string`       | **Requerido**. Contraseña           |

### Auth
Devuelve la información de autentición registrada en el token.
```http
  GET /onboarding/api/v0/auth
```



### Clients

Obtiene el listado de todos los clientes registrados 
```http
  GET /onboarding/api/v0/licenses/{licenses_id}/clients
```
donde: 
| Parameter  | Type           | Description                         |
| :--------- | :------------- | :---------------------------------- |
| `licenses_id` | `string` | Clave única de la licencia perteneciente a la institución. |


#### Response
| Key  | Value           | Description                         |
| :--------- | :------------- | :---------------------------------- |
| `_content` | `array` | Colección de objetos cliente |
| `_links` | `object` | Colección de objetos link. |
| `_page` | `object` | Objeto con la metainformación de paginación. |

Crea un nuevo usuario 
```http
  POST /onboarding/api/v0/licenses/{licenses_id}/clients
```
donde:
| Parameter  | Type           | Description                         |
| :--------- | :------------- | :---------------------------------- |
| `licenses_id` | `string` | Clave única de la licencia a la que pertenece. |

#### Body

| Parameter  | Type           | Description                         |
| :--------- | :------------- | :---------------------------------- |
| `RFC` | `string` | Registro Federal de Contribuyentes del nuevo cliente. |
| `CURP` | `string` | Clave Única de Registro de Población del nuevo cliente. |
| `email` | `string` | Correo electrónico |
| `phone_number` | `string` | Número telefónico |

#### Response
| Key  | Value           | Description                         |
| :--------- | :------------- | :---------------------------------- |
| `id` | `string` | Identificador único del cliente. |
| `institution_id` | `string` | Identificador único de la institución a la que pertenece. |
| `name` | `string` | Nombre del cliente. |
| `last_name` | `string` | Apellido paterno del cliente. |
| `surname` | `string` | Apellido materno del cliente. |
| `gender` | `string` | Sexo del cliente. |
| `RFC` | `string` | Registro Federal de Contribuyentes del cliente. |
| `CURP` | `string` | Clave Única de Registro de Población del cliente. |
| `age` | `number` | Edad del cliente. |
| `birthdate` | `string` | Fecha de nacimiento del cliente. |
| `nationality` | `string` | Nacionalidad del cliente. |
| `person_type` | `string` | Tipo de cliente. |
| `email` | `string` | Correo electrónico del cliente. |
| `phone_number` | `string` | Número telefónico del cliente. |
| `address` | `object` | Domicilio del cliente. |
| `location` | `object` | Ubicación del cliente. |
| `identifications` | `array` | Identificaciones del cliente. |
| `facial_mesh` | `object` | Malla facial del cliente. |
| `created_at` | `string` | Representación de la fecha de creación del token en la zona horaria UTC. |
| `updated_at` | `string` | Representación de la ultima fecha de actualización del token en la zona. horaria UTC. |	

Obtiene La información de un cliente específico de la licencia. 
```http
  GET /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}
```
donde: 
| Parameter  | Type           | Description                         |
| :--------- | :------------- | :---------------------------------- |
| `licenses_id` | `string` | Clave única de la licencia pertenenciente a la institución. |
| `client_id` | `string` | Clave única del cliente perteneciente a la licencia. |

#### Response
| Key  | Value           | Description                         |
| :--------- | :------------- | :---------------------------------- |
| `id` | `string` | Identificador único del cliente. |
| `institution_id` | `string` | Identificador único de la institución a la que pertenece. |
| `name` | `string` | Nombre del cliente. |
| `last_name` | `string` | Apellido paterno del cliente. |
| `surname` | `string` | Apellido materno del cliente. |
| `gender` | `string` | Sexo del cliente. |
| `RFC` | `string` | Registro Federal de Contribuyentes del cliente. |
| `CURP` | `string` | Clave Única de Registro de Población del cliente. |
| `age` | `number` | Edad del cliente. |
| `birthdate` | `string` | Fecha de nacimiento del cliente. |
| `nationality` | `string` | Nacionalidad del cliente. |
| `person_type` | `string` | Tipo de cliente. |
| `email` | `string` | Correo electrónico del cliente. |
| `phone_number` | `string` | Número telefónico del cliente. |
| `address` | `object` | Domicilio del cliente. |
| `location` | `object` | Ubicación del cliente. |
| `identifications` | `array` | Identificaciones del cliente. |
| `facial_mesh` | `object` | Malla facial del cliente. |
| `created_at` | `string` | Representación de la fecha de creación del token en la zona horaria UTC. |
| `updated_at` | `string` | Representación de la ultima fecha de actualización del token en la zona. horaria UTC. |	

Actualiza La información de un cliente específico de la licencia. 
```http
  PUT /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}
```
donde: 
| Parameter  | Type           | Description                         |
| :--------- | :------------- | :---------------------------------- |
| `licenses_id` | `string` | Clave única de la licencia pertenenciente a la institución. |
| `client_id` | `string` | Clave única del cliente perteneciente a la licencia. |

#### Body

| Parameter  | Type           | Description                         |
| :--------- | :------------- | :---------------------------------- |
| `id` | `string` | Identificador único del cliente. |
| `institution_id` | `string` | Identificador único de la institución a la que pertenece. |
| `name` | `string` | Nombre del cliente. |
| `last_name` | `string` | Apellido paterno del cliente. |
| `surname` | `string` | Apellido materno del cliente. |
| `gender` | `string` | Sexo del cliente. |
| `RFC` | `string` | Registro Federal de Contribuyentes del cliente. |
| `CURP` | `string` | Clave Única de Registro de Población del cliente. |
| `age` | `number` | Edad del cliente. |
| `birthdate` | `string` | Fecha de nacimiento del cliente. |
| `nationality` | `string` | Nacionalidad del cliente. |
| `person_type` | `string` | Tipo de cliente. |
| `email` | `string` | Correo electrónico del cliente. |
| `phone_number` | `string` | Número telefónico del cliente. |
| `address` | `object` | Domicilio del cliente. |
| `location` | `object` | Ubicación del cliente. |
| `identifications` | `array` | Identificaciones del cliente. |
| `facial_mesh` | `object` | Malla facial del cliente. |
| `created_at` | `string` | Representación de la fecha de creación del token en la zona horaria UTC. |
| `updated_at` | `string` | Representación de la ultima fecha de actualización del token en la zona. horaria UTC. |	

#### Response
| Key  | Value           | Description                         |
| :--------- | :------------- | :---------------------------------- |
| `id` | `string` | Identificador único del cliente. |
| `institution_id` | `string` | Identificador único de la institución a la que pertenece. |
| `name` | `string` | Nombre del cliente. |
| `last_name` | `string` | Apellido paterno del cliente. |
| `surname` | `string` | Apellido materno del cliente. |
| `gender` | `string` | Sexo del cliente. |
| `RFC` | `string` | Registro Federal de Contribuyentes del cliente. |
| `CURP` | `string` | Clave Única de Registro de Población del cliente. |
| `age` | `number` | Edad del cliente. |
| `birthdate` | `string` | Fecha de nacimiento del cliente. |
| `nationality` | `string` | Nacionalidad del cliente. |
| `person_type` | `string` | Tipo de cliente. |
| `email` | `string` | Correo electrónico del cliente. |
| `phone_number` | `string` | Número telefónico del cliente. |
| `address` | `object` | Domicilio del cliente. |
| `location` | `object` | Ubicación del cliente. |
| `identifications` | `array` | Identificaciones del cliente. |
| `facial_mesh` | `object` | Malla facial del cliente. |
| `created_at` | `string` | Representación de la fecha de creación del token en la zona horaria UTC. |
| `updated_at` | `string` | Representación de la ultima fecha de actualización del token en la zona. horaria UTC. |	

Eliminar un cliente específico de la licencia.
```http
  DELETE /onboarding/api/v0/licenses/{licenses_id}/clients/{client_id}
```
donde: 
| Parameter  | Type           | Description                         |
| :--------- | :------------- | :---------------------------------- |
| `licenses_id` | `string` | Clave única de la licencia perteneciente a la institución. |
| `client_id` | `string` | Clave única del cliente perteneciente a la licencia. |
