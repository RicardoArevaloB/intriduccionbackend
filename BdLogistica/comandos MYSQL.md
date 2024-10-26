CREATE DATABASE amazon;
USE amazon;

CREATE TABLE pais (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
CONSTRAINT PK_PAIS_Id PRIMARY KEY (id)
);

CREATE TABLE region (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
idpais_fk INT,
CONSTRAINT PK_REGION_Id PRIMARY KEY (id),
CONSTRAINT FK_PaisRegion FOREIGN KEY (idpais_fk) REFERENCES pais(id)
);

CREATE TABLE ciudad (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
idregion_fk INT,
CONSTRAINT PK_CIUDAD_Id PRIMARY KEY (id),
CONSTRAINT FK_CiudadRegion FOREIGN KEY (idregion_fk) REFERENCES region(id)
);

CREATE TABLE empresa (
nit INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
fundador VARCHAR(50),
fecha_fundacion DATE,
fecha_registro DATE,
CONSTRAINT PK_EMPRESA_Nit PRIMARY KEY (nit)
);

CREATE TABLE Sucursal (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(100) NOT NULL,
direccion VARCHAR(255),
nit_empresa INT,
id_ciudad INT,
CONSTRAINT PK_SUCURSAL_Id PRIMARY KEY (id),
CONSTRAINT FK_SucursalEmpresa FOREIGN KEY (nit_empresa) REFERENCES empresa(nit),
CONSTRAINT FK_SucursalCiudad FOREIGN KEY (id_ciudad) REFERENCES ciudad(id)
);

CREATE TABLE centro_distribucion (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(100) NOT NULL,
direccion VARCHAR(70),
id_ciudad INT,
CONSTRAINT PK_CENTRO_Id PRIMARY KEY (id),
CONSTRAINT FK_CentroCiudad FOREIGN KEY (id_ciudad) REFERENCES ciudad(id)
);

CREATE TABLE Cliente (
CC INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(100) NOT NULL,
correo VARCHAR(100),
direccion VARCHAR(255),
telefono VARCHAR(20),
CONSTRAINT PK_CLIENTE_CC PRIMARY KEY (CC)
);

CREATE TABLE Paquete (
id INT NOT NULL AUTO_INCREMENT,
numero_seguimiento VARCHAR(50) UNIQUE NOT NULL,
peso DECIMAL(10, 2),
dimensiones VARCHAR(50),
contenido TEXT,
valor_declarado DECIMAL(12, 2),
tipo_servicio ENUM('nacional', 'internacional', 'express', 'estandar'),
id_cliente INT,
id_ciudad_destino INT,
CONSTRAINT PK_PAQUETE_Id PRIMARY KEY (id),
CONSTRAINT FK_PaqueteCliente FOREIGN KEY (id_cliente) REFERENCES Cliente(CC),
CONSTRAINT FK_PaqueteCiudad FOREIGN KEY (id_ciudad_destino) REFERENCES ciudad(id)
);

CREATE TABLE Envio (
id INT NOT NULL AUTO_INCREMENT,
id_paquete INT,
id_cliente INT,
fecha_envio DATE,
estado_actual ENUM('recibido', 'en_transito', 'entregado', 'retenido_en_aduana') DEFAULT 'recibido',
id_ruta INT,
id_sucursal_origen INT,
id_sucursal_destino INT,
id_centro_distribucion INT,
CONSTRAINT PK_ENVIO_Id PRIMARY KEY (id),
CONSTRAINT FK_EnvioPaquete FOREIGN KEY (id_paquete) REFERENCES Paquete(id),
CONSTRAINT FK_EnvioCliente FOREIGN KEY (id_cliente) REFERENCES Cliente(CC),
CONSTRAINT FK_EnvioSucursalOrigen FOREIGN KEY (id_sucursal_origen) REFERENCES Sucursal(id),
CONSTRAINT FK_EnvioSucursalDestino FOREIGN KEY (id_sucursal_destino) REFERENCES Sucursal(id),
CONSTRAINT FK_EnvioCentroDistribucion FOREIGN KEY (id_centro_distribucion) REFERENCES centro_distribucion(id)
);

CREATE TABLE Seguimiento (
id INT NOT NULL AUTO_INCREMENT,
id_envio INT,
ubicacion_actual VARCHAR(255),
estado ENUM('recibido', 'en_transito', 'entregado', 'retenido_en_aduana'),
fecha_hora DATETIME,
CONSTRAINT PK_SEGUIMIENTO_Id PRIMARY KEY (id),
CONSTRAINT FK_SeguimientoEnvio FOREIGN KEY (id_envio) REFERENCES Envio(id)
);

