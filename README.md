# Desempe-o2
Aquí profe James, le dejo el trabajo de Desempeño2 DB que se debe entregar el 19 de abril.

Aca se encuentra todo el SCRIPT y esta ordenado para que lo ejecute paso por paso | Recomendado usar HEIDISQL

# Hola, para ejecutar todo bien o en el orden que se tiene que hacer es simple:
# Todas las Tablas,Foreign Keys, Primary Keys , insert values , pruebas de index y las
# consultas de ejemplo ya estan aqui.
------------------------------------------------------------------------------------------------------------------------
-- Orden para ejecutar cada fuente cada línea de código ---
# 1. "Creación de las tablas." Line code 26-104 | estan ya ordenadas las tablas y separadas cada una con los campos que tiene.
# 2. "Primary Keys" Line code 105-132 | aqui se ejecutaran las "Primary Keys" todas tambíen en orden estan cada una.
# 3. "Foreign Keys" Line code 133-162 | Mismo caso que el de las primary keys.
# 4. "Index" Line code 163-183 | se ejecutaran aca los index, todos tambíen en orden.
# 5. "Insert Values" Line code 230-275 | se añaden valores a la tabla para al momento de usar "SHOW * FROM" o "DESCRIBE" ya mostrar lo que tiene la tabla.
# 6. "Consultas y Consultas Avan." Line code 184-232 | Aquí se consultan lo que tienen las tablas
# 7. "Stored Procedures" Line code 280-377 | Se almacenaran las procedures del coso aquì 
# 8. "Views" Line code 378 - 428 | Se ejecutaran las views.
# 9. "Events" Line code 431 - 522 | Aquí se crearan los events y se ejecutaran tambíen.
-------------------------------------------------------------------------------------------------------------------------
-- Crear la base de datos
CREATE DATABASE IF NOT EXISTS GreenPower;

-- Seleccionarla para usarla
USE GreenPower;

-- 1. Creación de las tablas.

-- Tabla principal de plantas de energía --

CREATE TABLE Planta (
    codigo_planta INT,
    nombre VARCHAR(255),
    tipo VARCHAR(255),
    ubicacion VARCHAR(255),
    capacidad_instalada_MW DECIMAL(10,2),
    estado_operativo VARCHAR(255)
);

# 
SELECT * FROM Planta

DESCRIBE Planta;

-- Tabla de equipos
CREATE TABLE Equipo (
    numero_serie INT,
    tipo VARCHAR(255),
    marca VARCHAR(255),
    modelo VARCHAR(255),
    codigo_planta INT
);

SELECT * FROM Equipo

DESCRIBE Equipo;

-- Tabla de estaciones meteorológicas
CREATE TABLE Estacion (
    codigo_estacion INT,
    ubicacion VARCHAR(255)
);

SELECT * FROM Estacion

DESCRIBE Estacion;

-- Tabla de lecturas meteorológicas
CREATE TABLE Lectura (
    id_lectura INT NOT NULL,
    codigo_estacion INT,
    fecha DATETIME,
    tipo VARCHAR(50),
    valor DECIMAL(10,2)
);

SELECT * FROM Lectura

DESCRIBE Lectura;

SHOW TABLES;

-- Tabla de producción energética
CREATE TABLE Produccion (
    id_produccion INT NOT NULL,
    codigo_planta INT,
    fecha DATE,
    energia_generada DECIMAL(10,2)
);

SELECT * FROM Produccion

DESCRIBE Produccion;

-- Tabla de incidencias
CREATE TABLE Incidencia (
    codigo_incidencia INT,
    codigo_planta INT,
    descripcion TEXT
);

SELECT * FROM Incidencia

DESCRIBE incidencia;

CREATE TABLE Contrato (
    id_contrato INT AUTO_INCREMENT PRIMARY KEY,
    codigo_planta INT,
    cliente VARCHAR(255),
    fecha_inicio DATE,
    fecha_fin DATE,
    precio_MWh DECIMAL(10,2),
    FOREIGN KEY (codigo_planta) REFERENCES Planta(codigo_planta)
);

-- 2. Aquí van a ir las Primeary Keys por que bueno, no se, me gusta tenerlo todo ordenado. --

-- Clave primaria de Planta
ALTER TABLE Planta
ADD CONSTRAINT pk_planta PRIMARY KEY (codigo_planta);

-- Clave primaria de Equipo
ALTER TABLE Equipo
ADD CONSTRAINT pk_equipo PRIMARY KEY (numero_serie);

-- Clave primaria de Estacion
ALTER TABLE Estacion
ADD CONSTRAINT pk_estacion PRIMARY KEY (codigo_estacion);

