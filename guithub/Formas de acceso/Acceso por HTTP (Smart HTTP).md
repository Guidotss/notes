# 🌐 Acceso a repos Git por HTTP (Smart HTTP)

## 🧠 ¿Qué es Smart HTTP?

Es el protocolo que usa Git para `clone`, `push`, `pull` usando HTTP/HTTPS.  
Git espera que el servidor entienda una serie de rutas y respuestas binarias especiales.

---

## 🔧 ¿Cómo funciona?

El cliente Git hace peticiones como:

```http
GET  /usuario/repo.git/info/refs?service=git-upload-pack     ← clone / pull
POST /usuario/repo.git/git-upload-pack                       ← data de respuesta

POST /usuario/repo.git/git-receive-pack                      ← push
```

## ✅ ¿Cómo implementarlo fácilmente?

Usá el binario que ya trae Git:

```bash
/usr/lib/git-core/git-http-backend
```

Este programa actúa como intérprete del protocolo Smart HTTP.  
Se ejecuta como CGI desde Apache, NGINX o incluso un backend como Express/Nest con `spawn`.

## 📦 Variables necesarias

Para funcionar, `git-http-backend` necesita variables de entorno:
```bash
GIT_PROJECT_ROOT=/data/repos
GIT_HTTP_EXPORT_ALL=1
PATH_INFO=/usuario/mi-repo.git/git-upload-pack
REQUEST_METHOD=POST
CONTENT_TYPE=application/x-git-upload-pack-request
```
Y el contenido de la petición se le pasa por `stdin`.

## ⚙️ Integración con servidor web (ejemplo con Apache)
```apache 
SetEnv GIT_PROJECT_ROOT /data/repos
SetEnv GIT_HTTP_EXPORT_ALL
ScriptAlias /git/ /usr/lib/git-core/git-http-backend/
```

```bash
git clone https://mi-servidor.com/git/usuario/mi-repo.git
```

## ✅ Ventajas

- Compatible con cualquier cliente Git
    
- Permite acceso por navegador
    
- Ideal para APIs públicas o integración con UI web

## ❌ Desventajas

- Más complejo de configurar que SSH
    
- El protocolo es binario y poco amigable para debug
    
- Necesitás montar servidor web o simularlo manualmente
    

---

## 🔐 Seguridad

- Podés proteger el acceso con autenticación básica (HTTP Basic Auth) o tokens
    
- También podés limitar el acceso con proxies inversos
    

---

## 🧩 ¿Cuándo usarlo?

- Si se quiere que usuarios usen Git sin configurar SSH
    
- Si se va a integrar tu app con una interfaz web
    
- Si necesitás exponer el repositorio en un entorno web tradicional