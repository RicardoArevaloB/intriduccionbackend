CREATE TABLE pais (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
CONSTRAINT PK_PAIS_Id PRIMARY KEY (id)
);

CREATE TABLE ciudad (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
idpais_fk INT,
CONSTRAINT PK_CIUDAD_Id PRIMARY KEY (id),
CONSTRAINT FK_PaisCiudad FOREIGN KEY (idpais_fk) REFERENCES pais(id)
);

CREATE TABLE aeropuerto (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
idciudad_fk INT,
CONSTRAINT PK_AEROPUERTO_Id PRIMARY KEY (id),
CONSTRAINT FK_CiudadAeropuerto FOREIGN KEY (idciudad_fk) REFERENCES ciudad(id)
);

CREATE TABLE aerolinea (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
CONSTRAINT PK_AEROLINEA_Id PRIMARY KEY (id)
);

CREATE TABLE fabricante (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
CONSTRAINT PK_FABRICANTE_Id PRIMARY KEY (id)
);

CREATE TABLE estado (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
CONSTRAINT PK_ESTADO_Id PRIMARY KEY (id)
);

CREATE TABLE modelo (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
idfabricante_fk INT,
CONSTRAINT PK_MODELO_Id PRIMARY KEY (id),
CONSTRAINT FK_FabricanteModelo FOREIGN KEY (idfabricante_fk) REFERENCES fabricante(id)
);

CREATE TABLE avion (
nromatricula VARCHAR(30) NOT NULL,
capacidad INT,
fechafabricacion DATE,
idestado_fk INT,
idmodelo_fk INT,
idaerolinea_fk INT,
CONSTRAINT PK_AVION_NROPRIMARIO PRIMARY KEY (nromatricula),
CONSTRAINT FK_EstadoAvion FOREIGN KEY (idestado_fk) REFERENCES estado(id),
CONSTRAINT FK_ModeloAvion FOREIGN KEY (idmodelo_fk) REFERENCES modelo(id),
CONSTRAINT FK_AerolineaAvion FOREIGN KEY (idaerolinea_fk) REFERENCES aerolinea(id)
);

CREATE TABLE trayecto (
id INT NOT NULL AUTO_INCREMENT,
fechatrayecto DATE,
valor DOUBLE,
ciudadorigen_fk INT,
ciudaddestino_fk INT,
CONSTRAINT PK_TRAYECTO_Id PRIMARY KEY (id),
CONSTRAINT FK_CiudadOrigenTrayecto FOREIGN KEY (ciudadorigen_fk) REFERENCES ciudad(id),
CONSTRAINT FK_CiudadDestinoTrayecto FOREIGN KEY (ciudaddestino_fk) REFERENCES ciudad(id)
);

CREATE TABLE escala (
id INT NOT NULL AUTO_INCREMENT,
idtrayecto_fk INT,
idavion_fk VARCHAR(30),
nnrovuelo INT,
idaeropuerto_fk INT,
CONSTRAINT PK_ESCALA_Id PRIMARY KEY (id),
CONSTRAINT FK_TrayectoEscala FOREIGN KEY (idtrayecto_fk) REFERENCES trayecto(id),
CONSTRAINT FK_AvionEscala FOREIGN KEY (idavion_fk) REFERENCES avion(nromatricula),
CONSTRAINT FK_AeropuertoEscala FOREIGN KEY (idaeropuerto_fk) REFERENCES aeropuerto(id)
);

CREATE TABLE gates (
id INT NOT NULL AUTO_INCREMENT,
nropuerta INT,
idaeropuerto_fk INT,
CONSTRAINT PK_GATES_Id PRIMARY KEY (id),
CONSTRAINT FK_AeropuertoGates FOREIGN KEY (idaeropuerto_fk) REFERENCES aeropuerto(id)
);

CREATE TABLE roltripulacion (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
CONSTRAINT PK_ROLTRIPULACION_Id PRIMARY KEY (id)
);

