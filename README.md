# Taller Bases de datos 
>[!NOTE]
>En las carpetas de este repocitoro se encuentra los soportes de los talleres
>En este README mostrare los comandos que utilice para hacer las consultas de los casos de usos del taller BdLogistica

#

---Obtener Información Completa de Envíos---

SELECT
E.id id_envio,
E.fecha_envio,
E.estado_actual,
C.nombre nombre_cliente,
P.numero_seguimiento,
D.nombre nombre_conductor,
V.placa placa_vehiculo,
S.nombre sucursal_origen,
SD.nombre sucursal_destino
FROM
Envio E
JOIN
Cliente C ON E.id_cliente = C.CC
JOIN
Paquete P ON E.id_paquete = P.id
JOIN
Ruta R ON E.id_ruta = R.id
JOIN
Conductor D ON R.id_conductor = D.id
JOIN
Vehiculo V ON D.id_vehiculo = V.id
JOIN
Sucursal S ON R.id_sucursal_origen = S.id
JOIN
Sucursal SD ON R.id_sucursal_destino = SD.id;

+----------+-------------+---------------+----------------+--------------------+------------------+----------------+----------------------+----------------------+
| id_envio | fecha_envio | estado_actual | nombre_cliente | numero_seguimiento | nombre_conductor | placa_vehiculo | sucursal_origen      | sucursal_destino     |
+----------+-------------+---------------+----------------+--------------------+------------------+----------------+----------------------+----------------------+
|       10 | 2024-10-01  | entregado     | Carlos Pérez   | 100001             | Juan López       | ABC123         | Sucursal Medellín    | Sucursal Bogotá      |
|       11 | 2024-10-02  | recibido      | Mariana Torres | 100002             | Laura García     | DEF456         | Sucursal Bogotá      | Sucursal Los Ángeles |
|       12 | 2024-10-03  | entregado     | Carlos Pérez   | 100003             | Miguel Díaz      | GHI789         | Sucursal Los Ángeles | Sucursal Medellín    |
+----------+-------------+---------------+----------------+--------------------+------------------+----------------+----------------------+----------------------+
#
---Reporte de Envíos por Cliente---

SELECT
C.CC,
C.nombre,
E.id,
E.fecha_envio,
E.estado_actual,
P.numero_seguimiento,
P.tipo_servicio
FROM
Cliente C
JOIN
Envio E ON C.CC = E.id_cliente
JOIN
Paquete P ON E.id_paquete = P.id
ORDER BY
C.CC, E.fecha_envio;

+----+----------------+----+-------------+---------------+--------------------+---------------+
| CC | nombre         | id | fecha_envio | estado_actual | numero_seguimiento | tipo_servicio |
+----+----------------+----+-------------+---------------+--------------------+---------------+
|  1 | Carlos Pérez   | 10 | 2024-10-01  | en_transito   | 100001             | nacional      |
|  1 | Carlos Pérez   | 12 | 2024-10-03  | entregado     | 100003             | express       |
|  2 | Mariana Torres | 11 | 2024-10-02  | recibido      | 100002             | internacional |
+----+----------------+----+-------------+---------------+--------------------+---------------+
-----Actualizar el Estado de un Paquete---

UPDATE Envio
SET estado_actual = 'entregado'
WHERE id_paquete = 1;

Query OK, 1 row affected (0.01 sec)
Rows matched: 1 Changed: 1 Warnings: 0
#
---Rastrear la Ubicación Actual de un Paquete---

SELECT
P.numero_seguimiento,
S.ubicacion_actual,
S.estado,
S.fecha_hora
FROM
Paquete P
JOIN
Envio E ON P.id = E.id_paquete
JOIN
Seguimiento S ON E.id = S.id_envio
WHERE
P.numero_seguimiento = '001234'
ORDER BY
S.fecha_hora DESC
LIMIT 1;

+--------------------+---------------------------------+-------------+---------------------+
| numero_seguimiento | ubicacion_actual                | estado      | fecha_hora          |
+--------------------+---------------------------------+-------------+---------------------+
| 100001             | Centro de Distribución Medellín | en_transito | 2024-10-01 08:30:00 |
+--------------------+---------------------------------+-------------+---------------------+
#
---Obtener Historial de Envíos de un Cliente---

