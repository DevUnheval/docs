[![N|Solid](https://www.unheval.edu.pe/portal/wp-content/uploads/2021/09/UNHEVAL-LOGO-1-1.png)](https://ws.unheval.edu.pe)

## Endpoints de Integración Sistema de Gestión Docente
Los siguientes endpoints fueron creados para la integración del registro y configuracion de notas del sistema de gestion docente y el aula virtual.
## 1. Endpoint para obtener Token de Acceso
Para obtener el token de acceso es necesario hace una petición POST al siguiente url:

- **URL**:
```
"${DOMINIO_PROPORCIONADO}/api/token"
```
- **Método HTTP**: POST

### Parámetros

| Nombre | Descripcion | Tipo | Requerido |
| ------ | ------ |------ | --------|
| client_id | ID proporcionado por la UNHEVAL  | texto | Si |
| client_secret | proporcionado por la UNHEVAL  | texto | Si |

### Respuesta exitosa

```json
{
  "success" : true
  "access_token" : "TOKEN_DE_ACCESO",
  "token_type" : "Bearer",
}
```
### Ejemplo peticion (javascript)

```javascript
//Obtener token de acceso
const token_acceso = async () => {
  const url = `${DOMINIO_PROPORCIONADO}/api/token`;
  const data = {
    client_id: 'TU_CLIENT_ID',
    client_secret: 'TU_CLIENT_SECRET',
  };

  const response = await fetch(url, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded'
    },
    body: new URLSearchParams(data)
  });

  const responseData = await response.json();
  return responseData.access_token;
};

token_acceso().then(token => {
  console.log('access_token es: '+token);
}).catch(error => {
  console.log(error);
});
```

## 2. Acceder a los endpoints protegidos

## Endpoint para obtener la configuracion de notas del curso

- **URL**:
```
${DOMINIO_PROPORCIONADO}/api/v1/lectiva/notas/configuracion
```
- **Método HTTP**: GET

### Parametros
| Nombre | Descripcion | Tipo | Requerido |
| ------ | ------ |------ |------|
| curso_id | ID único del curso | string(24) | Si |

### Endpoint protegido

Se debe incluir el encabezado de autorización para que la petición sea exitosa

```json 
{
    "Authorization" : "Bearer TOKEN_DE_ACCESO"
}
``` 
### Respuesta exitosa

```json
{
  "succes": true,
  "message": "Configuración de notas obtenida correctamente",
  "data": {
    "curso_id" : "ID_UNICO_DEL_CURSO",
    "curso" : "NOMBRE_DEL_CURSO",
    "grupo" : "GRUPO_DEL_CURSO",
    "evaluaciones": [
      {
        "key": "Nota01",
        "peso": 1,
        "descripcion": "Nota 1"
      },
        ... //Máximo 20 evaluaciones
    ]
  }
}
```

### Ejemplo de Petición (JavaScript)

```javascript
//Acceder al endpoint protegido
const getConfiguracionNotas = async (token) => {
  const url = `${DOMINIO_PROPORCIONADO}/api/v1/lectiva/notas/configuracion?curso_id=${ID_UNICO_DEL_CURSO}`;
  
  const response = await fetch(url, {
    method: 'GET',
    headers: {
      'Authorization': `Bearer ${token}`
    },
  });

  const responseData = await response.json();
  return responseData;
};

getConfiguracionNotas(token_acceso).then(response => {
  console.log('Respuesta del Endpoint Protegido:', response);
}).catch(error => {
  console.error(error)
})
```
## Endpoint para registrar la configuracion de notas del curso

- **URL**:
```
${DOMINIO_PROPORCIONADO}/api/v1/lectiva/notas/configuracion
```
- **Método HTTP**: POST

### Parametros
| Nombre | Descripcion | Tipo | Requerido |
| ------ | ------ |------ |------|
| curso_id | ID único del curso | string(24) | Si |
| evaluaciones | Arreglo del objetos de las evaluaciones | Array(20) | Si |

Ejemplo
```json
{
    "curso_id" : "ID_UNICO_DEL_CURSO",
    "evaluaciones" : [
        {
            "peso" : "1",
            "descripcion" : "Primer examen"
        },
        {
            "peso" : "1",
            "descripcion" : "Segundo examen"
        },
        {
            "peso" : "2",
            "descripcion" : "Proyecto final"
        }, 
        {
            "peso" : "1",
            "descripcion" : "Exposiciones"
        }
    ]
}
```

### Endpoint protegido

Se debe incluir el encabezado de autorización para que la petición sea exitosa

```json 
{
    "Authorization" : "Bearer TOKEN_DE_ACCESO"
}
``` 
### Respuesta exitosa

```json
{
  "succes": true,
  "message": "Configuración de notas registrada correctamente",
  "data": null
}
```

### Ejemplo de Petición (JavaScript)

```javascript
//Acceder al endpoint protegido
const storeConfiguracionNotas = async (token) => {
  const url = `${DOMINIO_PROPORCIONADO}/api/v1/lectiva/notas/configuracion`;
  const data = {
    "curso_id" : "ID_UNICO_DEL_CURSO",
    "evaluaciones" : [
      { "peso" : "1", "descripcion" : "Primer examen" },
      { "peso" : "1", "descripcion" : "Segundo examen" },
      { "peso" : "2", "descripcion" : "Proyecto final" }, 
      { "peso" : "1", "descripcion" : "Exposiciones" }
    ]
  };

  const response = await fetch(url, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`
      'Content-Type', 'application/x-www-form-urlencoded'
    },
    body: new URLSearchParams(data)
  });

  const responseData = await response.json();
  return responseData;
};

