# Tarea12SXE  

# Tarea 12 - Sistemas de Xestión Empresarial  

Este repositorio contiene la resolución de la Tarea 12 de la asignatura Sistemas de Xestión Empresarial utilizando Odoo y PostgreSQL.  

## 📌 Descripción  

La tarea consiste en realizar varias consultas y modificaciones sobre la base de datos de Odoo. Se ha realizado una instalación limpia con datos de   demostración y se han instalado los módulos de Facturación y Contactos.  

Cada apartado de la tarea tiene su propio commit con el script SQL correspondiente y una captura de pantalla que demuestra su ejecución.  

## 📂 Estructura del Repositorio  

sql/ → Contiene los scripts SQL de cada apartado.  

capturas/ → Contiene capturas de pantalla de la ejecución de las consultas.  

README.md → Este archivo con la explicación de la tarea.  

## ⚙️ Configuración de la Base de Datos  

Para ejecutar los comandos SQL, es necesario tener acceso a la base de datos de Odoo en PostgreSQL. Se recomienda el uso de PgAdmin, DBeaver o la   terminal con psql.  

## Conectarse a la Base de Datos en psql  

psql -U odoo -d nombre_de_tu_base_de_datos  

Reemplazar odoo con el usuario de PostgreSQL y nombre_de_tu_base_de_datos con el nombre de tu base de datos.  

## 🚀 Ejecución de los Ejercicios  

Cada ejercicio se encuentra en la carpeta sql/. Para ejecutarlos:  

1. Abrir PgAdmin o la terminal (psql).  

2. Seleccionar la base de datos de Odoo.  

3. Ejecutar el script SQL correspondiente:  

\\i sql/apartadoX.sql  # Reemplaza X por el número de apartado  

4. Verificar los resultados en la interfaz de Odoo o con una consulta SELECT.  

5. Adjuntar captura de pantalla antes y después de cada operación en la carpeta capturas/.  

## 📝 Consultas SQL y Capturas  

### 1️⃣ Crear la tabla EmpresasFCT  

CREATE TABLE EmpresasFCT (  
    idEmpresa SERIAL PRIMARY KEY,  
    nombre VARCHAR(40) NOT NULL,  
    useChatgpt BOOLEAN DEFAULT TRUE,  
    quiereAlumnos BOOLEAN,  
    numAlumnos INTEGER,  
    fechaContacto DATE  
);  
![Captura 1: Creación de la tabla](https://github.com/JavierP5/Tarea12SXE/blob/main/Captura%20desde%202025-02-10%2014-09-59.png)  

### 2️⃣ Insertar 5 registros en EmpresasFCT  

INSERT INTO EmpresasFCT (nombre, quiereAlumnos, numAlumnos, fechaContacto)  
VALUES   
    ('Empresa A', TRUE, 3, '2024-02-01'),  
    ('Empresa B', FALSE, 0, '2023-12-15'),  
    ('Empresa C', TRUE, 2, '2024-01-10'),  
    ('Empresa D', FALSE, 0, '2023-11-20'),  
    ('Empresa E', TRUE, 5, '2024-02-05');  

![Captura 2: Insertar 5 registros](https://github.com/JavierP5/Tarea12SXE/blob/main/Captura%20desde%202025-02-10%2014-10-40.png)  

### 3️⃣ Consultar todos los datos de EmpresasFCT ordenados por fecha  

SELECT * FROM EmpresasFCT ORDER BY fechaContacto DESC;  

![Captura 3: Consultar datos ordenados por fecha](https://github.com/JavierP5/Tarea12SXE/blob/main/Captura%20desde%202025-02-10%2014-11-01.png)  

### Apartado 4: Listado de contactos en Odoo de Tracy (código postal 95304)  

SELECT  
    res_partner.name AS Nombre,  
    res_partner.city AS Ciudad,  
    res_partner.zip AS CodigoPostal,  
    res_partner.company_name AS NombreComercial  
FROM res_partner  
WHERE res_partner.city = 'Tracy'   
  AND res_partner.zip = '95304'  
  AND res_partner.is_company = FALSE  
ORDER BY res_partner.company_name;  

### Apartado 5: Empresas proveedoras con reembolsos  

SELECT   
    rp.name AS NombreEmpresa,   
    am.name AS NumeroFactura,   
    am.invoice_date AS FechaFactura,   
    am.amount_total AS TotalConImpuestos,   
    am.amount_untaxed AS TotalSinImpuestos  
FROM account_move am  
JOIN res_partner rp ON am.partner_id = rp.id  
WHERE am.move_type = 'in_refund'  
ORDER BY am.invoice_date DESC;  

### Apartado 6: Empresas clientes con más de dos facturas de venta confirmadas  

SELECT   
    rp.name AS NombreEmpresa,   
    COUNT(am.id) AS NumeroFacturas,   
    SUM(am.amount_total) AS TotalConImpuestos,   
    SUM(am.amount_untaxed) AS TotalSinImpuestos  
FROM account_move am  
JOIN res_partner rp ON am.partner_id = rp.id  
WHERE am.move_type = 'out_invoice'  
  AND am.state = 'posted'  
GROUP BY rp.name  
HAVING COUNT(am.id) > 2;  

### Apartado 7: Actualizar correos electrónicos con nuevo dominio  

UPDATE res_partner  
SET email = REPLACE(email, '@bilbao.example.com', '@bilbao.bizkaia.neus')  
WHERE email LIKE '%@bilbao.example.com';  

### Apartado 8: Eliminar contactos de "Ready Mat" sin eliminar la empresa  

DELETE FROM res_partner   
WHERE company_name = 'Ready Mat'   
  AND is_company = FALSE;  


