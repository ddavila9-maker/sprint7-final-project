# sprint7-final-project
# Análisis de datos - ConnectaTel

## Objetivo del proyecto

Analizar el comportamiento de uso de los clientes de ConnectaTel, una empresa de telecomunicaciones. La idea es limpiar los datos, explorar el comportamiento de los usuarios y segmentarlos por edad y nivel de uso, para sacar conclusiones que le sirvan al negocio a la hora de mejorar sus planes.

## Datasets

- plans.csv: información de los planes (Básico y Premium), con sus límites y tarifas
- users_latam.csv: datos de los usuarios (edad, ciudad, fecha de registro, plan, etc)
- usage.csv: historial de mensajes y llamadas de cada usuario

## Herramientas usadas

- Python
- pandas y numpy
- seaborn y matplotlib

## Etapas del análisis

### 1. Carga y exploración inicial
- Se cargaron los 3 datasets
- Se revisó el tamaño de cada uno con .shape
- Se usó .info() para ver tipos de datos y nulos
- Se usó .describe() para ver un resumen estadístico

### 2. Limpieza de datos
Se encontraron varios problemas:

- age tenía el valor -999 como marcador de dato faltante, se reemplazó por la mediana
- city tenía nulos y también el símbolo '?' en vez de nulo real, se dejó todo como NaN (en total casi 14% de los usuarios sin ciudad)
- reg_date tenía 40 fechas del año 2026, que no son posibles porque los datos llegan hasta 2024, se marcaron como NaT
- churn_date tiene 88% de nulos pero no es un error, un nulo ahí significa que el usuario sigue activo
- duration y length en usage tienen muchos nulos (55% y 45%) pero se comprobó que dependen de la columna type (duration solo aplica a llamadas, length solo a mensajes), así que se dejaron como están, no se imputaron

### 3. Agregación de uso por usuario
Se agrupó la tabla usage por user_id para sacar:
- cantidad total de mensajes
- cantidad total de llamadas
- total de minutos de llamada

Después se combinó esta tabla con users para tener todo en una sola tabla (user_profile).

### 4. Distribuciones y outliers
- Se graficaron histogramas de age, cant_mensajes, cant_llamadas y cant_minutos_llamada, separados por plan
- Se graficaron boxplots de las mismas variables
- Se calcularon los límites de outliers con el método IQR

Resultado:
- age no tiene outliers
- cant_mensajes tiene 46 outliers (límite superior en 11.5)
- cant_llamadas tiene 30 outliers (límite superior en 10.5)
- cant_minutos_llamada tiene 96 outliers (límite superior en 61.3)

No se eliminaron los outliers porque parecen usuarios reales de alto consumo, no errores en los datos.

### 5. Segmentación de clientes
Se crearon dos columnas nuevas:

grupo_uso (según cant_llamadas y cant_mensajes):
- Bajo uso: llamadas < 5 y mensajes < 5
- Uso medio: llamadas < 10 y mensajes < 10
- Alto uso: el resto

grupo_edad (según age):
- Joven: menos de 30 años
- Adulto: menos de 60 años
- Adulto Mayor: el resto

## Resultados de la segmentación

Por uso:
- Uso medio: ~72.5% de los usuarios
- Bajo uso: ~19%
- Alto uso: ~7%

Por edad:
- Adulto: ~50%
- Adulto Mayor: ~30%
- Joven: ~19%

## Conclusiones principales

- La edad no parece influir en qué plan tiene el usuario ni en cuánto lo usa, la proporción entre Básico y Premium se mantiene igual en todas las edades
- El nivel de uso sí marca diferencia: la mayoría de usuarios está en uso medio, y solo un grupo pequeño (7%) tiene uso alto
- Las variables de uso están sesgadas a la derecha, hay un grupo chico que consume mucho más que el resto
- El grupo de "Alto uso" es el más interesante desde el punto de vista comercial, porque son candidatos a pasarse a Premium

## Recomendaciones

- Contactar a los usuarios de alto uso que están en plan Básico y ofrecerles Premium
- Pensar en un plan intermedio, ya que la mayoría de usuarios está en uso medio y hoy solo hay dos opciones
- No eliminar a los usuarios outliers, tratarlos como un segmento de alto valor
- Enfocar las estrategias comerciales en el nivel de uso y no en la edad
- Mejorar cómo se captura la ciudad del usuario al registrarse, para no perder ese dato

## Cómo ejecutar el notebook

1. Abrir el archivo .ipynb en Google Colab (se puede subir directo desde GitHub con "Abrir en Colab", o desde tu computador con Archivo > Subir notebook)
2. Subir los 3 archivos csv (plans.csv, users_latam.csv, usage.csv) a la carpeta /datasets/ dentro del entorno de Colab, o ajustar la ruta según donde los tengas
3. Correr las celdas en orden, de arriba hacia abajo, porque cada paso depende del anterior (por ejemplo, la limpieza depende de la carga, y la segmentación depende de la tabla ya agregada)

## Guía de reproducción

- Si quieres correrlo de nuevo desde cero, basta con reiniciar el entorno de ejecución (Entorno de ejecución > Reiniciar y ejecutar todo)
- No hace falta instalar nada aparte, Colab ya trae pandas, numpy, seaborn y matplotlib instalados
- Si corres el notebook localmente (no en Colab), instalar las librerías con: pip install pandas numpy seaborn matplotlib
- Los datasets deben estar en la misma ruta que se usa en el notebook (por defecto /datasets/), si cambian de ubicación hay que actualizar las rutas en las celdas de carga
