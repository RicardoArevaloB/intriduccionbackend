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
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
fecha_fundacion DATE,
fundado_por VARCHAR(50),
fecha_registro DATE,
nit VARCHAR(20),
idciudad_fk INT,
CONSTRAINT PK_EMPRESA_Id PRIMARY KEY (id),
CONSTRAINT FK_EmpresaCiudad FOREIGN KEY (idciudad_fk) REFERENCES ciudad(id)
);

CREATE TABLE sucursal (
id INT NOT NULL AUTO_INCREMENT,
direccion VARCHAR(100),
idempresa_fk INT,
representante VARCHAR(50),
CONSTRAINT PK_SUCURSAL_Id PRIMARY KEY (id),
CONSTRAINT FK_SucursalEmpresa FOREIGN KEY (idempresa_fk) REFERENCES empresa(id)
);

CREATE TABLE cliente (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
email VARCHAR(100),
CONSTRAINT PK_CLIENTE_Id PRIMARY KEY (id)
);

CREATE TABLE repuesto (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
valor DECIMAL(10, 2),
CONSTRAINT PK_REPUESTO_Id PRIMARY KEY (id)
);

CREATE TABLE facturacion (
id INT NOT NULL AUTO_INCREMENT,
idventa_fk INT,
fecha DATE,
monto DECIMAL(10, 2),
pais VARCHAR(50),
CONSTRAINT PK_FACTURACION_Id PRIMARY KEY (id)
);

CREATE TABLE bicicleta (
id INT NOT NULL AUTO_INCREMENT,
modelo VARCHAR(50),
fecha_fabricacion DATE,
talla VARCHAR(10),
CONSTRAINT PK_BICICLETA_Id PRIMARY KEY (id)
);

CREATE TABLE proveedor (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
direccion VARCHAR(100),
telefono VARCHAR(20),
email VARCHAR(100),
CONSTRAINT PK_PROVEEDOR_Id PRIMARY KEY (id)
);

CREATE TABLE empleado (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
idtelefono VARCHAR(20),
salario DECIMAL(10, 2),
CONSTRAINT PK_EMPLEADO_Id PRIMARY KEY (id)
);

CREATE TABLE stock (
id INT NOT NULL AUTO_INCREMENT,
idbicicleta_fk INT,
idrepuesto_fk INT,
cantidad INT,
CONSTRAINT PK_STOCK_Id PRIMARY KEY (id),
CONSTRAINT FK_StockBicicleta FOREIGN KEY (idbicicleta_fk) REFERENCES bicicleta(id),
CONSTRAINT FK_StockRepuesto FOREIGN KEY (idrepuesto_fk) REFERENCES repuesto(id)
);

CREATE TABLE productos (
id INT NOT NULL AUTO_INCREMENT,
idbicicleta_fk INT,
idrepuesto_fk INT,
idstock_fk INT,
CONSTRAINT PK_PRODUCTOS_Id PRIMARY KEY (id),
CONSTRAINT FK_ProductosBicicleta FOREIGN KEY (idbicicleta_fk) REFERENCES bicicleta(id),
CONSTRAINT FK_ProductosRepuesto FOREIGN KEY (idrepuesto_fk) REFERENCES repuesto(id),
CONSTRAINT FK_ProductosStock FOREIGN KEY (idstock_fk) REFERENCES stock(id)
);

CREATE TABLE area (
id INT NOT NULL AUTO_INCREMENT,
nombre_area VARCHAR(50),
idempleado_fk INT,
CONSTRAINT PK_AREA_Id PRIMARY KEY (id),
CONSTRAINT FK_AreaEmpleado FOREIGN KEY (idempleado_fk) REFERENCES empleado(id)
);

CREATE TABLE venta (
id INT NOT NULL AUTO_INCREMENT,
idcliente_fk INT,
idempleado_fk INT,
idsucursal_fk INT,
idproducto INT,
area VARCHAR(50),
CONSTRAINT PK_VENTA_Id PRIMARY KEY (id),
CONSTRAINT FK_VentaCliente FOREIGN KEY (idcliente_fk) REFERENCES cliente(id),
CONSTRAINT FK_VentaEmpleado FOREIGN KEY (idempleado_fk) REFERENCES empleado(id),
CONSTRAINT FK_VentaSucursal FOREIGN KEY (idsucursal_fk) REFERENCES sucursal(id),
CONSTRAINT FK_VentaProducto FOREIGN KEY (idproducto) REFERENCES productos(id) -- Asegúrate de que este id esté relacionado correctamente
);

CREATE TABLE metodo_pago (
id INT NOT NULL AUTO_INCREMENT,
idventa_fk INT,
CONSTRAINT PK_METODO_PAGO_Id PRIMARY KEY (id),
CONSTRAINT FK_MetodoPagoVenta FOREIGN KEY (idventa_fk) REFERENCES venta(id)
);

--INSERCIONES--

