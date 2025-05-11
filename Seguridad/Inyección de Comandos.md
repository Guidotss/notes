
La inyección de comandos ocurre cuando una aplicación pasa entradas no confiables a un intérprete del sistema operativo (ej., Bash, cmd.exe) sin validación o saneamiento adecuado. Esto permite que un atacante ejecute comandos arbitrarios en el sistema operativo subyacente.

---

## 🔍 ¿Cómo ocurre?

1. **Entrada no controlada:** El usuario proporciona datos que se usan directamente en un comando del sistema operativo.
    
2. **Falta de validación/saneamiento:** La aplicación no filtra o valida correctamente la entrada antes de enviarla al intérprete.
    
3. **Ejecución del comando:** El comando se ejecuta con los privilegios de la aplicación que lo procesa, pudiendo acceder o modificar archivos, realizar operaciones en el sistema o incluso comprometerlo por completo.
    

---

## 📌 Ejemplo Vulnerable (PHP)

```php
<?php
// Ejemplo típico: Usar `ping` para probar la conectividad con un host dado por el usuario.
$host = $_GET['host'];
$output = shell_exec("ping -c 4 " . $host);
echo "<pre>$output</pre>";
```

### 🔥 Ataque de Inyección de Comandos

```
http://example.com/ping.php?host=example.com;cat /etc/passwd
```

El código ejecutará:

```bash
ping -c 4 example.com; cat /etc/passwd
```

Esto devolverá el resultado del ping seguido del contenido del archivo `/etc/passwd`.

---

## ⚙️ Cómo Detectar la Vulnerabilidad

- Revisar el código para identificar funciones que interactúan con el sistema operativo, como:
    
    - `exec()`
        
    - `shell_exec()`
        
    - `system()`
        
    - `popen()`
        
    - `passthru()`
        
- Probar con cadenas maliciosas como:
    
    - `example.com; cat /etc/passwd`
        
    - `example.com && ls -la`
        
    - `example.com | whoami`
        

---

## 🛡️ Cómo Prevenir la Inyección de Comandos

1. No usar funciones del sistema operativo si no es absolutamente necesario.
    
2. Validar y sanear entradas adecuadamente.
    
3. Usar funciones seguras o APIs específicas en lugar de ejecutar comandos directamente.
    
4. Escapar o sanitizar entradas usando herramientas específicas.
    
5. Implementar listas blancas de entradas permitidas.
    
6. Establecer privilegios mínimos para la aplicación.
    
7. Usar librerías específicas que eviten la ejecución de comandos arbitrarios.
    

---

## 🔒 Ejemplo Seguro (PHP)

```php
<?php
$allowed_hosts = ['example.com', 'google.com']; // Lista blanca de hosts permitidos
$host = $_GET['host'];

if (!in_array($host, $allowed_hosts)) {
    die("Host no permitido.");
}

$output = shell_exec("ping -c 4 " . escapeshellarg($host));
echo "<pre>$output</pre>";
```

---

## 📢 Conclusión

La inyección de comandos es una vulnerabilidad crítica que puede llevar a la ejecución remota de código o al compromiso total del sistema. Evitarla requiere:

- Validación estricta de las entradas.
    
- Evitar llamadas directas a comandos del sistema.
    
- Uso de técnicas de sanitización adecuadas.
    

---

## 🔑 Simplificación del Comando

### Comando Original:

```python
${self.module.cache.util.os.popen('cat ../flag.txt > ./application/templates/index.html').read()}
```

### Versión Simplificada con `os.system()`:

```python
import os
os.system('cat ../flag.txt > ./application/templates/index.html')
```

### Versión Simplificada con `subprocess.run()`:

```python
import subprocess
subprocess.run(['cat', '../flag.txt', '>', './application/templates/index.html'], shell=True)
```

---

## 🚨 Relación con Server-Side Template Injection (SSTI)

La inyección de comandos también puede ocurrir cuando una aplicación web sufre de vulnerabilidad **Server-Side Template Injection (SSTI)**. Esto ocurre cuando una entrada del usuario se pasa sin filtrar a un motor de plantillas en el backend.

### 🔥 Ejemplo de SSTI en Python (Jinja2)

```python
from flask import Flask, request, render_template_string

app = Flask(__name__)

@app.route('/')
def index():
    name = request.args.get('name', 'Mundo')
    return render_template_string("Hola {{ name }}")
```

#### Ataque de SSTI:

```
http://example.com/?name={{ self.module.cache.util.os.popen('cat ../flag.txt').read() }}
```

Esto ejecutaría el comando y devolvería el contenido del archivo `flag.txt`.

### 🔑 Conexión con `popen()`

El uso de `popen()` en Python para manipular archivos o ejecutar comandos arbitrarios puede ser aprovechado mediante SSTI si la entrada del usuario se pasa sin filtrar. En general, debes:

- Validar todas las entradas.
    
- Usar listas blancas si es posible.
    
- Evitar pasar entradas del usuario directamente a métodos que ejecutan comandos del sistema.
    

¿Te gustaría que continúe con ejemplos de cómo detectar SSTI y mitigar sus riesgos? 🚀