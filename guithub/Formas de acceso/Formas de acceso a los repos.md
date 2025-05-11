# 🔐 Formas de acceso a los repositorios Git

Para que los usuarios puedan hacer `git clone`, `git push` o `git pull`, tu servidor debe exponer los repositorios de alguna forma.

Las dos formas principales son: **SSH** y **HTTP (Smart HTTP)**.

---

## ✅ [[Acceso por SSH]]

### 📌 ¿Cómo funciona?

- Cada usuario tiene una clave pública registrada en el servidor.
- El servidor usa `git-shell` como shell para limitar lo que el usuario puede hacer.
- Los usuarios se conectan por SSH y hacen `push` o `clone` directamente.

### 📦 Ejemplo de estructura

``` bash 
~/.ssh/authorized_keys /data/repos/usuario/repo.git
```


### 🧪 Ejemplo de uso

```bash
git remote add origin ssh://usuario@mi-servidor:/data/repos/nico/mi-repo.git
git push origin main
```

### ✅ Ventajas

- Muy simple de configurar
    
- No requiere implementar protocolo HTTP
    
- Seguro con claves públicas
    

### ⚠️ Requiere

- Acceso SSH al servidor
    
- Configuración de `authorized_keys`
    
- Opción `git-shell` o scripts seguros para evitar comandos maliciosos


## 🌐 [[guithub/Formas de acceso/Acceso por HTTP (Smart HTTP)|Acceso por HTTP (Smart HTTP)]]

### 📌 ¿Cómo funciona?

Git se comunica con tu servidor mediante HTTP, usando un protocolo binario específico.

Tu servidor debe manejar peticiones como:
``` http
GET  /usuario/repo.git/info/refs?service=git-upload-pack
POST /usuario/repo.git/git-upload-pack
POST /usuario/repo.git/git-receive-pack
```

Esto se puede manejar con `git-http-backend`.

### 🔧 Configuración (usando Apache o Nginx)

Debe exponer el binario:
``` bash
/usr/lib/git-core/git-http-backend
```

Y definir variables de entorno como:

- `GIT_PROJECT_ROOT=/data/repos`
    
- `GIT_HTTP_EXPORT_ALL=1`
    

### ✅ Ventajas

- Compatible con clientes Git estándar
    
- Permite acceso por navegador o detrás de un reverse proxy
    

### ❌ Desventajas

- Más difícil de implementar que SSH
    
- Necesita manejar variables de entorno y contenido binario

## 🧾 Comparación general

| Acceso     | Ventajas                   | Desventajas                           | Requiere                          |
| ---------- | -------------------------- | ------------------------------------- | --------------------------------- |
| SSH        | ✅ Simple, seguro, directo  | ❌ Requiere configurar claves y users  | Claves públicas + shell limitado  |
| Smart HTTP | ✅ Web compatible, flexible | ❌ Protocolo complejo, setup más largo | `git-http-backend` + servidor web |
## 📌 Recomendación

Usá **SSH al inicio**. Es más fácil de implementar y seguro si controlás las claves públicas.

Podés agregar soporte HTTP más adelante si necesitás acceso por navegador o integración con interfaces web.
