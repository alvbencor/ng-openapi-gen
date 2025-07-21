# Proyecto Angular con ng-openapi-gen

Este documento explica cómo crear un proyecto Angular y generar clientes HTTP tipados a partir de una especificación OpenAPI usando la biblioteca **ng-openapi-gen**.

---

## 1. Requisitos previos

* **Node.js** v14 o superior
* **npm** (incluido con Node.js) o **yarn**
* **Angular CLI** instalado globalmente:

  ```bash
  npm install -g @angular/cli

  ```


````

---

## 2. Crear un nuevo proyecto Angular

```bash
ng new mi-proyecto
cd mi-proyecto
````

Durante la creación:

* Elige tu prefijo de estilo (CSS, SCSS, etc.).
* Activa o desactiva routing según necesites.

---

## 3. Instalar la biblioteca ng-openapi-gen

Añade **ng-openapi-gen** como dependencia de desarrollo:

```bash
npm install --save-dev ng-openapi-gen

```

---

## 4. Configuración mediante archivo JSON (elidible si se usan comandos cli)

Crea un fichero `ng-openapi-gen.json` en la raíz de tu proyecto con la configuración deseada:

```json
{
  "input": "src/assets/openapi.json",     // Ruta a tu spec OpenAPI (JSON o YAML)
  "output": "src/app/api",               // Carpeta donde generar código
  "ignoreUnusedModels": true,              // Omitir modelos no referenciados
  "serviceSuffix": "ApiService",         // Sufijo para clases de servicio
  "modelSuffix": "",                     // Sufijo para interfaces de modelo
  "useUnionTypes": false                   // Desactivar tipos unión en modelos
}
```

* Guarda tu especificación OpenAPI en `src/assets/openapi.json` (o `.yaml`).

---

## 5. Generar el cliente

En tu `package.json`, añade un script para regenerar tu cliente:

```json
"scripts": {
  "openapi:generate": "ng-openapi-gen --config ng-openapi-gen.json"
}
```

Ejecuta:

```bash
npm run openapi:generate

```

Esto generará en `src/app/api`:

* Interfaces de modelo basadas en los esquemas OpenAPI.
* Servicios `@Injectable` para cada tag/endpoint.

---

## 6. Opciones CLI disponibles
                                           
| Comando                                          | Descripción                                                                                         |
|--------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| `-i, --input <ruta>`                             | Ruta al fichero o URL de la especificación OpenAPI (JSON o YAML).                                    |
| `-o, --output <directorio>`                      | Carpeta de salida donde se generarán los archivos (por defecto `src/app/api`).                       |
| `-c, --config <archivo>`                         | Ruta al fichero de configuración JSON (por defecto `ng-openapi-gen.json`).                           |
| `-h, --help`                                     | Muestra la ayuda con todas las opciones disponibles.                                                |
| `--fetchTimeout <ms>`                            | Tiempo máximo (ms) para descargar la spec desde una URL (por defecto `20000`).                      |
| `--defaultTag <etiqueta>`                        | Etiqueta por defecto para operaciones sin tags (por defecto `Api`).                                 |
| `--includeTags <tag1,tag2,…>`                    | Solo genera servicios para los tags indicados (coma-separados).                                      |
| `--excludeTags <tag1,tag2,…>`                    | Excluye servicios para los tags indicados (coma-separados).                                          |
| `--ignoreUnusedModels`                           | Omite los modelos que no referencie ninguna operación (true por defecto).                            |
| `--removeStaleFiles`                             | Elimina archivos en la carpeta de salida que ya no se generan (true por defecto).                    |
| `--modelIndex <nombre|false>`                    | Fichero (sin `.ts`) que exporta todos los modelos. Pon `false` para omitir (por defecto `models`).   |
| `--services <true|false>`                        | Genera (`true`) o no (`false`) los servicios `@Injectable`, módulo e índice de servicios.          |
| `--serviceIndex <nombre|false>`                  | Fichero (sin `.ts`) que exporta todos los servicios. Pon `false` para omitir (por defecto `services`). |
| `--servicePrefix <prefijo>`                      | Prefijo para las clases de servicio (vacío por defecto).                                             |
| `--serviceSuffix <sufijo>`                       | Sufijo para las clases de servicio (por defecto `Service`).                                          |
| `--modelPrefix <prefijo>`                        | Prefijo para las interfaces de modelo (vacío por defecto).                                           |
| `--modelSuffix <sufijo>`                         | Sufijo para las interfaces de modelo (vacío por defecto).                                           |
| `--configuration <clase>`                        | Nombre de la clase de configuración generada (por defecto `ApiConfiguration`).                      |
| `--baseService <clase>`                          | Nombre de la clase base de servicio (por defecto `BaseService`).                                     |
| `--apiService <clase>`                           | Nombre del servicio que invoca funciones directamente (vacío por defecto).                           |
| `--requestBuilder <clase>`                       | Nombre de la clase `RequestBuilder` (por defecto `RequestBuilder`).                                  |
| `--response <clase>`                             | Nombre de la clase de respuesta (por defecto `StrictHttpResponse`).                                  |
| `--module <clase|false>`                         | Nombre del `NgModule` que exporta los servicios. Pon `false` para omitir (por defecto `ApiModule`).  |
| `--enumStyle <alias|upper|pascal|ignorecase>`    | Estilo de enums raíz (por defecto `pascal`).                                                         |
| `--enumArray`                                    | Exporta un array con los valores de cada enum en un fichero hermano.                                 |
| `--endOfLineStyle <crlf|lf|cr|auto>`              | Normaliza finales de línea: CRLF, LF, CR o auto (por defecto `auto`).                               |
| `--templates <ruta>`                             | Directorio con plantillas Handlebars personalizadas.                                                |
| `--excludeParameters <p1,p2,…>`                  | Omite parámetros con esos nombres en los servicios generados (coma-separados).                      |
| `--indexFile`                                    | Genera un `index.ts` que exporta todos los ficheros generados.                                       |
| `--skipJsonSuffix`                               | No genera sufijos `$Json` en los métodos de los servicios.                                          |
| `--customizedResponseType <JSON>`                | Especifica tipos de respuesta personalizados por ruta (objeto JSON).                                 |
| `--useTempDir`                                   | Usa un directorio temporal en lugar del de salida para archivos intermedios.                         |
| `--silent`                                       | Modo silencioso: sin salida verbose (por defecto `false`).                                           |
| `--camelizeModelNames`                           | Cameliza nombres de modelo (true por defecto).                                                      |
| `--keepFullResponseMediaType <boolean|JSON[]>`   | Conserva variantes completas de media types o define reglas de abreviación.                          |


> Para ver la lista completa de flags y sus detalles, ejecuta:
>
> ```bash
> npx ng-openapi-gen --help
> ```

---