SELECT
E.id id_envio,
E.fecha_envio,
P.numero_seguimiento,
S.ubicacion_actual,
S.estado,
S.fecha_hora
FROM
Envio E
JOIN
Paquete P ON E.id_paquete = P.id
JOIN
Seguimiento S ON S.id_envio = E.id
WHERE
E.id_cliente = 1
ORDER BY
E.fecha_envio DESC, S.fecha_hora DESC;

+----------+-------------+--------------------+------------------------------------+-------------+---------------------+
| id_envio | fecha_envio | numero_seguimiento | ubicacion_actual                   | estado      | fecha_hora          |
+----------+-------------+--------------------+------------------------------------+-------------+---------------------+
|       12 | 2024-10-03  | 100003             | Centro de Distribución Los Ángeles | entregado   | 2024-10-03 10:00:00 |
|       10 | 2024-10-01  | 100001             | Centro de Distribución Medellín    | en_transito | 2024-10-01 08:30:00 |
+----------+-------------+--------------------+------------------------------------+-------------+---------------------+
#
---Listar Conductores y sus Rutas Asignadas--

SELECT
C.id id_conductor,
C.nombre nombre_conductor,
V.placa placa_vehiculo,
R.id id_ruta,
S.nombre nombre_sucursal_origen,
SD.nombre nombre_sucursal_destino
FROM
Conductor C
JOIN
Ruta R ON C.id = R.id_conductor
JOIN
Vehiculo V ON C.id_vehiculo = V.id
JOIN
Sucursal S ON R.id_sucursal_origen = S.id
JOIN
Sucursal SD ON R.id_sucursal_destino = SD.id;

+--------------+------------------+----------------+---------+------------------------+-------------------------+
| id_conductor | nombre_conductor | placa_vehiculo | id_ruta | nombre_sucursal_origen | nombre_sucursal_destino |
+--------------+------------------+----------------+---------+------------------------+-------------------------+
|            1 | Juan López       | ABC123         |       7 | Sucursal Medellín      | Sucursal Bogotá         |
|            2 | Laura García     | DEF456         |       8 | Sucursal Bogotá        | Sucursal Los Ángeles    |
|            3 | Miguel Díaz      | GHI789         |       9 | Sucursal Los Ángeles   | Sucursal Medellín       |
+--------------+------------------+----------------+---------+------------------------+-------------------------+

---Obtener Detalles de Rutas y Auxiliares Asignados---

SELECT
R.id id_ruta,
R.id_sucursal_origen,
R.id_sucursal_destino,
A.id_auxiliar,
A.nombre nombre_auxiliar
FROM
ruta R
JOIN
Auxiliar A ON R.id = A.id_ruta;

+---------+--------------------+---------------------+-------------+------------------+
| id_ruta | id_sucursal_origen | id_sucursal_destino | id_auxiliar | nombre_auxiliar  |
+---------+--------------------+---------------------+-------------+------------------+
|       7 |                  4 |                   5 |           7 | Andrea Fernández |
|       8 |                  5 |                   6 |           8 | Luis Ramírez     |
|       9 |                  6 |                   4 |           9 | María González   |
+---------+--------------------+---------------------+-------------+------------------+
#
---Generar Reporte de Paquetes por Sucursal y Estado---

SELECT
S.nombre AS sucursal,
E.estado_actual,
COUNT(P.id) AS cantidad_paquetes
FROM
Paquete P
JOIN
Envio E ON P.id = E.id_paquete
JOIN
Sucursal S ON E.id_sucursal_origen = S.id OR E.id_sucursal_destino = S.id
GROUP BY
S.nombre, E.estado_actual
ORDER BY
S.nombre, E.estado_actual;

+----------------------+---------------+-------------------+
| sucursal             | estado_actual | cantidad_paquetes |
+----------------------+---------------+-------------------+
| Sucursal Bogotá      | recibido      |                 1 |
| Sucursal Bogotá      | entregado     |                 1 |
| Sucursal Los Ángeles | recibido      |                 1 |
| Sucursal Los Ángeles | entregado     |                 1 |
| Sucursal Medellín    | entregado     |                 2 |
+----------------------+---------------+-------------------+
#
---Obtener Información Completa de un Paquete y su Historial de Seguimiento---

