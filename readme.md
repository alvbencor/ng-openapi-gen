# angular-petstore-generator

Este README muestra desde cero cómo crear un proyecto Angular llamado **angular-petstore-generator**, agregar tu spec OpenAPI de Petstore y generar un cliente tipado con **ng-openapi-gen**.

---

## 1. Preparar carpeta de trabajo

Abre PowerShell y sitúate en tu escritorio o en la carpeta que prefieras:

```powershell
cd C:\Users\Alvaro\Desktop\angular
```

Si la carpeta no existe, créala:

```powershell
mkdir angular && cd angular
```

---

## 2. Crear nuevo proyecto Angular

Instala Angular CLI (si no lo tienes):

```powershell
npm install -g @angular/cli
```

Crea el proyecto:

```powershell
ng new angular-petstore-generator
cd angular-petstore-generator
```

- Elige **CSS** o tu preprocesador favorito.
- Activa *routing* si quieres, aunque no es estrictamente necesario.

---

## 3. Añadir tu especificación OpenAPI

Dentro del proyecto, crea la carpeta `openapi/` y copia el contrato Petstore (`petstore.yaml` o `openapi.yaml`):

```
angular-petstore-generator
└── openapi
    └── petstore.yaml
```

Ejemplo mínimo de `petstore.yaml`:

```yaml
openapi: 3.0.0
info:
  title: Petstore
  version: 1.0.0
servers:
  - url: https://petstore.swagger.io/v1
paths:
  /pets:
    get:
      operationId: listPets
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Pet'
components:
  schemas:
    Pet:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
``` 

---

## 4. Instalar ng-openapi-gen

Añade la herramienta como dependencia de desarrollo:

```bash
npm install --save-dev ng-openapi-gen
```

---

## 5. Generar el cliente Angular

Puedes hacerlo **sin** crear fichero de config, todo via CLI. Ejecuta:

```bash
npx ng-openapi-gen --input openapi/petstore.yaml \
  --output src/app/api \
  --serviceSuffix ApiService \
  --ignoreUnusedModels true \
  --indexFile true \
  --modelIndex models \
  --serviceIndex services \
  --module ApiModule
```

> En PowerShell escribe todo en **una sola línea**, cambiando `\` por un espacio:
>
> ```powershell
> npx ng-openapi-gen --input openapi/petstore.yaml --output src/app/api --serviceSuffix ApiService --ignoreUnusedModels true --indexFile true --modelIndex models --serviceIndex services --module ApiModule
> ```

Esto creará en `src/app/api`:

- `api.module.ts` (modulo con todos los servicios)
- `api-configuration.ts`
- `models.ts` (o carpeta `models/`)
- `services.ts` (o carpeta `services/`)
- `index.ts` (barrel)

---

## 6. Importar y usar el cliente en tu App

### 6.1. Con componentes stand-alone (Angular 15+)

Edita `src/main.ts`:

```ts
import { bootstrapApplication } from '@angular/platform-browser';
import { importProvidersFrom } from '@angular/core';
import { provideRouter } from '@angular/router';

import { App } from './app/app';
import { routes } from './app/app.routes';
import { ApiModule } from './app/api/api.module';

bootstrapApplication(App, {
  providers: [
    importProvidersFrom(ApiModule.forRoot({ rootUrl: 'https://petstore.swagger.io/v1' })),
    provideRouter(routes)
  ]
});
```

### 6.2. Con módulo tradicional

Si prefieres un **AppModule**, en `src/app/app.module.ts`:

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http';
import { ApiModule } from './api/api.module';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, HttpClientModule, ApiModule.forRoot({ rootUrl: 'https://petstore.swagger.io/v1' })],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

---

## 7. Probar tu aplicación

Arranca el servidor de desarrollo:

```bash
ng serve --open
```

Visita `http://localhost:4200` y comprueba que puedes inyectar servicios siguiendo estos pasos:

1. **Genera un componente** para listar mascotas:
   ```bash
   ng generate component pet-list --standalone
   ```