INSERT INTO pais (nombre) VALUES ('Colombia');

INSERT INTO region (nombre, idpais_fk) VALUES
('Antioquia', 1),  
('Cundinamarca', 1),  
('Valle del Cauca', 1),  
('Atlántico', 1);

INSERT INTO ciudad (nombre, idregion_fk) VALUES
('Medellín', 1),  
('Bogotá', 2),  
('Cali', 3),  
('Barranquilla', 4);

INSERT INTO empresa (nombre, fecha_fundacion, fundado_por, fecha_registro, nit, idciudad_fk)
VALUES ('Campus Bike', '2010-01-15', 'Juan Pérez', '2010-02-20', '900123456-1', 2);

INSERT INTO sucursal (direccion, idempresa_fk, representante) VALUES
('Calle 123 # 45-67', 1, 'Carlos Rodríguez'),  
('Carrera 10 # 20-30', 1, 'Ana Fernández'),  
('Avenida 5 # 10-15', 1, 'Sofía Martínez'),  
('Diagonal 15 # 50-60', 1, 'Luis Torres');

INSERT INTO cliente (nombre, email) VALUES ('Ana Gómez', 'ana.gomez@example.com');
INSERT INTO cliente (nombre, email) VALUES ('Luis Martínez', 'luis.martinez@example.com');

INSERT INTO repuesto (nombre, valor) VALUES
('Cadena', 20.50),
('Frenos', 15.75),
('Cámara de aire', 10.00),
('Tijeras para frenos', 25.00),
('Manilla de freno', 5.50),
('Cambio de velocidades', 30.00),
('Pedales', 12.00),
('Asiento', 18.00);

INSERT INTO bicicleta (modelo, fecha_fabricacion, talla) VALUES
('Mountain Bike', '2023-05-10', 'M'),  
('Bicicleta de ruta', '2023-03-15', 'L'),  
('Bicicleta todo terreno', '2023-07-20', 'S'),
('Bicicleta para niños', '2023-04-05', 'XS'),  
('Bicicleta híbrida', '2023-06-12', 'M'),  
('Bicicleta de paseo', '2023-08-15', 'L');

INSERT INTO proveedor (nombre, direccion, telefono, email)
VALUES ('Proveedores de Bicicletas S.A.', 'Carrera 50 # 22-34', '3001234567', 'info@proveedoresbicicletas.com');

INSERT INTO empleado (nombre, idtelefono, salario)
VALUES ('María López', '3009876543', 1200.00);
INSERT INTO empleado (nombre, idtelefono, salario)
VALUES ('Javier Pérez', '3101234567', 1100.00);

INSERT INTO stock (idbicicleta_fk, idrepuesto_fk, cantidad) VALUES (1, NULL, 10);
INSERT INTO stock (idbicicleta_fk, idrepuesto_fk, cantidad) VALUES (NULL, 1, 50);

INSERT INTO productos (idbicicleta_fk, idrepuesto_fk, idstock_fk) VALUES (1, NULL, 1);
INSERT INTO productos (idbicicleta_fk, idrepuesto_fk, idstock_fk) VALUES (NULL, 1, 2);

INSERT INTO area (nombre_area, idempleado_fk) VALUES ('Ventas', 1);

INSERT INTO venta (idcliente_fk, idempleado_fk, idsucursal_fk, idproducto, area)
VALUES (1, 1, 1, 1, 'Ventas');

INSERT INTO metodo_pago (idventa_fk) VALUES (1);

---CONSULTAS----

-- Consultar todos los países --

SELECT nombre
FROM pais;

-- Consultar todas las regiones junto con el país --

SELECT region.nombre, pais.nombre
FROM region
JOIN pais ON region.idpais_fk = pais.id;

-- Consultar todas las ciudades junto con su región --

SELECT ciudad.nombre, region.nombre
FROM ciudad
JOIN region ON ciudad.idregion_fk = region.id;

-- Consultar todas las bicicletas y sus detalles --

SELECT modelo, fecha_fabricacion, talla
FROM bicicleta;

-- Consultar todos los repuestos y su valor --

SELECT nombre, valor
FROM repuesto;

-- Consultar todos los clientes --

SELECT nombre, email
FROM cliente;

-- Consultar todos los empleados --

SELECT nombre, salario
FROM empleado;

-- Consultar las sucursales y sus representantes --

SELECT direccion, representante
FROM sucursal;

-- Consultar todas las ventas y sus detalles--

SELECT venta.id, cliente.nombre, empleado.nombre, sucursal.direccion, venta.area
FROM venta
JOIN cliente ON venta.idcliente_fk = cliente.id
JOIN empleado ON venta.idempleado_fk = empleado.id
JOIN sucursal ON venta.idsucursal_fk = sucursal.id;

-- Consultar los métodos de pago por venta --

SELECT venta.id, metodo_pago.id
FROM venta
JOIN metodo_pago ON venta.id = metodo_pago.idventa_fk;