SELECT
P.numero_seguimiento,
P.peso,
P.dimensiones,
P.contenido,
P.valor_declarado,
P.tipo_servicio,
C.nombre nombre_cliente,
C.telefono telefono_cliente,
E.fecha_envio,
E.estado_actual,
S.ubicacion_actual,
S.estado estado_seguimiento,
S.fecha_hora
FROM
Paquete P
JOIN
Envio E ON P.id = E.id_paquete
JOIN
Cliente C ON E.id_cliente = C.CC
JOIN
Seguimiento S ON E.id = S.id_envio
WHERE
P.numero_seguimiento = '100001'
ORDER BY
S.fecha_hora DESC;

+--------------------+------+-------------+-----------+-----------------+---------------+----------------+------------------+-------------+---------------+---------------------------------+--------------------+---------------------+
| numero_seguimiento | peso | dimensiones | contenido | valor_declarado | tipo_servicio | nombre_cliente | telefono_cliente | fecha_envio | estado_actual | ubicacion_actual                | estado_seguimiento | fecha_hora          |
+--------------------+------+-------------+-----------+-----------------+---------------+----------------+------------------+-------------+---------------+---------------------------------+--------------------+---------------------+
| 100001             | 2.50 | 30x20x15    | Ropa      |           50.00 | nacional      | Carlos Pérez   | 3001234567       | 2024-10-01  | entregado     | Centro de Distribución Medellín | en_transito        | 2024-10-01 08:30:00 |
+--------------------+------+-------------+-----------+-----------------+---------------+----------------+------------------+-------------+---------------+---------------------------------+--------------------+---------------------+
1 row in set (0.00 sec)


---Obtener Paquetes Enviados Dentro de un Rango de Fechas--
#
SELECT
P.numero_seguimiento,
P.peso,
P.dimensiones,
P.contenido,
P.valor_declarado,
E.fecha_envio,
E.estado_actual,
C.nombre nombre_cliente,
C.telefono telefono_cliente
FROM
Paquete P
JOIN
Envio E ON P.id = E.id_paquete
JOIN
Cliente C ON E.id_cliente = C.CC
WHERE
E.fecha_envio BETWEEN '2024-10-01' AND '2024-10-31'
ORDER BY
E.fecha_envio;

+--------------------+------+-------------+--------------+-----------------+-------------+---------------+----------------+------------------+
| numero_seguimiento | peso | dimensiones | contenido    | valor_declarado | fecha_envio | estado_actual | nombre_cliente | telefono_cliente |
+--------------------+------+-------------+--------------+-----------------+-------------+---------------+----------------+------------------+
| 100001             | 2.50 | 30x20x15    | Ropa         |           50.00 | 2024-10-01  | entregado     | Carlos Pérez   | 3001234567       |
| 100002             | 1.20 | 20x15x10    | Electrónicos |          200.00 | 2024-10-02  | recibido      | Mariana Torres | 3109876543       |
| 100003             | 3.00 | 40x30x20    | Libros       |           30.00 | 2024-10-03  | entregado     | Carlos Pérez   | 3001234567       |
+--------------------+------+-------------+--------------+-----------------+-------------+---------------+----------------+------------------+
3 rows in set (0.00 sec)

---Obtener Paquetes con Ciertos Estados---
#
SELECT
P.numero_seguimiento,
P.peso,
P.dimensiones,
P.contenido,
P.valor_declarado,
E.fecha_envio,
E.estado_actual,
C.nombre nombre_cliente,
C.telefono telefono_cliente
FROM
Paquete P
JOIN
Envio E ON P.id = E.id_paquete
JOIN
Cliente C ON E.id_cliente = C.CC
WHERE
E.estado_actual IN ('en_transito', 'entregado')
ORDER BY
E.fecha_envio;