-- Clave primaria de Lectura
ALTER TABLE Lectura
MODIFY COLUMN id_lectura INT NOT NULL AUTO_INCREMENT,
ADD CONSTRAINT pk_lectura PRIMARY KEY (id_lectura);

-- Clave primaria de Produccion
ALTER TABLE Produccion
MODIFY COLUMN id_produccion INT NOT NULL AUTO_INCREMENT,
ADD CONSTRAINT pk_produccion PRIMARY KEY (id_produccion);

-- Clave primaria de Incidencia
ALTER TABLE Incidencia
ADD CONSTRAINT pk_incidencia PRIMARY KEY (codigo_incidencia);

-- 3. Creación de las foreign Keys, todas las foreign keys se pondran en este apartado. --

-- Relación: Equipo pertenece a una Planta
ALTER TABLE Equipo
ADD CONSTRAINT fk_equipo_planta
FOREIGN KEY (codigo_planta)
REFERENCES Planta(codigo_planta)
ON DELETE CASCADE;

-- Relación: Lectura pertenece a una Estación
ALTER TABLE Lectura
ADD CONSTRAINT fk_lectura_estacion
FOREIGN KEY (codigo_estacion)
REFERENCES Estacion(codigo_estacion)
ON DELETE CASCADE;

-- Relación: Producción pertenece a una Planta
ALTER TABLE Produccion
ADD CONSTRAINT fk_produccion_planta
FOREIGN KEY (codigo_planta)
REFERENCES Planta(codigo_planta)
ON DELETE CASCADE;

-- Relación: Incidencia pertenece a una Planta
ALTER TABLE Incidencia
ADD CONSTRAINT fk_incidencia_planta
FOREIGN KEY (codigo_planta)
REFERENCES Planta(codigo_planta)
ON DELETE CASCADE;

-- 4. Creación de las index. --

-- Índice para búsquedas por fecha en producción
CREATE INDEX idx_fecha_produccion 
ON Produccion(fecha);

-- Índice compuesto para búsquedas por planta y fecha
CREATE INDEX idx_planta_fecha 
ON Produccion(codigo_planta, fecha);

-- Índice único para evitar duplicados en equipos
CREATE UNIQUE INDEX idx_numero_serie 
ON Equipo(numero_serie);

-- Índice FULLTEXT para buscar en descripciones
ALTER TABLE Incidencia 
ADD FULLTEXT(descripcion);

-- 5. Consultas de ejemplo.

-- Consulta básica con WHERE
-- Muestra plantas con capacidad mayor a 50 MW
SELECT * FROM Planta
WHERE capacidad_instalada_MW > 50;

-- Consulta con AND
-- Equipos activos con buena eficiencia (ejemplo)
SELECT * FROM Equipo
WHERE marca = 'Siemens' AND modelo = 'X100';

-- Consulta con BETWEEN
-- Producción en un rango de fechas
SELECT * FROM Produccion
WHERE fecha BETWEEN '2025-01-01' AND '2026-12-31';

-- Consulta con IN
SELECT * FROM Planta
WHERE tipo IN ('solar', 'eolica');

-- Consulta con LIKE
SELECT * FROM Incidencia
WHERE descripcion LIKE '%fallo%';

-- 6. Consultas de no se que así avanzadas.

-- Suma de energía generada por planta
SELECT codigo_planta, SUM(energia_generada) AS total_energia
FROM Produccion
GROUP BY codigo_planta;

-- Ordenar resultados
SELECT * FROM Produccion
ORDER BY fecha DESC;

-- Limitar resultados
SELECT * FROM Produccion
LIMIT 5;

-- NULL
SELECT * FROM Incidencia
WHERE descripcion IS NULL;

-- Agrupación por año
SELECT YEAR(fecha) AS año, SUM(energia_generada)
FROM Produccion
GROUP BY YEAR(fecha);

-- 7. Insert de ejemplo para plantas de energía
INSERT INTO Planta (codigo_planta, nombre, tipo, ubicacion, capacidad_instalada_MW, estado_operativo)
VALUES
(1, 'Solar Alpha', 'solar', 'Desierto de Atacama', 75.5, 'Operativa'),
(2, 'Eólica Beta', 'eolica', 'Costa Atlántica', 120.0, 'Operativa'),
(3, 'Hidro Gamma', 'hidroeléctrica', 'Río Amazonas', 200.0, 'En Mantenimiento');

