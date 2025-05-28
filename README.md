# Trigger

## ¿Qué es un Trigger?
Un Trigger (o disparador en español) es un tipo especial de procedimiento almacenado que se ejecuta automáticamente en respuesta a ciertos eventos en una tabla de la base de datos. Estos eventos suelen ser operaciones de lenguaje de manipulación de datos (DML) como INSERT, UPDATE o DELETE. Piensa en él como un centinela que está siempre atento y, cuando detecta una acción específica, se activa para realizar una tarea predefinida.

## ¿Para que sirve un Trigger?
Sirven para automatizar acciones y mantener la integridad y la consistencia de los datos en una base de datos. Sus usos principales incluyen:

Aplicar reglas de negocio complejas: Pueden imponer reglas que no pueden ser manejadas por restricciones de integridad estándar (como CHECK o FOREIGN KEY).
Auditoría de datos: Registrar quién, cuándo y qué cambios se realizaron en los datos, por ejemplo, en una tabla de historial o log.
Mantenimiento de la integridad referencial: Asegurar que las relaciones entre tablas se mantengan correctas, incluso en cascada.
Sincronización de datos: Actualizar automáticamente datos en otras tablas cuando hay un cambio en la tabla principal.
Generación de valores automáticos: Por ejemplo, generar un ID único antes de insertar un registro.
Notificaciones: Aunque no es su función principal, indirectamente pueden desencadenar eventos que generen notificaciones.

## ¿Cuándo se puede usar un Trigger?
Cuando se necesite que una acción se ejecute de forma automática e incondicional cada vez que ocurra un evento específico en una tabla. Algunos escenarios comunes son:

* Cuando se necesite validar datos de una manera más compleja que una restricción CHECK simple.
* Cuando se quiera mantener un registro de todas las modificaciones realizadas en una tabla sensible.
* Cuando la actualización o eliminación de un registro en una tabla debe propagar cambios a otras tablas de forma automática.
* Cuando la lógica de negocio requiere que ciertas operaciones se realicen antes o después de una inserción, actualización o eliminación.


## Estructura de un Trigger
La estructura básica puede variar ligeramente entre diferentes sistemas de gestión de bases de datos (DBMS) como MySQL, PostgreSQL, SQL Server u Oracle, pero generalmente consta de los siguientes elementos:

```sql
CREATE [OR REPLACE] TRIGGER nombre_del_trigger
[BEFORE | AFTER | INSTEAD OF] [INSERT | UPDATE | DELETE] ON nombre_de_la_tabla
[FOR EACH ROW | FOR EACH STATEMENT]
[WHEN (condicion)]
BEGIN
    -- Cuerpo del Trigger (sentencias SQL a ejecutar)
END;
/
```

## Tipos de Trigger
* El momento de ejecución:

***BEFORE Triggers:*** Se ejecutan antes de que la operación DML altere los datos. Útiles para validaciones, modificar los datos que se van a insertar/actualizar.

***AFTER Triggers:*** Se ejecutan después de que la operación DML ha alterado los datos. Útiles para auditorías, actualizar tablas relacionadas.

***INSTEAD OF Triggers:*** Se usan principalmente con vistas que no se pueden modificar directamente. En lugar de ejecutar la operación DML en la vista, se ejecuta el código del Trigger.

* El nivel de granularidad:

***FOR EACH ROW (Row-level triggers):*** Se disparan una vez por cada fila afectada por la sentencia DML. Permiten acceder a los valores antiguos (OLD) y nuevos (NEW) de las filas.

***FOR EACH STATEMENT (Statement-level triggers):*** Se disparan una vez por la sentencia DML, sin importar cuántas filas afecte. No tienen acceso directo a los valores de las filas individuales.

## ¿Cuándo ejecuta su accion un Trigger?
Un Trigger ejecuta su acción automáticamente cuando se produce el evento DML (INSERT, UPDATE, DELETE) en la tabla asociada, y si se cumplen las condiciones BEFORE, AFTER o INSTEAD OF y cualquier cláusula WHEN que se haya especificado.

Por ejemplo:

