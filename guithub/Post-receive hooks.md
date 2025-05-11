
# 🪝 Post-receive hooks en repositorios bare

## 🧠 ¿Qué es un hook?

Un **hook** es un script que Git ejecuta automáticamente cuando ocurre un evento.

En un repositorio bare, el hook más útil es `post-receive`, que se ejecuta **después de un push exitoso**.

---

## 📍 Ubicación

El archivo del hook va en:
```bash
/data/repos/usuario/repo.git/hooks/post-receive
```


Debe tener permisos de ejecución:
```bash
chmod +x post-receive
```

## 🧪 Ejemplo básico

```bash 
#!/bin/bash
echo "Push recibido en $(date)" >> /var/log/git-push.log
```

Este hook se ejecuta cada vez que alguien hace `git push`.

## 📦 ¿Qué información recibe?

El hook lee de `stdin` las referencias actualizadas. Cada línea tiene:
```bash 
<old_sha> <new_sha> <ref>
```

```git 
27b2e07... 5a8d3fa... refs/heads/main
```

Podés usar eso para:

- Detectar qué branch fue modificado
    
- Saber el commit anterior y el nuevo


## 🔧 Ejemplo real: notificar al backend

```bash 
#!/bin/bash
while read oldrev newrev refname
do
  curl -X POST http://localhost:3000/hooks/push \
       -H "Content-Type: application/json" \
       -d "{\"repo\": \"$PWD\", \"ref\": \"$refname\", \"old\": \"$oldrev\", \"new\": \"$newrev\"}"
done
```

## Usos comunes

- Guardar logs
    
- Notificar a tu backend (Nest, Express, etc.)
    
- Lanzar procesos (tests, despliegue, etc.)
    
- Enviar WebSocket a la interfaz web
    
- Registrar el push en la base de datos
    

---

## ⚠️ Consideraciones

- Los hooks no se versionan con Git (no viven dentro del repo)
    
- Deben estar en el filesystem del repo `.git/hooks/`
    
- Asegurate de darles permisos de ejecución (`chmod +x`)
    
- Si usás contenedores (Docker), puede que tengas que montar los hooks
    

---

## 📌 Tip

Si se quiere que todos los nuevos repos tengan el hook automáticamente, podés usar una **plantilla de Git**:
```bash 
git init --bare --template=/path/a/mis-hooks nuevo-repo.git
```