-- Insert de ejemplo para equipos
INSERT INTO Equipo (numero_serie, tipo, marca, modelo, codigo_planta)
VALUES
(1001, 'Panel Solar', 'SunPower', 'SPX-300', 1),
(1002, 'Panel Solar', 'LG', 'LG-400', 1),
(2001, 'Aerogenerador', 'Siemens', 'SG-5.0', 2),
(2002, 'Aerogenerador', 'GE', 'GE-3.6', 2),
(3001, 'Turbina Hidráulica', 'Andritz', 'AT-250', 3);

-- Insert de ejemplo para estaciones meteorológicas
INSERT INTO Estacion (codigo_estacion, ubicacion)
VALUES
(1, 'Desierto de Atacama'),
(2, 'Costa Atlántica'),
(3, 'Río Amazonas');

-- Insert de ejemplo para lecturas meteorológicas
INSERT INTO Lectura (codigo_estacion, fecha, tipo, valor)
VALUES
(1, '2026-03-25 08:00:00', 'radiacion solar', 950.5),
(1, '2026-03-25 08:00:00', 'temperatura', 35.2),
(2, '2026-03-25 09:00:00', 'velocidad viento', 12.3),
(2, '2026-03-25 09:00:00', 'temperatura', 28.7),
(3, '2026-03-25 10:00:00', 'precipitacion', 15.0);

-- Insert de ejemplo para producción energética
INSERT INTO Produccion (codigo_planta, fecha, energia_generada)
VALUES
(1, '2026-03-25', 1800.5),
(1, '2026-03-26', 1750.2),
(2, '2026-03-25', 4200.0),
(3, '2026-03-25', 5500.0);

-- Insert de ejemplo para incidencias
INSERT INTO Incidencia (codigo_incidencia, codigo_planta, descripcion)
VALUES
(1, 1, 'Fallo menor en inversor solar, reparado el mismo día'),
(2, 2, 'Aerogenerador detenido por mantenimiento programado'),
(3, 3, 'Nivel de agua bajo, producción afectada');

-- 7. Poner las "Stored Procedures" aquì--

-- Stored Procedure de RegistrarPlantaGeneración | Planta

DELIMITER //

CREATE PROCEDURE RegistrarPlantaGeneracion(
    IN p_codigo INT,
    IN p_nombre VARCHAR(255),
    IN p_tipo VARCHAR(255),
    IN p_ubicacion VARCHAR(255),
    IN p_capacidad DECIMAL(10,2),
    IN p_estado VARCHAR(255)
)
BEGIN
    INSERT INTO Planta
    VALUES (p_codigo, p_nombre, p_tipo, p_ubicacion, p_capacidad, p_estado);
END //

DELIMITER ;

-- Stored procedure de ProgramarMantenimientoEquipos | Incidencia

DELIMITER //

CREATE PROCEDURE ProgramarMantenimientoEquipos(
    IN p_codigo_incidencia INT,
    IN p_codigo_planta INT,
    IN p_descripcion TEXT
)
BEGIN
    INSERT INTO Incidencia
    VALUES (p_codigo_incidencia, p_codigo_planta, p_descripcion);
END //

DELIMITER ;

-- Stored procedure de RegistrarProduccionEnergetica | Produccion

DELIMITER //

CREATE PROCEDURE RegistrarProduccionEnergetica(
    IN p_codigo_planta INT,
    IN p_fecha DATE,
    IN p_energia DECIMAL(10,2)
)
BEGIN
    INSERT INTO Produccion (codigo_planta, fecha, energia_generada)
    VALUES (p_codigo_planta, p_fecha, p_energia);
END //

DELIMITER ;

-- Stored procedure de CrearContratoVentaEnergia | Contrato

DELIMITER //

CREATE PROCEDURE CrearContratoVentaEnergia(
    IN p_codigo_planta INT,
    IN p_cliente VARCHAR(255),
    IN p_fecha_inicio DATE,
    IN p_fecha_fin DATE,
    IN p_precio DECIMAL(10,2)
)
BEGIN
    INSERT INTO Contrato (codigo_planta, cliente, fecha_inicio, fecha_fin, precio_MWh)
    VALUES (p_codigo_planta, p_cliente, p_fecha_inicio, p_fecha_fin, p_precio);
END //

DELIMITER ;

-- Stored Procedure RegistrarLecturasMeteorologicas | lectura

DELIMITER //

CREATE PROCEDURE RegistrarLecturasMeteorologicas(
    IN p_codigo_estacion INT,
    IN p_fecha DATETIME,
    IN p_tipo VARCHAR(50),
    IN p_valor DECIMAL(10,2)
)
BEGIN
    INSERT INTO Lectura (codigo_estacion, fecha, tipo, valor)
    VALUES (p_codigo_estacion, p_fecha, p_tipo, p_valor);