* Un **BEFORE INSERT Trigger** se ejecutará justo antes de que una nueva fila sea insertada.
* Un **AFTER UPDATE Trigger** se ejecutará una vez que las filas hayan sido actualizadas.
* Un **BEFORE DELETE Trigger** se ejecutará antes de que una fila sea eliminada.

## Ejemplo de un Trigger
* Este Trigger es acerca de registrar los cambios en una tabla de Empleados en una tabla de auditoría Historial_Empleados.

```
CREATE TABLE Empleados (
    id_empleado INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100),
    salario DECIMAL(10, 2),
    fecha_contratacion DATE
);

CREATE TABLE Historial_Empleados (
    id_historial INT PRIMARY KEY AUTO_INCREMENT,
    id_empleado INT,
    campo_modificado VARCHAR(50),
    valor_anterior VARCHAR(100),
    valor_nuevo VARCHAR(100),
    fecha_cambio DATETIME,
    tipo_operacion VARCHAR(10)
);
```

* Ejemplo para MySQL
```
DELIMITER //

CREATE TRIGGER tr_auditar_empleados
AFTER UPDATE ON Empleados
FOR EACH ROW
BEGIN
    IF OLD.nombre <> NEW.nombre THEN
        INSERT INTO Historial_Empleados (id_empleado, campo_modificado, valor_anterior, valor_nuevo, fecha_cambio, tipo_operacion)
        VALUES (OLD.id_empleado, 'nombre', OLD.nombre, NEW.nombre, NOW(), 'UPDATE');
    END IF;

    IF OLD.salario <> NEW.salario THEN
        INSERT INTO Historial_Empleados (id_empleado, campo_modificado, valor_anterior, valor_nuevo, fecha_cambio, tipo_operacion)
        VALUES (OLD.id_empleado, 'salario', OLD.salario, NEW.salario, NOW(), 'UPDATE');
    END IF;
END;
//

DELIMITER ;
```
* Ejemplo para actualizar a un empleado
```
INSERT INTO Empleados (nombre, salario, fecha_contratacion) VALUES ('Juan Perez', 50000.00, '2020-01-15');
UPDATE Empleados SET salario = 55000.00 WHERE nombre = 'Juan Perez';
UPDATE Empleados SET nombre = 'Juan Carlos Perez' WHERE nombre = 'Juan Perez';
```

## Trigger Marketing
refiere a una estrategia de marketing automatizada donde las comunicaciones o acciones de marketing se envían a los clientes en respuesta a un evento o comportamiento específico que han realizado. En este contexto, un "trigger" es ese evento o comportamiento que desencadena la acción de marketing.

Ejemplos de triggers en marketing:

* ***Abandono del carrito de compra:*** Envío de un correo electrónico recordatorio.
* ***Visita a una página de producto específica:*** Envío de información adicional sobre ese producto.
* ***Cumpleaños del cliente:*** Envío de una oferta especial o descuento.
* ***Inactividad por un tiempo determinado:*** Envío de un correo para reenganchar al cliente.
* ***Compra de un producto:*** Envío de un correo de seguimiento, recomendaciones de productos relacionados o una encuesta de satisfacción.

Su objetivo es enviar el mensaje correcto, a la persona correcta, en el momento correcto, lo que suele resultar en mayores tasas de conversión y satisfacción del cliente.

## ¿Cómo hacer un Trigger?
Implica definir la sentencia DDL (CREATE TRIGGER) en tu sistema de gestión de bases de datos. Los pasos generales son:

1. ***Identificar la tabla:*** ¿En qué tabla se quiere que actúe el Trigger?
2. ***Definir el evento:*** ¿Qué acción DML (INSERT, UPDATE, DELETE) debe activarlo?
3. ***Determinar el momento:*** ¿Debe ejecutarse BEFORE o AFTER del evento? ¿O INSTEAD OF en el caso de vistas?
4. ***Establecer la granularidad:*** ¿FOR EACH ROW (para cada fila afectada) o FOR EACH STATEMENT (una vez por la sentencia)?
5. ***Especificar la lógica:*** Escribir el código SQL (o PL/SQL, T-SQL, etc., dependiendo del DBMS) que se ejecutará cuando el Trigger se dispare. Se pueden acceder a los valores OLD y NEW de las filas para Triggers a nivel de fila.
6. ***Crear el Trigger:*** Ejecutar la sentencia CREATE TRIGGER en tu herramienta de gestión de base de datos.

