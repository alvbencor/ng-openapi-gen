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

# Opciones del comando `ng-openapi-gen`

| Opción                         | Alias | Tipo       | Descripción breve |
|-------------------------------|-------|------------|-------------------|
| `--input`                     | `-i`  | `string`   | Ruta al archivo OpenAPI (`.yaml` o `.json`). **Obligatorio**. |
| `--output`                    | `-o`  | `string`   | Carpeta donde se generará el cliente Angular. |
| `--config`                    | `-c`  | `string`   | Ruta a un archivo de configuración `ng-openapi-gen.json`. |
| `--ignoreUnusedModels`        |       | `boolean`  | No genera modelos que no estén referenciados en operaciones. |
| `--removeStaleFiles`          |       | `boolean`  | Elimina archivos obsoletos del directorio de salida antes de generar. |
| `--serviceSuffix`             |       | `string`   | Sufijo para los nombres de los servicios generados (ej: `Api`). |
| `--modelSuffix`               |       | `string`   | Sufijo para los nombres de los modelos generados. |
| `--servicePrefix`             |       | `string`   | Prefijo para los nombres de los servicios. |
| `--modelPrefix`               |       | `string`   | Prefijo para los nombres de los modelos. |
| `--defaultTag`                |       | `string`   | Etiqueta predeterminada si una operación no tiene `tags`. |
| `--includeTags`               |       | `string[]` | Solo incluye operaciones con estas etiquetas. |
| `--excludeTags`               |       | `string[]` | Excluye operaciones con estas etiquetas. |
| `--templates`                 |       | `string`   | Ruta a una carpeta con plantillas personalizadas de generación. |
| `--baseService`               |       | `string`   | Nombre de un servicio base común a todos los servicios generados. |
| `--apiService`                |       | `string`   | Nombre del servicio base de llamadas HTTP (por defecto usa `HttpClient`). |
| `--requestBuilder`            |       | `string`   | Nombre de la clase para construir requests. |
| `--response`                  |       | `string`   | Nombre de la clase que representa una respuesta HTTP. |
| `--module`                    |       | `string`   | Nombre del módulo Angular donde se agrupan los servicios. |
| `--enumStyle`                 |       | `string`   | Cómo generar enums: `string` o `enum` (por defecto: `enum`). |
| `--enumArray`                 |       | `boolean`  | Si es `true`, los enums se generan como arrays. |
| `--customizedResponseType`   |       | `boolean`  | Permite personalizar los tipos de respuesta de cada operación. |
| `--keepFullResponseMediaType`|       | `boolean`  | Mantiene el tipo de contenido original en las respuestas. |
| `--camelizeModelNames`       |       | `boolean`  | Convierte los nombres de modelos a camelCase. |
| `--silent`                    |       | `boolean`  | Silencia la salida de consola durante la generación. |
| `--useTempDir`               |       | `boolean`  | Usa un directorio temporal durante la generación. |
| `--indexFile`                |       | `string`   | Ruta personalizada para el archivo `index.ts` generado. |
| `--skipJsonSuffix`           |       | `boolean`  | No añade `.json` a los tipos MIME al generar las rutas. |
| `--fetchTimeout`             |       | `number`   | Tiempo de espera para operaciones de red (en milisegundos). |

> Puedes usar `ng-openapi-gen --help` para ver esta lista en tu terminal.


