#ACTIVITAT - EXERCICIS SUBPROGRAMES

Resol aquests exercicis de l'elaboració de subprogrames mitjançant les BD
proporcionades durant el curs.

ENUNCIATS DE FUNCIONS

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