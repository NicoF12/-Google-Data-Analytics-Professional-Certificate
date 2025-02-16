# -Google-Data-Analytics-Professional-Certificate
Case 1: How does a Bike-Share Navigate Speedy Success

Proyecto para el certificado de Google Data Analytics. Caso de estudio: How does a bike-share navigate speedy success?

La tarea es: 
Cual es la diferencia en la utilización de las bicicletas entre los miembros anuales y los casuales?

Armar un reporte con las siguientes consideraciones:
1. Un resumen con la tarea
2. Una descripción de toda la data utilizada
3. Documentar la limpieza o manipulación de los datos
4. Resumen del análisis
5. Visualizaciones y descubrimientos
6. Tres recomendaciones en base al análisis realizado

Como un data analista Jr de una empresa de alquiler de bicicletas (pedal y eléctricas), tengo que diseñar una estrategia de marketing con la finalidad de lograr que los riders casuales sean miembros anuales.
Para lograr dicho objetivo, se analiza la información de la empresa para el período diciembre 2023 - noviembre 2024, que contiene las siguientes columnas:

"ride_id
rideable_type
started_at
ended_at
start_station_name
end_station_name
start_lat
start_lng
end_lat
end_lng
member_casual"

Para analizar la información disponible, se utilizaron las herramientas de Excel, MySQL y PowerBi. 

En excel se fue analizando la información disponible de forma mensual. Se crearon columnas de duración de los viajes, mes y día. Se utilizaron tablas dinámicas para observar el comportamiento de los miembros anuales y casuales, por día, según el tipo de bicicleta, y promediando los tiempos de viaje. Asimismo, se revisó en Excel la existencia de nulos o valores duplicados.

En SQL se unificaron los 12 archivos mensuales en una sola dataset:

```
CREATE TABLE IF NOT EXISTS `2024_tripdata.all_tripdata` AS 
(
  SELECT * FROM '202312_tripdata`
  UNION ALL
  SELECT * FROM `202401_tripdata`
  UNION ALL
  SELECT * FROM `202402_tripdata`
  UNION ALL
  SELECT * FROM `202403_tripdata`
  UNION ALL
  SELECT * FROM `202404_tripdata`
  UNION ALL
  SELECT * FROM `202405_tripdata`
  UNION ALL
  SELECT * FROM `202406_tripdata`
  UNION ALL
  SELECT * FROM `202407_tripdata`
  UNION ALL
  SELECT * FROM `202408_tripdata`
  UNION ALL
  SELECT * FROM `202409_tripdata`
  UNION ALL
  SELECT * FROM `202410_tripdata`
  UNION ALL
  SELECT * FROM `202411_tripdata`
);
```


Se realizaron consultas para ver los valores totales, si existen valores nulos o duplicados que afecten al análisis. Se modificó el tipo de valor de las columnas de started_at y ended_at para poder crear la columna "ride_length":

```
ALTER TABLE `2024_tripdata.all_tripdata`
MODIFY COLUMN started_at DATETIME(6);
```

```
ALTER TABLE `2024_tripdata.all_tripdata`
MODIFY COLUMN ended_at DATETIME(6);
```

```
UPDATE `2024_tripdata.all_tripdata` 
SET ride_length =
    CASE
        WHEN started_at <= ended_at THEN SEC_TO_TIME(TIMESTAMPDIFF(SECOND, started_at, ended_at))
        ELSE SEC_TO_TIME(ABS(TIMESTAMPDIFF(SECOND, started_at, ended_at)))
    END;
```

Una vez creada la columna y realizado la identificación de los valores nulos y duplicadoss, se creó una nueva tabla incluyendo una columna de día y otra de mes:

```
CREATE TABLE `2024_tripdata.all_tripdata_clean` AS 
(
  SELECT 
    ride_id, rideable_type, started_at, ended_at, 
    ride_length,
    CASE EXTRACT(DAY FROM started_at) 
      WHEN 1 THEN 'Sun'
      WHEN 2 THEN 'Mon'
      WHEN 3 THEN 'Tue'
      WHEN 4 THEN 'Wed'
      WHEN 5 THEN 'Thu'
      WHEN 6 THEN 'Fri'
      WHEN 7 THEN 'Sat'    
    END AS day_of_week,
    CASE EXTRACT(MONTH FROM started_at)
      WHEN 1 THEN 'Jan'
      WHEN 2 THEN 'Feb'
      WHEN 3 THEN 'Mar'
      WHEN 4 THEN 'Apr'
      WHEN 5 THEN 'May'
      WHEN 6 THEN 'Jun'
      WHEN 7 THEN 'Jul'
      WHEN 8 THEN 'Aug'
      WHEN 9 THEN 'Sep'
      WHEN 10 THEN 'Oct'
      WHEN 11 THEN 'Nov'
      WHEN 12 THEN 'Dec'
    END AS month,
    start_station_name, end_station_name, 
    start_lat, start_lng, end_lat, end_lng, member_casual
    FROM `2024_tripdata.all_tripdata`)
```

Por último, se utilizó PowerBi con la información para visualizar gráficamente el comportamiento de los riders casuales y suscriptos, según el tipo de bicicleta, el día y la cantidad de tiempo que duró el viaje.



Resultados:

