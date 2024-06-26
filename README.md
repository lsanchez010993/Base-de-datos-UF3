# ENUNCIATS DE FUNCIONS

Exercici 1 - Fes una funció anomenada spData, tal que donada una data en format
MySQL ( AAAA-MM-DD ) ens retorni una cadena de caràcters en format DD-MM-AAAA
Exemple : SELECT spData('1988-12-01') => 01-12-1988
```mysql

DELIMITER //

CREATE FUNCTION spData(pFecha DATE) RETURNS VARCHAR(10)
BEGIN
    DECLARE vFecha VARCHAR(10);
    DECLARE vDia VARCHAR(2);
    DECLARE vMes VARCHAR(2);
    DECLARE vAny VARCHAR(4);
    
    SET vDia = DAY(pFecha);
    SET vMes = MONTH(pFecha);
    SET vAny = YEAR(pFecha);
    
    IF LENGTH(vDia) = 1 THEN
        SET vDia = CONCAT('0', vDia);
    END IF;
    
    IF LENGTH(vMes) = 1 THEN
        SET vMes = CONCAT('0', vMes);
    END IF;
    
    SET vFecha = CONCAT(vDia, '-', vMes, '-', vAny);
    
    RETURN vFecha;
END //

DELIMITER ;

SELECT spData('1988-2-01');

```
Exercici 2 - Fes una funció anomenada spPotencia, tal que donada una base i un
exponent, ens calculi la seva potència. Intenta no utilitzar la funció POW.
Exemple : SELECT spPotencia(2,3) => 8

```mysql

DELIMITER //

CREATE FUNCTION spPotencia(sBase INT, sExponent INT) RETURNS INT
BEGIN
    DECLARE vPotencia INT;
    
    SET vPotencia = POW(sBase, sExponent);
    
    RETURN vPotencia;
END //
DELIMITER ;
```
Exercici 3 - Fes una funció anomenada spIncrement que donat un codi d’empleat i un
% de increment, ens calculi el salari sumant aquest percentatge.
Per exemple, suposem que l’ empleat amb id_empleat = 124 té un salari de 1000
Exemple: SELECT spIncrement(124,10) obtindriem -> 1100
```mysql
DELIMITER //
CREATE FUNCTION spIncrement(empleatID INT, porcentaje INT) RETURNS INT
BEGIN
    DECLARE salariIncrementat INT;

    SET salariIncrementat = (SELECT salari
                                FROM empleats
                                WHERE empleat_id = empleatID) 
                            * porcentaje/100;

    RETURN salariIncrementat;
END//
DELIMITER ;



SELECT spIncrement (100,20)
```



Exercici 4 - Fes una funció anomenada spPringat, tal que li passem un codi de
departament, i ens torni el codi d’empleat que guanya menys d’aquell departament.

```mysql
DROP FUNCTION IF EXISTS pringat;
DELIMITER //
CREATE FUNCTION pringat(codiDepartament INT) RETURNS INT
BEGIN
    DECLARE idPringat INT;

    SELECT empleat_id INTO idPringat
    FROM empleats
    WHERE codiDepartament = departament_id
    ORDER BY salari ASC
    LIMIT 1;
      
    RETURN idPringat;
END//
DELIMITER ;
SELECT pringat (90)

```

Exercici 5 - Utilitzant la funció spPringat fes una consulta per obtenir de cada
departament, l’empleat pringat. Mostra el codi i nom del departament, i el codi d’empleat.

```mysql
SELECT DISTINCT d.departament_id, d.nom, pringat(e.departament_id)
FROM empleats e
INNER JOIN departaments d ON d.departament_id = e.departament_id;

```

Exercici 6 - Fes una funció anomenada spCategoria, tal que donat un codi d’empleat,
ens digui en quina categoria professional està. El criteri que volem seguir per determinar
la categoria professional és en funció dels anys que porta treballant a l’empresa:
Entre 0 i 1 anys -> Auxiliar
Entre 2 i 10 anys -> Oficial de Segona
Entre 11 i 20 Anys -> Oficial de Primera
Més de 20 anys -> Que es jubili!

```mysql
DROP FUNCTION IF EXISTS spCategoria;
DELIMITER //
CREATE FUNCTION spCategoria(codiEmpleat INT) RETURNS VARCHAR(50)
BEGIN
    DECLARE anys INT;
    DECLARE text_categoria VARCHAR(50);

    SELECT TIMESTAMPDIFF(YEAR, data_contractacio, CURRENT_DATE()) INTO anys
    FROM empleats
    WHERE empleat_id = codiEmpleat;

    IF anys >= 0 AND anys < 1 THEN
        SET text_categoria = 'Auxiliar';
    ELSEIF anys >= 2 AND anys <= 10 THEN
        SET text_categoria = 'Oficial de Segona';
    ELSEIF anys >= 11 AND anys <= 20 THEN
        SET text_categoria = 'Oficial de Primera';
    ELSE
        SET text_categoria = 'Que es jubili!';
    END IF;

    RETURN text_categoria;
END//
DELIMITER ;
 SELECT  spCategoria(empleat_id)
    FROM empleats

```

Exercici 7 - Fes una consulta utilitzant la funció anterior perquè mostri mostri de cada
empleat, el codi d’empleat, el nom, els anys treballats i la categoria professional a la que
pertany.
 ```mysql
 SELECT  empleat_id as codi_empleat, nom, TIMESTAMPDIFF(YEAR, data_contractacio, CURRENT_DATE()) as anys_treballats,
        spCategoria(empleat_id) as categoria_profesional
FROM empleats

 
 ```

Exercici 8 - Fes una funció anomenada spEdat, tal que donada una data per paràmetre
ens retorni l'edat d'una persona. Les dates posteriors a la data d'avui han de retornar 0.
```mysql
DROP FUNCTION IF EXISTS spEdat;
DELIMITER //
CREATE FUNCTION spEdat(data_naix DATE) RETURNS INT
BEGIN
    DECLARE anys INT;

    IF data_naix > CURRENT_DATE() THEN
        SET anys = 0;
    ELSE
        SET anys = TIMESTAMPDIFF(YEAR, data_naix, CURRENT_DATE());
    END IF;

    RETURN anys;
END//
DELIMITER ;

SELECT spEdat('1990-05-15') AS edad;

```

Exercici 9 - Fes una funció que ens retorni el número de directors (caps) diferents tenim.
```mysql
DROP FUNCTION IF EXISTS numCaps;
DELIMITER //
CREATE FUNCTION numCaps() RETURNS INT
BEGIN
    DECLARE caps INT;
    SELECT count (departament_id) into caps
    FROM empleats
    WHERE departament_id = (SELECT departament_id
                                FROM departaments
                                WHERE nom = 'Directiu');

   RETURN caps;
   END//

DELIMITER ;

SELECT numCaps()


```
Exercici 10 - Quina instrucció utilitzarem si volem veure el contingut de la funció
spPringat?
```mysql
SHOW CREATE FUNCTION pringat;

```

# ENUNCIATS DE PROCEDIMENTS
NOTA: En cada exercici, indica com ho faries per cridar el procediment.

Exercici 1 - Fes un procediment que permeti obtenir la data i hora del sistema i l’usuari
actual.
```mysql
DELIMITER //
DROP PROCEDURE IF EXISTS obtenirDataHora;
CREATE PROCEDURE obtenirDataHora() 
BEGIN
    SELECT NOW() AS 'data i hora',
    USER() as usuari_actual;
END//
DELIMITER ;



call obtenirDataHora ()



```
Exercici 2 - Fes un procediment que intercanvii el sou de dos empleats passats per
paràmetre.
```mysql

DROP PROCEDURE IF EXISTS intercambiarSueldo;
DELIMITER //
CREATE PROCEDURE intercambiarSueldo(IN empleadID1 INT, IN empleadID2 INT) 
BEGIN
    DECLARE salariEmpleat1 INT;
    DECLARE salariEmpleat2 INT;
    
    SELECT salari INTO salariEmpleat1 FROM empleats WHERE empleat_id = empleadID1;
    SELECT salari INTO salariEmpleat2 FROM empleats WHERE empleat_id = empleadID2;
    
    UPDATE empleats 
    SET salari = salariEmpleat2
    WHERE empleat_id = empleadID1;

    UPDATE empleats 
    SET salari = salariEmpleat1
    WHERE empleat_id = empleadID2;

END//
DELIMITER ;


```
Exercici 3 - Fes un procediment que donat dos Ids d'empleat assigni el codi de
departament del primer en el segon.
```mysql
DROP PROCEDURE IF EXISTS intercambiarDep;
DELIMITER //
CREATE PROCEDURE intercambiarDep(IN empleadID1 INT, IN empleadID2 INT) 
BEGIN
    DECLARE departamentEmpleat1 INT;
    DECLARE departamentEmpleat2 INT;
    
    SELECT departament_id INTO departamentEmpleat1 FROM empleats WHERE empleat_id = empleadID1;

    
    UPDATE empleats 
    SET departament_id = departamentEmpleat1
    WHERE empleat_id = empleadID2;

END//
DELIMITER ;




call intercambiarDep (100,103)


```
Exercici 4 - Fes un procediment que donat dos codis de departament assigni tots els
empleats del segon en el primer. Un cop executat el procediment el departament que
correspont en el segon paràmetre ha de quedar desert/sense cap empleat.
```mysql

DROP PROCEDURE IF EXISTS asignarEmpleadosDep;
DELIMITER //
CREATE PROCEDURE asignarEmpleadosDep(IN depID1 INT, IN depID2 INT) 
BEGIN
    
   UPDATE empleats 
   SET departament_id = depID2
   WHERE departament_id = depID1;

END//
DELIMITER ;

call asignarEmpleadosDep (90,60);

```
Exercici 5 - Fes un procediment per mostrar un llistat dels empleats. Volem veure el
id_empleat, nom_empleat, nom_departament i el nom de la localització del departament
```mysql

DROP PROCEDURE IF EXISTS listarEmpleados;
DELIMITER //
CREATE PROCEDURE listarEmpleados() 
BEGIN
    
   SELECT e.empleat_id, e.nom, d.nom, d.localitzacio_id from empleats e
   INNER JOIN departaments d ON d.departament_id = e.departament_id; 

END//
DELIMITER ;

call listarEmpleados ();

```
Exercici 6 - Fes un procediment que donat un codi d’empleat, ens doni la informació de
l’empleat ( agafa la informació que creguis rellevant)
```mysql

DROP PROCEDURE IF EXISTS mostrarInforEmpleado;
DELIMITER //
CREATE PROCEDURE mostrarInforEmpleado(IN empleatID INT) 
BEGIN
    
   SELECT e.empleat_id, e.nom, d.nom, d.localitzacio_id from empleats e
   INNER JOIN departaments d ON d.departament_id = e.departament_id 
   WHERE empleatID=e.empleat_id;

END//
DELIMITER ;

call mostrarInforEmpleado (101);

```
Exercici 7 - Volem fer un registre dels usuaris que entren al nostre sistema. Per fer-ho
primer caldrà crear una taula amb dos camps, un per guardar l’usuari i l’altre per guardar
la data i hora de l’accés.
```mysql

DROP PROCEDURE IF EXISTS registroUsuarios;
DELIMITER //
CREATE PROCEDURE registroUsuarios() 
BEGIN
    CREATE TABLE IF NOT EXISTS registro (
        id INT AUTO_INCREMENT PRIMARY KEY,
        nomUsuari VARCHAR(50),
        dataAcces DATETIME
    );

    INSERT INTO registro (nomUsuari, dataAcces)
    VALUES (USER(), CURRENT_TIMESTAMP());
END//
DELIMITER ;


CALL registroUsuarios();


SELECT * FROM registro

```
Exercici 8 - A continuació feu un procediment sense arguments, de manera que cada
vegada que el crideu, insereixi en aquesta taula l’usuari actual i la data i hora en que s’ha
executat el procediment.
```mysql

CALL registroUsuarios();

```
Exercici 9 - Fes un procediment que ens permeti afegir un nou departament però amb la
següent particularitat: En cas que la localització no existeixi a la taula localitzacions, ens
posarà un NULL en el camp id_localtizacio de la taula departaments. Al procediment li
hem de passar el codi de departament, el nom del departament i el codi de la localització.
```mysql

DROP PROCEDURE IF EXISTS afegirDep;
DELIMITER //
CREATE PROCEDURE afegirDep(in codiDep INT, in nomD VARCHAR(30), in localitzacio INT) 
BEGIN

    IF NOT EXISTS (SELECT departament_id FROM departaments WHERE departament_id = codiDep) THEN
        IF EXISTS (SELECT localitzacio_id FROM departaments WHERE localitzacio_id = localitzacio) THEN
            INSERT INTO departaments (departament_id, nom, localitzacio_id)
            VALUES (codiDep,nomD,localitzacio);
        ELSE  
            INSERT INTO departaments (departament_id, nom, localitzacio_id)
            VALUES (codiDep,nomD,NULL);

        END IF;
    END IF;
END//
DELIMITER ;


CALL afegirDep(11, 'Cosas Interesantes', 569);


SELECT * FROM departaments

```
Exercici 10 - Fes un procediment que donat un codi d’empleat, ens posi en paràmetres
de sortida el nom i el cognom. Indica com ho pots fer per comprovar si el procediment et
funciona.
```mysql



```
Exercici 11 - Fes un procediment que ens permeti modificar el nom i cognom d’un
empleat.
```mysql



```
Exercici 12 - Crea una taula d’auditoria anomenada logs_usuaris. Aquesta taula la
utilitzarem per monitoritzar algunes de les accions que fan els usuaris sobre les dades,
per exemple si actualitzen dades, eliminen registres.
La taula ha de tenir els següents camps:
usuari VARCHAR(100) Usuari que ha realitzat l’acció
data DATETIME Data-Hora en que s’ha realitzat l’acció
taula VARCHAR(50) Taula sobre la que es realitza l’acció
accio VARCHAR(20) “ELIMINAR”,”AFEGIR”,”MODIFICAR”,”INSERIR”
valor_pk VARCHAR(200) valor que identifica el registre que ha patit
l’acció
Fes un procediment amb nom spRegistrarLog que rebrà com a paràmetres el nom de la
taula, l’acció i el valor_pk.
Aquest procediment només cal que insereixi un registre en la taula logs_usuaris amb les
dades rebudes, tenint en compte l’usuari actual i la data-hora del sistema.
```mysql



```
Exercici 13 - Fes un procediment que ens permeti eliminar un departament determinat.
El departament s’ha d’eliminar de la taula departaments. Utilitza a més dins d’aquest
procediment, el procediment creat anteriorment (spRegistrarLog) per guardar també un
registre del que ha realitzat l’usuari. Ens ha de quedar clar que l’usuari actual, en data X
ha eliminat de la taula DEPARTAMENTS el codi departament Y.
Per exemple, si fem una crida al procediment per eliminar un departament:
CALL eliminarDept(300);
El departament 300 s’ha d’eliminar de la taula departaments, i a més si fem una SELECT
de la taula logs_usuaris, veuriem per exemple el següent:
uauri data taula accio valor_pk
root@localhost 2012-04-30 12:34:00 DEPARTAMENTS ELIMINAR 300
```mysql



```
Exercici 14 - Fes un procediment que ens posi en paràmetres de sortida, el número
d’empleats que tenim, el número de departaments i el número de localitzacions.
```mysql



```


