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