+--------------------+------+-------------+-----------+-----------------+-------------+---------------+----------------+------------------+
| numero_seguimiento | peso | dimensiones | contenido | valor_declarado | fecha_envio | estado_actual | nombre_cliente | telefono_cliente |
+--------------------+------+-------------+-----------+-----------------+-------------+---------------+----------------+------------------+
| 100001             | 2.50 | 30x20x15    | Ropa      |           50.00 | 2024-10-01  | entregado     | Carlos Pérez   | 3001234567       |
| 100003             | 3.00 | 40x30x20    | Libros    |           30.00 | 2024-10-03  | entregado     | Carlos Pérez   | 3001234567       |
+--------------------+------+-------------+-----------+-----------------+-------------+---------------+----------------+------------------+
2 rows in set (0.00 sec)
#
---Obtener Paquetes Excluyendo Ciertos Estados---

SELECT
P.numero_seguimiento,
P.peso,
P.dimensiones,
P.contenido,
P.valor_declarado,
E.fecha_envio,
E.estado_actual,
C.nombre nombre_cliente,
C.telefono telefono_cliente
FROM
Paquete P
JOIN
Envio E ON P.id = E.id_paquete
JOIN
Cliente C ON E.id_cliente = C.CC
WHERE
E.estado_actual NOT IN ('entregado', 'en_transito')  
ORDER BY
E.fecha_envio;

+--------------------+------+-------------+--------------+-----------------+-------------+---------------+----------------+------------------+
| numero_seguimiento | peso | dimensiones | contenido    | valor_declarado | fecha_envio | estado_actual | nombre_cliente | telefono_cliente |
+--------------------+------+-------------+--------------+-----------------+-------------+---------------+----------------+------------------+
| 100002             | 1.20 | 20x15x10    | Electrónicos |          200.00 | 2024-10-02  | recibido      | Mariana Torres | 3109876543       |
+--------------------+------+-------------+--------------+-----------------+-------------+---------------+----------------+------------------+
1 row in set (0.00 sec)
#
---Obtener Clientes con Envíos Realizados Dentro de un Rango de Fechas---

SELECT
C.CC,
C.nombre,
C.correo,
C.telefono,
COUNT(E.id) AS total_envios
FROM
Cliente C
JOIN
Envio E ON C.CC = E.id_cliente
WHERE
E.fecha_envio BETWEEN '2024-10-01' AND '2024-10-31'
GROUP BY
C.CC, C.nombre, C.correo, C.telefono;

+----+----------------+----------------------------+------------+--------------+
| CC | nombre         | correo                     | telefono   | total_envios |
+----+----------------+----------------------------+------------+--------------+
|  1 | Carlos Pérez   | carlos.perez@example.com   | 3001234567 |            2 |
|  2 | Mariana Torres | mariana.torres@example.com | 3109876543 |            1 |
+----+----------------+----------------------------+------------+--------------+
2 rows in set (0.00 sec)
#
---Obtener Conductores Disponibles que No Están Asignados a Ciertas Rutas---

SELECT
C.id,
C.nombre,
C.licencia,
C.telefono_principal
FROM
Conductor C
LEFT JOIN
conductor_ruta CR ON C.id = CR.id_conductor
WHERE
CR.id_ruta IS NULL OR CR.id_ruta NOT IN (1, 2, 3);

+----+--------------+----------+--------------------+
| id | nombre       | licencia | telefono_principal |
+----+--------------+----------+--------------------+
|  1 | Juan López   | L123456  | 3001234567         |
|  2 | Laura García | L654321  | 3007654321         |
|  3 | Miguel Díaz  | L789123  | 3009876543         |
+----+--------------+----------+--------------------+
3 rows in set (0.00 sec)
#
---Obtener Información de Paquetes con Valor Declarado Dentro de un Rango Específico---

SELECT
id,
numero_seguimiento,
peso,
dimensiones,
contenido,
valor_declarado,
tipo_servicio,
id_cliente,
id_ciudad_destino
FROM
Paquete
WHERE
valor_declarado BETWEEN 100 AND 500;

