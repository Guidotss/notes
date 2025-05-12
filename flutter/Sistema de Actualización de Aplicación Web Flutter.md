

## Contexto
Cuando se hace un nuevo deploy de la aplicación web, necesitamos notificar al usuario y forzar la actualización de la aplicación. Esto se logra mediante un sistema que combina el service worker de Flutter y una modal nativa de HTML.

## Componentes Principales

### 1. Dockerfile - Generación del Cache Name
```bash
RUN HASH=$(sha1sum build/web/main.dart.js | cut -d ' ' -f 1) && \
    TIMESTAMP=$(date +%s) && \
    sed -i "s/const CACHE_NAME = .*/const CACHE_NAME = 'flutter-app-cache-$HASH-$TIMESTAMP';/" build/web/flutter_service_worker.js
```
Este comando:
- Genera un hash SHA1 del archivo main.dart.js
- Obtiene el timestamp actual
- Modifica el CACHE_NAME en el service worker con el nuevo hash y timestamp

### 2. Modal de Actualización (index.html)
```html
<div id="updateModal" class="update-modal">
  <div class="modal-content">
    <h2 class="modal-title">Nueva versión disponible</h2>
    <p class="modal-message">Hay una nueva versión de la aplicación disponible. Por favor, recarga la página para actualizar.</p>
    <div class="modal-buttons">
      <button class="btn btn-secondary" onclick="closeModal()">Más tarde</button>
      <button class="btn btn-primary" onclick="reloadPage()">Actualizar ahora</button>
    </div>
  </div>
</div>
```

### 3. Lógica de Verificación
```javascript
async function checkForNewVersion() {
  try {
    const response = await fetch('flutter_service_worker.js');
    const text = await response.text();
    const cacheNameMatch = text.match(/const CACHE_NAME = '([^']+)'/);
    
    if (cacheNameMatch) {
      const currentVersion = cacheNameMatch[1];
      const lastVersion = localStorage.getItem('last_app_version');
      
      if (lastVersion && lastVersion !== currentVersion) {
        showUpdateModal();
      }
      
      localStorage.setItem('last_app_version', currentVersion);
    }
  } catch (error) {
    console.error('Error checking for new version:', error);
  }
}
```

## Flujo de Funcionamiento

1. **Build y Deploy**:
   - Al hacer build, se genera un nuevo hash del main.dart.js
   - Se actualiza el CACHE_NAME en el service worker

2. **Detección de Cambios**:
   - La aplicación verifica el CACHE_NAME actual
   - Compara con la última versión guardada en localStorage
   - Si hay diferencia, muestra la modal

3. **Actualización**:
   - Usuario puede elegir "Actualizar ahora" o "Más tarde"
   - Al actualizar, se recarga la página forzando una carga limpia

## Notas Importantes

- El sistema usa localStorage para persistir la última versión conocida
- La verificación se hace 2 segundos después de cargar la página
- La modal no se puede cerrar sin tomar una acción
- El CACHE_NAME incluye tanto el hash del código como un timestamp para garantizar unicidad
