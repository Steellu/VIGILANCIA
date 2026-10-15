# Control de Acceso con QR — Guía de instalación

## Cómo funciona

1. Cada empleado tiene un QR con sus datos (código, DNI, nombre, área) y una **firma** que el servidor puede verificar.
2. La tableta de vigilancia abre `index.html` (alojado con HTTPS), escanea el QR y lo envía a Google Apps Script.
3. Apps Script valida la firma, busca al empleado en la hoja **Empleados**, decide si es **ENTRADA** o **SALIDA** y guarda todo en la hoja **Registros**.

> Los datos que se muestran en pantalla vienen siempre de la hoja, no del QR. Así, si alguien cambia de área o pasa a INACTIVO, se refleja de inmediato.

## Estructura de la hoja “Empleados” (fila 1 = encabezados)

| A | B | C | D | E | F |
|---|---|---|---|---|---|
| codigo | dni | nombres | apellidos | area | estado (ACTIVO / INACTIVO) |

La hoja **Registros** se crea sola en el primer escaneo.

## Paso 1 — Backend (Apps Script)

1. Abre tu Google Sheet → **Extensiones → Apps Script**.
2. Reemplaza todo el contenido de `Codigo.gs` por el archivo entregado. Puedes borrar `Index.html` del proyecto (ya no se usa).
3. **Cambia las tres claves** al inicio del archivo: `CLAVE_VIGILANCIA`, `CLAVE_ADMIN` y `SECRETO_QR`.
4. En **Configuración del proyecto (⚙️)** pon la zona horaria en `(GMT-05:00) America/Lima`.
5. **Implementar → Nueva implementación → Aplicación web**
   - Ejecutar como: **Yo**
   - Quién tiene acceso: **Cualquier persona**
6. Autoriza los permisos y copia la URL que termina en `/exec`.

> Cada vez que cambies `Codigo.gs` debes crear una **nueva versión** de la implementación (Implementar → Administrar implementaciones → editar → Nueva versión).

## Paso 2 — Frontend (hosting con HTTPS)

1. En `index.html` y en `generador.html` reemplaza `PEGA_AQUI_LA_URL...` por tu URL `/exec`.
2. Sube ambos archivos a un hosting estático con HTTPS. La opción más rápida: **https://app.netlify.com/drop** (arrastras la carpeta y te da una URL). También sirven GitHub Pages o Firebase Hosting.

La cámara solo funciona en páginas `https://` y fuera de Apps Script, por eso este paso es obligatorio.

## Paso 3 — Generar e imprimir los QR

1. Abre `generador.html` desde tu hosting → **Cargar empleados** (pide la `CLAVE_ADMIN`).
2. Filtra por área si quieres y pulsa **Imprimir / PDF**.

## Paso 4 — Tableta de vigilancia

1. Abre la URL de `index.html` en Chrome → **Iniciar cámara** → permitir cámara.
2. La primera vez pedirá la `CLAVE_VIGILANCIA` y la recuerda en esa tableta.
3. Menú de Chrome → **Añadir a pantalla de inicio** para abrirla como app.

## Seguridad

- Un QR solo es válido si trae la firma generada con tu `SECRETO_QR`. Los QR antiguos (solo con el código) **ya no sirven**; hay que regenerarlos.
- Si cambias `SECRETO_QR`, todos los QR existentes quedan invalidados.
- Las claves de vigilancia y administrador no están en el código del frontend: se piden una vez y se guardan en el navegador del dispositivo.
