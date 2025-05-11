# ⚙️ Backend para gestión de repositorios Git

El backend se encarga de:
- Crear y eliminar repositorios bare
- Exponer rutas para clonar o hacer push
- Ejecutar comandos Git
- (Opcional) manejar autenticación, permisos, UI

---

## 🚀 Recomendación: usar NestJS

NestJS te permite estructurar el proyecto de forma modular y escalable.

---

## 📁 Estructura de carpetas (propuesta)

```
src/ 
	├── auth/ → autenticación (tokens, JWT, SSH, etc.) 
	├── users/ → usuarios y perfiles 
	├── repos/ → lógica para crear, eliminar, listar repos 
	├── git/ → lógica específica de Git (hooks, comandos) 
	├── app.module.ts 
	└── main.ts
```


---

## 🧱 Servicio base: `GitStorageService`

Maneja el sistema de archivos para crear y gestionar repos bare.

### 📦 Funciones típicas

```ts
createBareRepo(username: string, repoName: string): Promise<void>
deleteRepo(username: string, repoName: string): void
repoExists(username: string, repoName: string): boolean
getRepoPath(username: string, repoName: string): string
```

``` ts
import { Injectable } from '@nestjs/common';
import * as fs from 'fs';
import * as path from 'path';
import { exec } from 'child_process';

@Injectable()
export class GitStorageService {
  private basePath = '/data/repos';

  private getRepoPath(user: string, name: string) {
    return path.join(this.basePath, user, `${name}.git`);
  }

  async createBareRepo(user: string, name: string) {
    const dir = path.join(this.basePath, user);
    const repo = this.getRepoPath(user, name);
    if (!fs.existsSync(dir)) fs.mkdirSync(dir, { recursive: true });

    return new Promise<void>((resolve, reject) => {
      exec(`git init --bare "${repo}"`, (err) => {
        if (err) reject(err);
        else resolve();
      });
    });
  }
}
```

## Endpoint de API (controller)

``` ts 
@Post(':user/:repo') createRepo(@Param('user') user: string, @Param('repo') repo: string) {   return this.gitStorageService.createBareRepo(user, repo); }
```
## Notas

- Los comandos se ejecutan con `child_process.exec`, por lo que hay que validar entradas.
    
- Es mejor no exponer directamente las rutas locales del repo.
    
- Podés usar hooks (`post-receive`) para comunicarte con la app cuando alguien hace `push`.
    

---

## 🧩 Futuras mejoras

- Control de permisos por repo
    
- Hooks para integrar con eventos
    
- UI web para ver commits, branches, etc.
    
- WebSockets o eventos para notificaciones
    
- Integración con acceso SSH y/o Smart HTTP