# PRUEBA CON CURSORES

CREAR NOMBRES DE USUARIO:

```MYSQL

    DROP PROCEDURE IF EXISTS crearNomUsuari;
DELIMITER //
CREATE PROCEDURE crearNomUsuari()
BEGIN
  DECLARE done INT DEFAULT 0;
  DECLARE nombreEmpleado VARCHAR(50);
  DECLARE apellido VARCHAR(50);
  DECLARE nombreUsuario VARCHAR(100); -- Aumenté la longitud para evitar truncamiento

  -- Declaro un cursor para recorrer las filas de la tabla
  DECLARE cur CURSOR FOR SELECT nom, cognoms FROM empleats;
  -- Manejo de errores
  DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

  OPEN cur;
  
  -- Utilizando un bucle WHILE en lugar del bucle LOOP
  WHILE done = 0 DO
    FETCH cur INTO nombreEmpleado, apellido;
    IF done THEN
      LEAVE;
    END IF;

    -- Generar el nombre de usuario
    SET nombreUsuario = CONCAT(LOWER(SUBSTRING(nombreEmpleado, 1, 1)), '.', LOWER(apellido));

    -- Actualizar el campo nombreUsuario en la fila actual
    UPDATE empleats
    SET nombreUsuario = nombreUsuario
    WHERE nom = nombreEmpleado AND cognoms = apellido;
  END WHILE;

  CLOSE cur;
END//
DELIMITER ;

CALL crearNomUsuari();

```


