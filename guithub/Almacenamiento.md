# 💾 Almacenamiento de repositorios Git

## 📍 ¿Dónde se guardan los repos?

Los repositorios se almacenan localmente en el sistema de archivos del servidor, como carpetas `.git` de tipo **bare**.

---

## 📁 Estructura recomendada

Se organiza por usuario, y cada repo es una carpeta bare:

```
/data
 /repos/ 
	├── guido/ │
	├── primer-repo.git/ 
	│ └── otro-repo.git/ 
	├── nico/ │ 
	└── nico-repo.git/
```


Cada repo se crea con:
```bash
git init --bare /data/repos/usuario/nombre-repo.git
```

## ✅ Ventajas de usar almacenamiento local

- Simplicidad
    
- Rápido acceso a los archivos
    
- Compatible directamente con `git push`, `git clone`
    
- Permite usar hooks (`post-receive`, etc.)
    
- Fácil de hacer backup con scripts
    

---

## 🛠 Consideraciones técnicas

- Asegurate que el servidor tenga permisos de lectura/escritura sobre `/data/repos`
    
- Los nombres de las carpetas terminan en `.git`
    
- Podés verificar si un repo existe con:

```bash
	test -d /data/repos/nico/mi-repo.git
```

## 🔄 Alternativas (más complejas)

| Método               | Ventajas                              | Desventajas                            |
| -------------------- | ------------------------------------- | -------------------------------------- |
| Almacenamiento local | ✅ Simple, directo, compatible con Git | ❌ Escalabilidad limitada               |
| Blob storage (S3)    | ✅ Escalable, ideal para la nube       | ❌ Requiere capa intermedia para Git    |
| Base de datos        | ✅ Consultable, centralizado           | ❌ Muy complejo, no es natural para Git |

## Recomendación

Para empezar, usá almacenamiento local con estructura clara:

`/data/repos/usuario/repo.git`

Más adelante podés montar un sistema de backups o sincronización si lo necesitás.

---

## 🧱 Siguiente paso

Usar un servicio en tu backend para manejar esta estructura, como `GitStorageService`, que:

- Crea repos bare
    
- Elimina repos
    
- Verifica existencia
    
- Retorna rutas absolutas

