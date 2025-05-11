
# 🧩 `git-http-backend`: servidor Git por HTTP

## 🧠 ¿Qué es?

Es un programa que viene con Git. Se encarga de manejar las rutas HTTP especiales que usa Git para `clone`, `push` y `pull` usando el protocolo Smart HTTP.

Está diseñado para ejecutarse como un CGI (Common Gateway Interface).

---

## 📍 Ubicación típica

```bash
/usr/lib/git-core/git-http-backend
```

## 🛠 ¿Qué hace exactamente?

Interpreta y responde a las siguientes rutas del protocolo Git:

|Ruta|Función|
|---|---|
|`/info/refs?service=git-upload-pack`|Leer (clone/pull)|
|`/git-upload-pack`|Enviar datos al cliente|
|`/git-receive-pack`|Recibir push del cliente|

Git espera respuestas binario-formateadas con el protocolo pkt-line.

## ⚙️ Variables de entorno necesarias

`git-http-backend` necesita varias variables para funcionar correctamente:
```bash
GIT_PROJECT_ROOT=/data/repos
GIT_HTTP_EXPORT_ALL=1
PATH_INFO=/usuario/mi-repo.git/git-upload-pack
REQUEST_METHOD=POST
CONTENT_TYPE=application/x-git-upload-pack-request
```

Además:

- Los datos del cliente llegan por `stdin`
    
- La respuesta debe ir por `stdout`

## 🧪 Ejemplo mínimo (manual)
```bash
GIT_PROJECT_ROOT=/data/repos \
GIT_HTTP_EXPORT_ALL=1 \
PATH_INFO=/nico/mi-repo.git/info/refs \
REQUEST_METHOD=GET \
QUERY_STRING="service=git-upload-pack" \
/usr/lib/git-core/git-http-backend
```

## 🛠 Cómo integrarlo con tu backend (Node/Nest)

Podés usar `spawn` para ejecutarlo desde tu backend:
```ts
import { spawn } from 'child_process';

const child = spawn('/usr/lib/git-core/git-http-backend', {
  env: {
    ...process.env,
    GIT_PROJECT_ROOT: '/data/repos',
    GIT_HTTP_EXPORT_ALL: '1',
    PATH_INFO: req.path,
    REQUEST_METHOD: req.method,
    CONTENT_TYPE: req.headers['content-type'],
    QUERY_STRING: req.url.split('?')[1] || '',
  },
  stdio: ['pipe', 'pipe', 'pipe'],
});

req.pipe(child.stdin);
child.stdout.pipe(res);
```


## 🔐 Seguridad

- No expone el sistema si está bien configurado
    
- Pero deberías protegerlo con autenticación o controlar el acceso por IP/token si los repos no son públicos

## 📌 Recomendación

Usá `git-http-backend` si:

- Querés que Git funcione por HTTP
    
- No querés implementar el protocolo Smart HTTP desde cero
    
- Tenés un servidor Apache, NGINX, o un backend Node con control del entorno