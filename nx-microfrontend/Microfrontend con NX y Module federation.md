
### 🧩 Microfrontends con Nx y Module Federation

#### 🛠️ ¿Qué es Module Federation?

- Funcionalidad de Webpack 5 que permite que una app consuma módulos de otra **en tiempo de ejecución**.
    
- Soporta **carga remota**, **compartir dependencias** y **microfrontends independientes**.
    
- Ideal para arquitecturas desacopladas.
  
  

### 📦 Nx + Module Federation

1. Crear workspace:
    `npx create-nx-workspace@latest my-org cd my-org`
2. Generar host y remotes
```bash
    nx g @nrwl/react:host shell nx g @nrwl/react:remote mfe1 --host=shell nx g @nrwl/react:remote mfe2 --host=shell
```
    
3. Correr todas las apps:
```bash
	nx run-many --target=serve --projects=shell,mfe1,mfe2 --parallel
```
    
4. Compartir módulos usando `ModuleFederationPlugin` y `React.lazy` para cargar componentes remotos.

### 🧪 Microfrontends con diferentes frameworks

**Caso:** Shell en React + MFE en Angular y Vue.

#### Estrategias posibles:

1. **Custom Elements (Web Components)**:
    
    - Cada MFE expone un `<my-widget></my-widget>`.
        
    - Framework-agnóstico.
        
    - Ideal para mantener independencia.
        
2. **Funciones `mount()`**:
    
    - Cada MFE exporta una función JS que recibe un container y monta la UI.
        
    - Más control, pero acopla más con el shell.
        
3. **Iframe**:
    
    - Aislado pero poco práctico para apps modernas.
      

### Comunicación entre MFEs

#### 1. **Custom Events**
```ts 
// Emitir
	window.dispatchEvent(new CustomEvent('userSelected', { detail: { id: 123 } }));
	
	// Escuchar
	window.addEventListener('userSelected', (e) => {
	  console.log(e.detail.id);
	});
```

* Sin dependencias, framework-agnóstico.

#### 2. **Event Bus compartido**
```ts 
// event-bus.js
	const listeners = {};
	export const on = (event, cb) => (listeners[event] ||= []).push(cb);
	export const emit = (event, data) => (listeners[event] || []).forEach(cb => cb(data));

```