# 🔐 Acceso a repos Git por SSH

## 🧠 ¿Qué es?

Permite a los usuarios hacer `git clone`, `push`, `pull`, etc., usando el protocolo SSH.

---

## ✅ Ventajas

- Seguro (autenticación por clave pública)
- Fácil de implementar
- Compatible con clientes Git estándar
- No requiere servidor web ni protocolo HTTP

---

## 📁 Estructura recomendada

```bash
/home/git/ 
├── .ssh/ 
│ └── authorized_keys /data/repos/ 
├── guido/ 
│ └── mi-repo.git/
```

---

## 🔑 Claves SSH

Cada usuario genera una clave SSH:
```bash
ssh-keygen -t ed25519 -C "nombre@ejemplo.com"
```

Luego te pasa su clave pública (`id_ed25519.pub`), y vos la agregás a:

```bash 
/home/git/.ssh/authorized_keys
```

## ⚙️ git-shell

Para evitar que el usuario pueda ejecutar comandos arbitrarios al conectar por SSH, le asignás el shell restringido:
```bash
usermod -s $(which git-shell) git
```

```bash 
git:x:1001:1001:git:/home/git:/usr/bin/git-shell
```

Esto limita al usuario a comandos Git válidos (`clone`, `push`, etc.)

## 🧪 Ejemplo de uso (desde el cliente)

Se Puede usar el hook `post-receive` para lanzar acciones después de un push por SSH.