2. **Inyecta el servicio** en el componente (`src/app/pet-list.component.ts`):
   ```ts
   import { Component, OnInit } from '@angular/core';
   import { PetApiService, Pet } from './api';

   @Component({
     selector: 'app-pet-list',
     standalone: true,
     template: `
       <h2>Lista de Mascotas</h2>
       <ul *ngIf="pets.length">
         <li *ngFor="let pet of pets">{{ pet.name }} (ID: {{ pet.id }})</li>
       </ul>
       <p *ngIf="!pets.length">No hay mascotas.</p>
     `
   })
   export class PetListComponent implements OnInit {
     pets: Pet[] = [];

     constructor(private petSvc: PetApiService) {}

     ngOnInit() {
       this.petSvc.listPets().subscribe(data => this.pets = data);
     }
   }
   ```

3. **Registra providers** en `src/main.ts`, añadiendo el componente y el módulo API:
   ```ts
   import { bootstrapApplication } from '@angular/platform-browser';
   import { importProvidersFrom } from '@angular/core';
   import { PetListComponent } from './app/pet-list.component';
   import { ApiModule } from './app/api/api.module';

   bootstrapApplication(PetListComponent, {
     providers: [
       importProvidersFrom(
         ApiModule.forRoot({ rootUrl: 'https://petstore.swagger.io/v1' })
       )
     ]
   });
   ```

4. **Abre en el navegador**:
   ```
   http://localhost:4200
   ```

   Deberías ver la lista de mascotas cargada desde la API.
```ts
constructor(private petSvc: PetApiService) {}

ngOnInit() {
  this.petSvc.listPets().subscribe(pets => console.log(pets));
}
```

---

## 8. ¿Dónde ver la spec OpenAPI?

### 8.1. En tu editor de código

Simplemente abre el fichero `openapi/petstore.yaml` (o como lo hayas llamado) dentro de tu IDE (VS Code, WebStorm, etc.) para consultar el título, la versión, listas de endpoints, esquemas y toda la documentación embebida.

### 8.2. Servir la spec como recurso estático

Angular sirve por defecto la carpeta `public/`. Si los cambios en `angular.json` no surten efecto en modo desarrollo, puedes copiar tu spec directamente en `public/` para probar rápidamente:

```bash
cp openapi/petstore.yaml public/
```

Luego accede en el navegador a:

```
http://localhost:4200/petstore.yaml
```

Si prefieres usar `src/assets/`, asegúrate de:

1. Añadir **después** de `public` la ruta `src/assets` en tu `angular.json`:
   ```jsonc
   "assets": [
     { "glob": "**/*", "input": "public", "output": "/" },
     { "glob": "**/*", "input": "src/assets", "output": "/assets" }
   ]
   ```
2. Copiar el spec con:
   ```bash
   cp openapi/petstore.yaml src/assets/
   ```
3. **Reiniciar** el servidor de desarrollo (`Ctrl+C` y `ng serve`) para que Angular recoja la nueva configuración.
4. Acceder a:
   ```
   http://localhost:4200/assets/petstore.yaml
   ```

Con estas opciones podrás verificar rápidamente tu spec OpenAPI en el navegador.

### 8.3. Usar Swagger UI integrado. Usar Swagger UI integrado. Usar Swagger UI integrado. Usar Swagger UI integrado. Usar Swagger UI integrado. Usar Swagger UI integrado

Para una experiencia más visual, instala Swagger UI:

```bash
npm install --save swagger-ui-dist
```

Y luego añade en tu `index.html`:

```html
<link rel="stylesheet" href="/swagger-ui.css">
<div id="swagger-ui"></div>
<script src="/swagger-ui-bundle.js"></script>
<script>
  SwaggerUIBundle({
    url: 'assets/petstore.yaml',
    dom_id: '#swagger-ui'
  });
</script>
```

Abre `http://localhost:4200` y verás la UI con la documentación interactiva de tu API.

¡Con esto podrás consultar de forma cómoda toda la información de tu contract OpenAPI!"
