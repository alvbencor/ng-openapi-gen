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

| Comando                       | Descripción                                             |                                                          
| ----------------------------- | ------------------------------------------------------- | 
| `--input <ruta>`              | Ruta al fichero o URL de la especificación OpenAPI.     |                                                          
| `--output <directorio>`       | Carpeta de salida donde se generarán los archivos.      |                                                         
| `--config <archivo>`          | Ruta al fichero de configuración JSON.                  |                                                          
| `--ignoreUnusedModels`        | Omite los modelos no referenciados por ningún endpoint. |                                                          
| `--serviceSuffix <sufijo>`    | Sufijo para las clases de servicio generadas.           |                                                          
| `--modelSuffix <sufijo>`      | Sufijo para las interfaces de modelo generadas.         |                                                          
| `--useUnionTypes <true/false>`| Habilita o deshabilita el uso de tipos unión en modelos.|  
| `--removeStaleFiles`          | Elimina archivos obsoletos en la carpeta de salida.     |                                                          
| `--defaultTag <etiqueta>`     | Tag por defecto para operaciones sin tag especificado.  |                                                          
| `--includeTags <tag1,tag2,…>` | Solo genera servicios para los tags indicados.          |                                                          
| `--excludeTags <tag1,tag2,…>` | Excluye servicios para los tags indicados.              |                                                          
| `--serviceIndex<nombre/false>`| Nombre del archivo índice de servicios.                 |          
| `--modelIndex<nombre/false>`  | Nombre del archivo índice de modelos.                   |                 
| `--templates <ruta>`          | Carpeta con plantillas Handlebars personalizadas.       |                                                          
| `--silent`                    | Modo silencioso, sin salida verbose.                    |                                                          

> Para ver la lista completa de flags y sus detalles, ejecuta:
>
> ```bash
> npx ng-openapi-gen --help
> ```

---