## Caracteristicas de un Trigger
* ***Automatismo:*** Se ejecutan de forma automática sin intervención manual.
* ***Transparencia:*** Son invisibles para las aplicaciones cliente; estas solo interactúan con las operaciones DML, y el Trigger se encarga de la lógica subyacente.
* ***Integridad de datos:*** Ayudan a mantener la consistencia y validez de los datos.
* ***Event-driven:*** Se activan en respuesta a eventos específicos de la base de datos.
* ***Programabilidad:*** Permiten implementar lógica de negocio compleja que no puede ser manejada por restricciones de base de datos simples.
* ***Acceso a datos antiguos y nuevos (en Triggers de fila):*** La capacidad de acceder a los valores de los datos antes y después de la modificación (OLD y NEW).
* ***Dependencia:*** Si la tabla o los objetos a los que hace referencia el Trigger son eliminados,
el Trigger puede volverse inválido.

## Desventajas de un Trigger
* ***Dificultad de depuración:*** Pueden ser complejos de depurar, ya que su ejecución es implícita y automática, lo que puede dificultar el rastreo de problemas.
* ***Rendimiento:*** Un Trigger mal diseñado o que realiza operaciones costosas puede afectar negativamente el rendimiento de la base de datos, especialmente en sistemas con alto volumen de transacciones.
* ***Dependencia oculta:*** La lógica de negocio puede quedar "escondida" dentro de los Triggers en lugar de estar en el código de la aplicación, lo que puede hacer que el sistema sea más difícil de entender y mantener.
* ***Cascada de Triggers:*** Un Trigger puede disparar otra operación DML, que a su vez puede disparar otro Trigger, creando una cadena compleja que es difícil de predecir y controlar.
* ***Portabilidad limitada:*** La sintaxis y el comportamiento de los Triggers pueden variar significativamente entre diferentes sistemas de gestión de bases de datos, lo que dificulta la portabilidad de código.
* ***Sobrecarga en actualizaciones masivas:*** Si se realiza una operación DML que afecta a millones de filas, un Trigger a nivel de fila se ejecutará millones de veces, lo que puede ser ineficiente.

## Sustituto de los Trigger en otras bases de datos.
Aunque el concepto de "Trigger" es bastante universal en los sistemas de bases de datos relacionales, el término específico y algunas de sus implementaciones pueden variar. Sin embargo, las funcionalidades que un Trigger provee a menudo se logran a través de:

* ***Procedimientos Almacenados y Funciones:*** Aunque no se ejecutan automáticamente por eventos DML, pueden contener lógica de negocio compleja y se pueden llamar explícitamente desde el código de la aplicación.
* ***Restricciones de Integridad (CHECK, FOREIGN KEY, UNIQUE, NOT NULL):*** Para reglas de validación de datos y mantenimiento de relaciones simples, son más eficientes y declarativas que los Triggers.
* ***Lógica en la capa de aplicación:*** Muchos desarrolladores prefieren implementar la lógica de negocio en el código de la aplicación (Java, Python, C#, etc.) en lugar de la base de datos. Esto puede mejorar la portabilidad, la depuración y el escalado de la aplicación, pero requiere que toda la lógica se aplique consistentemente desde la aplicación.
* ***Sistemas de mensajería o colas (Message Queues):*** Para desacoplar la lógica de eventos. Por ejemplo, una aplicación inserta datos en una base de datos y luego publica un mensaje en una cola. Otros servicios pueden suscribirse a esa cola y reaccionar al evento.
* ***Change Data Capture (CDC):*** Son herramientas o características que rastrean y capturan los cambios en los datos de la base de datos, a menudo en tiempo real, para su posterior procesamiento o replicación. Esto es una alternativa más escalable para auditorías y sincronización de datos en entornos distribuidos.
* ***Servicios de nube específicos:*** Algunas plataformas en la nube ofrecen servicios que reaccionan a eventos de bases de datos (como AWS Lambda con eventos de DynamoDB o RDS) para ejecutar funciones sin servidor.
