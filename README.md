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




Exercici 4 - Fes una funció anomenada spPringat, tal que li passem un codi de
departament, i ens torni el codi d’empleat que guanya menys d’aquell departament.

Exercici 5 - Utilitzant la funció spPringat fes una consulta per obtenir de cada
departament, l’empleat pringat. Mostra el codi i nom del departament, i el codi d’empleat.

Exercici 6 - Fes una funció anomenada spCategoria, tal que donat un codi d’empleat,
ens digui en quina categoria professional està. El criteri que volem seguir per determinar
la categoria professional és en funció dels anys que porta treballant a l’empresa:
Entre 0 i 1 anys -> Auxiliar
Entre 2 i 10 anys -> Oficial de Segona
Entre 11 i 20 Anys -> Oficial de Primera
Més de 20 anys -> Que es jubili!

Exercici 7 - Fes una consulta utilitzant la funció anterior perquè mostri mostri de cada
empleat, el codi d’empleat, el nom, els anys treballats i la categoria professional a la que
pertany.


Exercici 8 - Fes una funció anomenada spEdat, tal que donada una data per paràmetre
ens retorni l'edat d'una persona. Les dates posteriors a la data d'avui han de retornar 0.

Exercici 9 - Fes una funció que ens retorni el número de directors (caps) diferents tenim.

Exercici 10 - Quina instrucció utilitzarem si volem veure el contingut de la funció
spPringat?