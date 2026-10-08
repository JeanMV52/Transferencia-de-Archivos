# Taller 1 – Integración mediante transferencia de archivos con Apache Camel

**Integrantes:** Mathias Vera, Jean Luc Morales, Elizabeth Guerrón, Wilson Egas
**Asignatura:** Integración de Sistemas
**Fecha:** 07-10-2026

## Descripción

Caso "TiendaSol": el sistema de ventas exporta sus transacciones diarias en un archivo CSV y el sistema de inventario debe recibirlas. Hasta ahora, los empleados copiaban el archivo a mano. Este proyecto automatiza el proceso con Apache Camel: lee `ventas.csv` desde una carpeta de entrada, transforma su contenido a mayúsculas, lo deja en una carpeta de salida y guarda una copia en una carpeta de archivo. Aplica el patrón de integración por transferencia de archivos (*File Transfer*).

## Requisitos

- Java 25 (Camel 4.22.1 requiere Java 17 o superior)
- Maven 3.10.0
- Apache Camel 4.22.1

## Estructura del proyecto

```
├── pom.xml
├── input/        Archivos que deja el sistema de ventas (ventas.csv)
├── output/       Archivos transformados que recibe el sistema de inventario
├── archived/     Copia de los archivos ya procesados
├── logs/         Carpeta para registros
└── src/main/java/FileTransferRoute.java
```

## Cómo ejecutarlo

1. Descargar o clonar este repositorio.
2. Abrir una terminal en la carpeta raíz del proyecto (donde está `pom.xml`).
3. Ejecutar:

```
mvn compile exec:java
```

4. En la consola deben aparecer los logs `Procesando archivo: ventas.csv` y `Archivando archivo: ventas.csv el <fecha y hora>`. En `output` y `archived` aparece `ventas.csv` con el contenido en mayúsculas.
5. Camel queda corriendo vigilando las carpetas. Detener con `Ctrl+C`.

Las rutas de las carpetas son relativas, por lo que el programa debe ejecutarse siempre desde la raíz del proyecto.

## Rutas implementadas

| Ruta | Qué hace |
|------|----------|
| `route1` | Lee de `input` (con `noop=true`, sin borrar el original), deja pasar solo archivos `.csv`, registra el nombre, convierte el contenido a texto, lo pasa a mayúsculas y lo escribe en `output`. |
| `route2` | Lee de `output` (con `noop=true`), registra el nombre del archivo con la fecha y hora del procesamiento y lo copia a `archived`. |

## Pruebas realizadas

- **Copia simple:** `ventas.csv` de `input` se copia a `output`.
- **Transformación:** el contenido de `output\ventas.csv` quedó en mayúsculas (`ID,PRODUCTO,CANTIDAD,PRECIO`).
- **Archivo:** `archived\ventas.csv` contiene la versión ya transformada, con log de fecha y hora.
- **Filtro:** se colocó un `prueba.txt` en `input`. No fue procesado ni apareció en `output` ni `archived`, porque el filtro solo deja pasar archivos que terminan en `.csv`.