# Triggers

Exercici 4: Validació de dades d’entrada
Mitjançant triggers volem dur el control de dades d’entrada d’una taula.
Concretament volem dur el control del camp salari de la taula empleats. Aquest
salari ha de ser un valor dins del rang marcat pels camps salari_min i
salari_max de la taula feines.
En definitiva, volem controlar que el salari dels empleats estigui dins dels rangs de
salaris marcats per el tipus de feina que fa l’empleat.

```MYSQL
DROP TRIGGER IF EXISTS trg_salari_empleats;

DELIMITER / /

CREATE TRIGGER trg_salari_empleats BEFORE UPDATE ON 
empleats FOR EACH ROW 
BEGIN 
	IF(
	    (
	        NEW.salari < (
	            SELECT salari_min
	            FROM feines
	            WHERE
	                feina_codi = NEW.feina_codi
	        )
	    )
	    OR (
	        NEW.salari > (
	            SELECT salari_max
	            FROM feines
	            WHERE
	                feina_codi = NEW.feina_codi
	        )
	    )
	) THEN
	SET
	    NEW.salari = OLD.salari;
END
	IF;
END
// 

DELIMITER / /

DROP TRIGGER IF EXISTS trg_salari_empleats_Insert;

CREATE TRIGGER trg_salari_empleats_Insert BEFORE INSERT 
ON empleats FOR EACH ROW 
BEGIN 
	IF(
	    (
	        NEW.salari < (
	            SELECT salari_min
	            FROM feines
	            WHERE
	                feina_codi = NEW.feina_codi
	        )
	    )
	    OR (
	        NEW.salari > (
	            SELECT salari_max
	            FROM feines
	            WHERE
	                feina_codi = NEW.feina_codi
	        )
	    )
	) THEN
	SIGNAL SQLSTATE '43000'
	SET
	    MESSAGE_TEXT = 'El salari introduit no es valit';
END
	IF;
END
// 

DELIMITER;

INSERT INTO
    empleats (
        empleat_id, nom, cognoms, email, salari, feina_codi, data_contractacio
    )
VALUES (
        5044, 'Pacouo', 'Paquito', 'pao1@paucoo.com', 10000, 'IT_PROG', '1999-09-09'
    )


```
# Creacion de usuarios y permisos

```mysql
-- crear usuario1
CREATE USER IF NOT EXISTS 'usuari1'@'localhost' IDENTIFIED BY '!Q"W12qw';
-- darle permisos de INSERT, UPDATE, DELETE, SELECT en todas las tablas de la bd
GRANT INSERT, UPDATE, DELETE, SELECT ON rrhh.* TO 'usuari1'@'localhost';
-- crear usuario2
CREATE USER IF NOT EXISTS 'usuari2'@'localhost' IDENTIFIED BY '!Q"W12qw';
-- darle permisos de actualizacion en columna salaris de la tabla empleados
GRANT UPDATE (salari) ON rrhh.empleats TO 'usuari2'@'localhost';
-- darle permisos para hacer SELECTS en la BD empleats
GRANT SELECT ON rrhh.* TO 'usuari2'@'localhost';
-- darle permisos para hacer SELECTS en la BD empleats
GRANT SELECT ON rrhh.* TO 'usuari1'@'%';



```