+----+--------------------+------+-------------+--------------+-----------------+---------------+------------+-------------------+
| id | numero_seguimiento | peso | dimensiones | contenido    | valor_declarado | tipo_servicio | id_cliente | id_ciudad_destino |
+----+--------------------+------+-------------+--------------+-----------------+---------------+------------+-------------------+
|  2 | 100002             | 1.20 | 20x15x10    | Electrónicos |          200.00 | internacional |          2 |                 2 |
+----+--------------------+------+-------------+--------------+-----------------+---------------+------------+-------------------+
1 row in set (0.00 sec)
#
---Obtener Envíos a Destinos Excluyendo Ciertas Ciudades---

SELECT
E.id AS id_envio,
E.fecha_envio,
P.numero_seguimiento,
C.nombre AS nombre_cliente,
CI.nombre AS ciudad_destino,
E.estado_actual
FROM
Envio E
JOIN
Paquete P ON E.id_paquete = P.id
JOIN
Cliente C ON E.id_cliente = C.CC
JOIN
ciudad CI ON P.id_ciudad_destino = CI.id
WHERE
CI.nombre NOT IN ('Medellín', 'Bogotá');

+----------+-------------+--------------------+----------------+----------------+---------------+
| id_envio | fecha_envio | numero_seguimiento | nombre_cliente | ciudad_destino | estado_actual |
+----------+-------------+--------------------+----------------+----------------+---------------+
|       12 | 2024-10-03  | 100003             | Carlos Pérez   | Los Ángeles    | entregado     |
+----------+-------------+--------------------+----------------+----------------+---------------+
1 row in set (0.00 sec)
#
---Obtener Seguimientos de Paquetes en un Rango de Fechas---

SELECT
S.id AS id_seguimiento,
S.id_envio,
S.ubicacion_actual,
S.estado,
S.fecha_hora,
E.fecha_envio,
P.numero_seguimiento,
C.nombre AS nombre_cliente
FROM
Seguimiento S
JOIN
Envio E ON S.id_envio = E.id
JOIN
Paquete P ON E.id_paquete = P.id
JOIN
Cliente C ON E.id_cliente = C.CC
WHERE
S.fecha_hora BETWEEN '2024-10-01' AND '2024-10-31';

+----------------+----------+------------------------------------+-------------+---------------------+-------------+--------------------+----------------+
| id_seguimiento | id_envio | ubicacion_actual                   | estado      | fecha_hora          | fecha_envio | numero_seguimiento | nombre_cliente |
+----------------+----------+------------------------------------+-------------+---------------------+-------------+--------------------+----------------+
|              4 |       10 | Centro de Distribución Medellín    | en_transito | 2024-10-01 08:30:00 | 2024-10-01  | 100001             | Carlos Pérez   |
|              5 |       11 | Sucursal Bogotá                    | recibido    | 2024-10-02 09:15:00 | 2024-10-02  | 100002             | Mariana Torres |
|              6 |       12 | Centro de Distribución Los Ángeles | entregado   | 2024-10-03 10:00:00 | 2024-10-03  | 100003             | Carlos Pérez   |
+----------------+----------+------------------------------------+-------------+---------------------+-------------+--------------------+----------------+
3 rows in set (0.00 sec)
#
---Obtener Clientes que Tienen Ciertos Tipos de Paquetes---

SELECT
DISTINCT C.CC AS id_cliente,
C.nombre AS nombre_cliente,
C.correo AS correo_cliente,
C.direccion AS direccion_cliente,
C.telefono AS telefono_cliente
FROM
Cliente C
JOIN
Paquete P ON C.CC = P.id_cliente
WHERE
P.tipo_servicio IN ('nacional', 'express');

+------------+----------------+--------------------------+-----------------------------+------------------+
| id_cliente | nombre_cliente | correo_cliente           | direccion_cliente           | telefono_cliente |
+------------+----------------+--------------------------+-----------------------------+------------------+
|          1 | Carlos Pérez   | carlos.perez@example.com | Carrera 80 #15-45, Medellín | 3001234567       |
+------------+----------------+--------------------------+-----------------------------+------------------+
1 row in set (0.00 sec)

Hecho por [Ricardo Arevalo Boorquez](https://github.com/RicardoArevaloB)