CREATE TABLE Vehiculo (
id INT NOT NULL AUTO_INCREMENT,
placa VARCHAR(20) UNIQUE NOT NULL,
modelo VARCHAR(100),
capacidad DECIMAL(10, 2),
CONSTRAINT PK_VEHICULO_Id PRIMARY KEY (id)
);

CREATE TABLE Conductor (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(100),
licencia VARCHAR(50),
id_vehiculo INT,
CONSTRAINT PK_CONDUCTOR_Id PRIMARY KEY (id),
CONSTRAINT FK_ConductorVehiculo FOREIGN KEY (id_vehiculo) REFERENCES Vehiculo(id)
);

CREATE TABLE ruta (
id INT NOT NULL AUTO_INCREMENT,
id_sucursal_origen INT,
id_sucursal_destino INT,
id_conductor INT,
id_centro_distribucion INT,
CONSTRAINT PK_RUTA_Id PRIMARY KEY (id),
CONSTRAINT FK_RutaSucursalOrigen FOREIGN KEY (id_sucursal_origen) REFERENCES Sucursal(id),
CONSTRAINT FK_RutaSucursalDestino FOREIGN KEY (id_sucursal_destino) REFERENCES Sucursal(id),
CONSTRAINT FK_RutaConductor FOREIGN KEY (id_conductor) REFERENCES Conductor(id),
CONSTRAINT FK_RutaCentroDistribucion FOREIGN KEY (id_centro_distribucion) REFERENCES centro_distribucion(id)
);

CREATE TABLE conductor_ruta (
id_conductor INT,
id_ruta INT,
id_vehiculo INT,
id_centro_distribucion INT,
CONSTRAINT PK_CONDUCTOR_RUTA PRIMARY KEY (id_conductor, id_ruta),
CONSTRAINT FK_ConductorRutaConductor FOREIGN KEY (id_conductor) REFERENCES Conductor(id),
CONSTRAINT FK_ConductorRutaRuta FOREIGN KEY (id_ruta) REFERENCES ruta(id),
CONSTRAINT FK_ConductorRutaVehiculo FOREIGN KEY (id_vehiculo) REFERENCES Vehiculo(id),
CONSTRAINT FK_ConductorRutaCentroDistribucion FOREIGN KEY (id_centro_distribucion) REFERENCES centro_distribucion(id)
);

CREATE TABLE Auxiliar (
id_auxiliar INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(100),
id_conductor INT,
id_ruta INT,
CONSTRAINT PK_AUXILIAR_Id PRIMARY KEY (id_auxiliar),
CONSTRAINT FK_AuxiliarConductor FOREIGN KEY (id_conductor) REFERENCES Conductor(id),
CONSTRAINT FK_AuxiliarRuta FOREIGN KEY (id_ruta) REFERENCES ruta(id)
);

CREATE TABLE telefono_conductor (
id INT NOT NULL AUTO_INCREMENT,
id_conductor INT,
telefono VARCHAR(20),
CONSTRAINT PK_telefono_conductor PRIMARY KEY (id),
CONSTRAINT FK_conductor_telefono FOREIGN KEY (id_conductor) REFERENCES Conductor(id)
);

ALTER TABLE Conductor
ADD COLUMN telefono_principal VARCHAR(20);

---

INSERT INTO pais (nombre) VALUES
('Colombia'),
('Estados Unidos'),
('México');

INSERT INTO region (nombre, idpais_fk) VALUES
('Antioquia', 1),
('Cundinamarca', 1),
('California', 2),
('Ciudad de México', 3);

INSERT INTO ciudad (nombre, idregion_fk) VALUES
('Medellín', 1),
('Bogotá', 2),
('Los Ángeles', 3),
('Ciudad de México', 4);

INSERT INTO empresa (nombre, fundador, fecha_fundacion, fecha_registro) VALUES
('Amazon Colombia', 'Jeff Bezos', '1994-07-05', '2022-01-01');

INSERT INTO Sucursal (nombre, direccion, nit_empresa, id_ciudad) VALUES
('Sucursal Medellín', 'Calle 50 #45-30', 1, 1),
('Sucursal Bogotá', 'Avenida 68 #70-90', 1, 2),
('Sucursal Los Ángeles', '123 Sunset Blvd', 2, 3);