storeConfiguracionNotas(token_acceso).then(response => {
  console.log('Respuesta del Endpoint Protegido:', response);
}).catch(erro => {
  console.error(error);
})

```

## Endpoint para registrar las notas de 1 alumno

- **URL**:
```
${DOMINIO_PROPORCIONADO}/api/v1/lectiva/notas/store
```
- **Método HTTP**: POST

### Parametros
| Nombre | Descripcion | Tipo | Requerido |
| ------ | ------ |------ |------|
| curso_id | ID único del curso | string(24) | Si |
| alumno_id | ID único del alumno | string(10) | Si |
| cod_profe | DNI del docente | string(10) | Si |
| notas | Arreglo con las notas del alumno | Array(20) | Si |


Ejemplo
```json
{
    "curso_id" : "ID_UNICO_DEL_CURSO",
    "id_alumno" : "CODIGO_DEL_ALUMNO",
    "cod_profe" : "DNI_DEL_DOCENTE",
    "notas" : [
        { "key" : "Nota01", "valor" : "12"},
        { "key" : "Nota02", "valor" : "15.5"},
        { "key" : "Nota03", "valor" : "13.5"},
        { "key" : "Nota04", "valor" : "12.0"}
    ]
}
```

### Endpoint protegido

Se debe incluir el encabezado de autorización para que la petición sea exitosa

```json 
{
    "Authorization" : "Bearer TOKEN_DE_ACCESO"
}
``` 
### Respuesta exitosa

```json
{
  "succes": true,
  "message": "Configuración de notas registrada correctamente",
  "data": null
}
```

### Ejemplo de Petición (JavaScript)

```javascript
//Acceder al endpoint protegido
const storeNotasAlumno = async (token) => {
  const url = `${DOMINIO_PROPORCIONADO}/api/v1/lectiva/notas/store`;
  const data = {
    "curso_id" : "ID_UNICO_DEL_CURSO",
    "id_alumno" : "CODIGO_DEL_ALUMNO",
    "cod_profe" : "DNI_DEL_DOCENTE",
    "notas" : [
        { "key" : "Nota01", "valor" : "12"},
        { "key" : "Nota02", "valor" : "15.5"},
        { "key" : "Nota03", "valor" : "13.5"},
        { "key" : "Nota04", "valor" : "12.0"}
    ]
  };

  const response = await fetch(url, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`
      'Content-Type', 'application/x-www-form-urlencoded'
    },
    body: new URLSearchParams(data)
  });

  const responseData = await response.json();
  return responseData;
};

storeNotasAlumno.then(response => {
  console.log("Respuesta del Endpoint Protegido :", response)
}).catcha(error => {
  console.error(error)
})
```
