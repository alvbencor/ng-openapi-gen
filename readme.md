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

Puedes hacerlo via CLI. Ejecuta:

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


