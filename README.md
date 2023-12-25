# KNOWLEDGE COMMANDS
Para las cuentas SYS, SYSTEM, PDBADMIN se utiliza la contraseña.
Personal Repo to learn and remember
### Oracle

SQLPLUS

<ul>
      
<li> ENTRY CMD </li>

Command: 
      
      sqlplus sys/tu_contraseña_como_sys as sysdba
      
Example:

      SQLPLUS SYS/923989388 AS SYSDBA
      
<li> CREATE USER </li>

Command: 
      
      CREATE USER tu_usuario IDENTIFIED BY tu_contraseña;
      
Example:

    CREATE USER LocutorBD IDENTIFIED BY 923989388;
    
<li> PRIVILEGES </li>

-- Concede todos los privilegios al usuario

Command: 

      GRANT ALL PRIVILEGES TO tu_usuario;
      
Example:

      GRANT ALL PRIVILEGES TO LocutorBD;
      
<li> Vistas </li>

Command: 

      GRANT CREATE VIEW TO tu_usuario;

 Example:
 
       GRANT CREATE VIEW TO LocutorBD;

--Permiso para conectar con java

    GRANT CONNECT, RESOURCE, DBA TO LocutorBD;

<li> CONNECT </li>

    CONN LocutorBD/923989388;

<li> SERVER </li>

    SET SERVEROUTPUT ON

<li> Cambiar Puerto de la base de datos </li>

    EXEC DBMS_XDB.SETHTTPPORT(9090); -- Por default esta en el 8080

<li> Revisar puerto de ejecucion </li>

    SELECT DBMS_XDB.GETHTTPPORT FROM DUAL;

<li> Setear la visualizacion tabla </li>

    SET LINESIZE 200;
    SELECT * FROM cliente; -- ejemplo para ver como queda seteado la visualización
    
<li> Ver todas las tablas creadas por el usuario </li>

    SELECT table_name FROM user_tables;
    
<li> Ver los errores en un trigger </li>

    SELECT * FROM USER_ERRORS WHERE NAME = 'UpdateTablaClienteB';
    
<li> Ver Columnas de una tabla </li>

    SELECT COLUMN_NAME
    FROM USER_TAB_COLUMNS
    WHERE TABLE_NAME = 'prestamo';
    
<li> Ver errores </li>

    SHOW ERRORS
    
<li> Eliminar atributo </li>

    ALTER TABLE PRESTAMO DROP COLUMN usuario;
    
<li> Deshabilitar el reemplazo de variables </li>

    SET DEFINE OFF; -- importante si para la data a insertar hay simbolos especiales por ejemplo '&'
    
<li> Desabilitar todas las relaciones foraneas </li>

    DECLARE
      v_sql VARCHAR2(1000);
    BEGIN
      FOR c IN (SELECT * FROM user_constraints WHERE constraint_type = 'R') LOOP
        v_sql := 'ALTER TABLE ' || c.table_name || ' DISABLE CONSTRAINT ' || c.constraint_name;
        EXECUTE IMMEDIATE v_sql;
      END LOOP;
    END;
    /
    
<li> Desabilitar una relacion </li>

    ALTER TABLE tabla
    DISABLE CONSTRAINT nombre_relacion;
    
<li> Forzar elimimacion de una tabla </li>  

    drop table tipo_telefono cascade constraints;
    
<li> Ver todas las secuencias </li>

    SELECT SEQUENCE_NAME
    FROM USER_SEQUENCES;
    
<li> Eliminar secuencia </li>

    drop sequence seq_audio;

<li> Ver tablas y cuantos valores tiene la tabla </li>

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
    
<li> Borrar todos los datos de las tablas y reiniciar las secuencias </li>

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
    
</ul>
