# KNOWLEDGE COMMANDS
Personal Repo to learn and remember
### Oracle
SQLPLUS
-- ENTRY CMD
-- sqlplus sys/tu_contraseña_como_sys as sysdba

      SQLPLUS SYS/923989388 AS SYSDBA

--CREATE USER
-- CREATE USER tu_usuario IDENTIFIED BY tu_contraseña;

    CREATE USER LocutorBD IDENTIFIED BY 923989388;

--PRIVILEGES
-- Concede todos los privilegios al usuario
-- GRANT ALL PRIVILEGES TO tu_usuario;

    GRANT ALL PRIVILEGES TO LocutorBD;
-- Vistas 
-- GRANT CREATE VIEW TO tu_usuario;

    GRANT CREATE VIEW TO LocutorBD;

--Permiso para conectar con java

    GRANT CONNECT, RESOURCE, DBA TO LocutorBD;

-- CONNECT

    CONN LocutorBD/923989388;

-- SERVER

    SET SERVEROUTPUT ON

--Cambiar Puerto de la base de datos

    EXEC DBMS_XDB.SETHTTPPORT(9090); -- Por default esta en el 8080

--Revisar puerto de ejecucion

    SELECT DBMS_XDB.GETHTTPPORT FROM DUAL;

-- Setear la visualizacion tabla

    SET LINESIZE 200;
    SELECT * FROM cliente; -- ejemplo para ver como queda seteado la visualización
-- Ver todas las tablas creadas por el usuario

    SELECT table_name FROM user_tables;
-- Ver los errores en un trigger

    SELECT * FROM USER_ERRORS WHERE NAME = 'UpdateTablaClienteB';
-- ver Columnas de una tabla

    SELECT COLUMN_NAME
    FROM USER_TAB_COLUMNS
    WHERE TABLE_NAME = 'prestamo';
-- ver errores

    SHOW ERRORS
-- eliminar atributo

    ALTER TABLE PRESTAMO DROP COLUMN usuario;
-- deshabilitar el reemplazo de variables

    SET DEFINE OFF; -- importante si para la data a insertar hay simbolos especiales por ejemplo '&'
-- Desabilitar todas las relaciones foraneas

    DECLARE
      v_sql VARCHAR2(1000);
    BEGIN
      FOR c IN (SELECT * FROM user_constraints WHERE constraint_type = 'R') LOOP
        v_sql := 'ALTER TABLE ' || c.table_name || ' DISABLE CONSTRAINT ' || c.constraint_name;
        EXECUTE IMMEDIATE v_sql;
      END LOOP;
    END;
    /
    
-- Desabilitar una relacion

    ALTER TABLE tabla
    DISABLE CONSTRAINT nombre_relacion;
    
-- Forzar elimimacion de una tabla

    drop table tipo_telefono cascade constraints;
    
-- ver todas las secuencias

    SELECT SEQUENCE_NAME
    FROM USER_SEQUENCES;
    
--Eliminar secuencia

    drop sequence seq_audio;

-- Ver tablas y cuantos valores tiene la tabla

    DECLARE
       table_name VARCHAR2(30);
       query_str VARCHAR2(100);
       count_rows NUMBER;
    BEGIN
       -- Obtener todas las tablas del usuario actual
       FOR tables IN (SELECT table_name FROM user_tables) LOOP
          table_name := tables.table_name;
          query_str := 'SELECT COUNT(*) FROM ' || table_name;
    
          -- Ejecutar la consulta para obtener el número de filas en cada tabla
          EXECUTE IMMEDIATE query_str INTO count_rows;
    
          -- Imprimir información sobre la tabla
          DBMS_OUTPUT.PUT_LINE('Tabla: ' || table_name);
          DBMS_OUTPUT.PUT_LINE('Número de filas: ' || count_rows);
          
          -- Si deseas imprimir los valores de las filas, puedes agregar un bucle similar aquí
    
          DBMS_OUTPUT.PUT_LINE('---');
       END LOOP;
    END;
    /
    
-- borrar todos los datos de las tablas y reiniciar las secuencias:

    DECLARE
       table_name VARCHAR2(30);
       query_str VARCHAR2(500);
       sequence_exists NUMBER;
    BEGIN
       FOR tables IN (SELECT table_name FROM user_tables) LOOP
          table_name := tables.table_name;
          
          -- Eliminar todos los valores de la tabla
          query_str := 'DELETE FROM ' || table_name;
          EXECUTE IMMEDIATE query_str;
    
          -- Verificar si la secuencia existe
          SELECT COUNT(*) INTO sequence_exists FROM user_sequences WHERE sequence_name = table_name || '_SEQ';
    
          IF sequence_exists > 0 THEN
             -- Reiniciar la secuencia a 1
             query_str := 'BEGIN EXECUTE IMMEDIATE ''ALTER SEQUENCE ' || table_name || '_SEQ INCREMENT BY 1 MINVALUE 0 CYCLE''; EXECUTE IMMEDIATE ''SELECT ' || table_name || '_SEQ.NEXTVAL FROM DUAL''; EXECUTE IMMEDIATE ''ALTER SEQUENCE ' || table_name || '_SEQ INCREMENT BY 1 MINVALUE 0 CYCLE''; END;';
             EXECUTE IMMEDIATE query_str;
          END IF;
       END LOOP;
    END;
    /