CREATE TABLE empleado (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
idrol_fk INT,
fechaingreso DATE,
idaerolinea_fk INT,
idaeropuerto_fk INT,
CONSTRAINT PK_EMPLEADO_Id PRIMARY KEY (id),
CONSTRAINT FK_RolEmpleado FOREIGN KEY (idrol_fk) REFERENCES roltripulacion(id),
CONSTRAINT FK_AerolineaEmpleado FOREIGN KEY (idaerolinea_fk) REFERENCES aerolinea(id),
CONSTRAINT FK_AeropuertoEmpleado FOREIGN KEY (idaeropuerto_fk) REFERENCES aeropuerto(id)
);

CREATE TABLE trayecto_tripulacion (
idempleado_fk INT,
idescala_fk INT,
CONSTRAINT PK_TRAYECTO_TRIPULACION PRIMARY KEY (idempleado_fk, idescala_fk),
CONSTRAINT FK_EmpleadoTrayecto FOREIGN KEY (idempleado_fk) REFERENCES empleado(id),
CONSTRAINT FK_EscalaTrayecto FOREIGN KEY (idescala_fk) REFERENCES escala(id)
);

CREATE TABLE revision (
id INT NOT NULL AUTO_INCREMENT,
fecha DATE,
idavion_fk VARCHAR(30),
CONSTRAINT PK_REVISION_Id PRIMARY KEY (id),
CONSTRAINT FK_AvionRevision FOREIGN KEY (idavion_fk) REFERENCES avion(nromatricula)
);

CREATE TABLE rev_empleado (
idempleado_fk INT,
idrevision_fk INT,
CONSTRAINT PK_REV_EMPLEADO PRIMARY KEY (idempleado_fk, idrevision_fk),
CONSTRAINT FK_EmpleadoRevision FOREIGN KEY (idempleado_fk) REFERENCES empleado(id),
CONSTRAINT FK_RevisionEmpleado FOREIGN KEY (idrevision_fk) REFERENCES revision(id)
);

CREATE TABLE detalle_revision (
idrevision_fk INT,
descripcion TEXT,
fecharev DATE,
idempleado_fk INT,
CONSTRAINT PK_DETALLE_REVISION PRIMARY KEY (idrevision_fk, idempleado_fk),
CONSTRAINT FK_RevisionDetalle FOREIGN KEY (idrevision_fk) REFERENCES revision(id),
CONSTRAINT FK_EmpleadoDetalle FOREIGN KEY (idempleado_fk) REFERENCES empleado(id)
);

CREATE TABLE tipodocumento (
id INT NOT NULL AUTO_INCREMENT,
nombre VARCHAR(50),
CONSTRAINT PK_TIPODOCUMENTO_Id PRIMARY KEY (id)
);

CREATE TABLE clientes (
id VARCHAR(20) NOT NULL,
edad INT,
idtipodoc_fk INT,
CONSTRAINT PK_CLIENTES_Id PRIMARY KEY (id),
CONSTRAINT FK_TipoDocClientes FOREIGN KEY (idtipodoc_fk) REFERENCES tipodocumento(id)
);

CREATE TABLE reservaviaje (
id INT NOT NULL AUTO_INCREMENT,
fecha DATE,
idtrayecto_fk INT,
CONSTRAINT PK_RESERVAVIAJE_Id PRIMARY KEY (id),
CONSTRAINT FK_TrayectoReserva FOREIGN KEY (idtrayecto_fk) REFERENCES trayecto(id)
);

CREATE TABLE tarifasvuelo (
id INT NOT NULL AUTO_INCREMENT,
descripcion VARCHAR(50),
valor DOUBLE,
CONSTRAINT PK_TARIFASVUELO_Id PRIMARY KEY (id)
);

CREATE TABLE detalle_reserva (
id INT NOT NULL AUTO_INCREMENT,
idcliente_fk VARCHAR(20),
idtarifa_fk INT,
valor DOUBLE,
CONSTRAINT PK_DETALLE_RESERVA_Id PRIMARY KEY (id),
CONSTRAINT FK_ClientesReserva FOREIGN KEY (idcliente_fk) REFERENCES clientes(id),
CONSTRAINT FK_TarifaReserva FOREIGN KEY (idtarifa_fk) REFERENCES tarifasvuelo(id)
);