INSERT INTO centro_distribucion (nombre, direccion, id_ciudad) VALUES
('Centro Distribución Medellín', 'Carrera 65 #10-20', 1),
('Centro Distribución Bogotá', 'Calle 80 #60-70', 2),
('Centro Distribución Los Ángeles', '456 Ocean Ave', 3);

INSERT INTO Cliente (nombre, correo, direccion, telefono) VALUES
('Carlos Pérez', 'carlos.perez@example.com', 'Carrera 80 #15-45, Medellín', '3001234567'),
('Mariana Torres', 'mariana.torres@example.com', 'Calle 100 #12-34, Bogotá', '3109876543'),
('John Smith', 'john.smith@example.com', '789 Maple St, Los Ángeles', '2134567890');

INSERT INTO Paquete (numero_seguimiento, peso, dimensiones, contenido, valor_declarado, tipo_servicio, id_cliente, id_ciudad_destino) VALUES
('100001', 2.5, '30x20x15', 'Ropa', 50.00, 'nacional', 1, 1),
('100002', 1.2, '20x15x10', 'Electrónicos', 200.00, 'internacional', 2, 2),
('100003', 3.0, '40x30x20', 'Libros', 30.00, 'express', 1, 3);

INSERT INTO ruta (id_sucursal_origen, id_sucursal_destino, id_conductor, id_centro_distribucion) VALUES
(4, 5, 1, 1),
(5, 6, 2, 2),
(6, 4, 3, 3);

INSERT INTO Envio (id_paquete, id_cliente, fecha_envio, estado_actual, id_ruta, id_sucursal_origen, id_sucursal_destino, id_centro_distribucion) VALUES
(1, 1, '2024-10-01', 'en_transito', 7, 4, 5, 1),
(2, 2, '2024-10-02', 'recibido', 8, 5, 6, 2),
(3, 1, '2024-10-03', 'entregado', 9, 6, 4, 3);

INSERT INTO Seguimiento (id_envio, ubicacion_actual, estado, fecha_hora) VALUES
(10, 'Centro de Distribución Medellín', 'en_transito', '2024-10-01 08:30:00'),
(11, 'Sucursal Bogotá', 'recibido', '2024-10-02 09:15:00'),
(12, 'Centro de Distribución Los Ángeles', 'entregado', '2024-10-03 10:00:00');

INSERT INTO Vehiculo (placa, modelo, capacidad) VALUES
('ABC123', 'Camión 1', 5000.00),
('DEF456', 'Camión 2', 3000.00),
('GHI789', 'Camión 3', 4000.00);

INSERT INTO Conductor (nombre, licencia, id_vehiculo) VALUES
('Juan López', 'L123456', 1),
('Laura García', 'L654321', 2),
('Miguel Díaz', 'L789123', 3);

INSERT INTO conductor_ruta (id_conductor, id_ruta, id_vehiculo, id_centro_distribucion) VALUES
(1, 7, 1, 1),
(2, 8, 2, 2),
(3, 9, 3, 3);

INSERT INTO Auxiliar (nombre, cedula, id_conductor, id_ruta) VALUES
('Andrea Fernández', 1000001234, 1, 7),
('Luis Ramírez', 1000005678, 2, 8),
('María González', 1000009876, 3, 9);

UPDATE Conductor SET telefono_principal = '3001234567' WHERE id = 1;
UPDATE Conductor SET telefono_principal = '3007654321' WHERE id = 2;
UPDATE Conductor SET telefono_principal = '3009876543' WHERE id = 3;

INSERT INTO telefono_conductor (id_conductor, telefono) VALUES
(1, '3001234567'),
(1, '3101234567'),
(2, '3007654321'),
(2, '3107654321'),
(3, '3009876543'),
(3, '3109876543');

---

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

---Actualizar el Estado de un Paquete---

UPDATE Envio
SET estado_actual = 'entregado'
WHERE id_paquete = 1;

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
P.numero_seguimiento = '100001'
ORDER BY
S.fecha_hora DESC
LIMIT 1;

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

---Obtener Paquetes Enviados Dentro de un Rango de Fechas--

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

---Obtener Paquetes con Ciertos Estados---

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

---Caso de Uso 7: Obtener Auxiliares Asignados a Rutas specíficas---

SELECT
A.id_auxiliar,
A.nombre,
A.id_conductor,
A.id_ruta,
R.id_sucursal_origen,
R.id_sucursal_destino
FROM
Auxiliar A
JOIN
ruta R ON A.id_ruta = R.id
WHERE
R.id = 1;

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