END //

DELIMITER ;

-- 8. Aquì se pondran las "views".--

-- 1. Producción total por planta

CREATE VIEW VistaProduccionPorPlanta AS
SELECT 
    p.nombre,
    p.tipo,
    SUM(pr.energia_generada) AS total_energia
FROM Planta p
JOIN Produccion pr ON p.codigo_planta = pr.codigo_planta
GROUP BY p.nombre, p.tipo;

-- 2. Equipos por planta

CREATE VIEW VistaEquiposPorPlanta AS
SELECT 
    p.nombre AS planta,
    e.tipo AS equipo,
    e.marca,
    e.modelo
FROM Equipo e
JOIN Planta p ON e.codigo_planta = p.codigo_planta;

-- 3. Incidencias por planta

CREATE VIEW VistaIncidencias AS
SELECT 
    p.nombre,
    i.descripcion
FROM Incidencia i
JOIN Planta p ON i.codigo_planta = p.codigo_planta;

-- 4. Lecturas meteorológicas recientes (últimos 7 días)

CREATE VIEW VistaLecturasRecientes AS
SELECT 
    e.ubicacion,
    l.fecha,
    l.tipo,
    l.valor
FROM Lectura l
JOIN Estacion e ON l.codigo_estacion = e.codigo_estacion
WHERE l.fecha >= NOW() - INTERVAL 7 DAY;

-- 5. Producción ordenada por fecha

CREATE VIEW VistaProduccionOrdenada AS
SELECT *
FROM Produccion
ORDER BY fecha DESC;

-- 9. Creando Events para la tabla de Green Powed-- 

-- Comando que hace que funcionen los Events.--

SET GLOBAL event_scheduler = ON;

-- Event de la tabla de EVT_VerificarRendimientoEquipos. 

DELIMITER // 
CREATE EVENT EVT_VerificarRendimientoEquipos 
ON SCHEDULE EVERY 1 DAY 
DO 
BEGIN 
  INSERT INTO Incidencia (codigo_planta, descripcion) 
  SELECT codigo_planta, 'Posible bajo rendimiento detectado automáticamente' 
  FROM Produccion 
  WHERE energia_generada < 2000; 
  END// 
  
DELIMITER ; 

-- Event de la tabla de EVT_GenerarPronosticosProduccion 

DELIMITER // 
CREATE EVENT EVT_GenerarPronosticosProduccion 
ON SCHEDULE EVERY 1 DAY 
DO 
BEGIN 
  INSERT INTO Incidencia (codigo_planta, descripcion) 
  SELECT codigo_planta, 
  CONCAT('Pronóstico generado: ', 
  AVG(energia_generada) * 1.1) 
  FROM Produccion 
  GROUP BY codigo_planta; 
END// 

DELIMITER ;

-- 3. EVT_ActualizarFactorCapacidad

DELIMITER //

CREATE EVENT EVT_ActualizarFactorCapacidad
ON SCHEDULE EVERY 1 MONTH
DO
BEGIN
    INSERT INTO Incidencia (codigo_planta, descripcion)
    SELECT 
        p.codigo_planta,
        CONCAT(
            'Factor de capacidad actualizado: ',
            ROUND(SUM(pr.energia_generada) / (p.capacidad_instalada_MW * 24 * 30), 2)
        )
    FROM Planta p
    JOIN Produccion pr ON p.codigo_planta = pr.codigo_planta
    GROUP BY p.codigo_planta;
END//

-- 4. EVT_MonitorearCumplimientoContratos

DELIMITER //

CREATE EVENT EVT_MonitorearCumplimientoContratos
ON SCHEDULE EVERY 1 DAY
DO
BEGIN
    INSERT INTO Incidencia (codigo_planta, descripcion)
    SELECT 
        c.codigo_planta,
        'Contrato próximo a vencer o vencido'
    FROM Contrato c
    WHERE c.fecha_fin <= CURDATE() + INTERVAL 7 DAY;
END//

-- 5. EVT_ProgramarMantenimientosPreventivos

DELIMITER //

CREATE EVENT EVT_ProgramarMantenimientosPreventivos
ON SCHEDULE EVERY 1 MONTH
DO
BEGIN
    INSERT INTO Incidencia (codigo_planta, descripcion)
    SELECT 
        codigo_planta,
        'Mantenimiento preventivo programado automáticamente'
    FROM Produccion
    GROUP BY codigo_planta
    HAVING SUM(energia_generada) > 50000;
END//

DELIMITER ;
