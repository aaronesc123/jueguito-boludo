# jueguito-boludo
Un juego de "rol" xd, exploración de mazmorras y estrategia por turnos que combina la gestión de recursos de Scoundrel, la mecánica de combos/multiplicadores de Balatro y el posicionamiento táctico del Ajedrez... ¡ejecutado 100% dentro de un motor de base de datos SQL!No requiere un motor de videojuegos tradicional: la base de datos es la lógica, las consultas UPDATE son los controles y las consultas SELECT son la pantalla de renderizado en arte ASCII.🕹️ Mecánicas del JuegoEl juego te coloca en una sala de cuadrícula (tablero de ajedrez). Cada casilla representa un elemento o una amenaza:[ K ] Rey (Tú): Tu posición actual en el tablero.[ P6 ] Peón (Monstruo): Enemigo débil. Su número representa sus Puntos de Vida (HP).[ N12 ] Caballo (Monstruo): Enemigo fuerte con mayor vida.[ S5 ] Espada (Arma): Te otorga poder de ataque base y aumenta tu multiplicador (MULT).[ H4 ] Poción: Restaura tu salud actual.[ . ] Vacío: Casilla libre por la que puedes desplazarte.⚔️ Sistema de CombateEl daño realizado a los enemigos sigue la lógica de multiplicadores de Balatro:$$\text{Daño Total} = \text{Ataque de Arma} \times \text{Multiplicador}$$Si tu Daño Total es igual o superior al HP del enemigo, lo eliminas sin recibir daño.Si el enemigo sobrevive a tu ataque, te infligirá la diferencia de vida que le quede como daño directo a tu HP.🚀 Cómo Empezar a JugarNo necesitas instalar software ni bases de datos complejas. Puedes jugarlo directamente en el navegador a través de SQLiteOnline.Paso 1: Inicializar la PartidaCopia el siguiente código en la consola principal de SQLiteOnline y presiona Run (o Ctrl + Enter):SQL-- 1. CREACIÓN Y REINICIO DE TABLAS
DROP TABLE IF EXISTS tablero;
DROP TABLE IF EXISTS jugador;

CREATE TABLE jugador (
    id INT PRIMARY KEY, 
    hp INT, 
    hp_max INT, 
    arma_ataque INT, 
    multiplicador INT
);

CREATE TABLE tablero (
    x CHAR(1), 
    y INT, 
    tipo VARCHAR(20), 
    valor INT, 
    icono VARCHAR(10)
);

-- Estado Inicial del Jugador
INSERT INTO jugador VALUES (1, 20, 20, 0, 1);

-- Generación de la Sala (Tablero 4x2)
INSERT INTO tablero VALUES 
('A', 1, 'JUGADOR',  0,  '[ K ]'),
('B', 1, 'MONSTRUO', 6,  '[ P6 ]'),
('C', 1, 'POCION',   4,  '[ H4 ]'),
('D', 1, 'ARMA',     5,  '[ S5 ]'),
('A', 2, 'MONSTRUO', 12, '[ N12]'),
('B', 2, 'VACIO',    0,  '[ . ]'),
('C', 2, 'VACIO',    0,  '[ . ]'),
('D', 2, 'VACIO',    0,  '[ . ]');

-- RENDERIZAR TABLERO Y ESTADÍSTICAS
SELECT ('HP: ' || hp || '/' || hp_max || ' | ARMA: +' || arma_ataque || ' | MULT: x' || multiplicador) AS STATS FROM jugador;

SELECT 
    y AS Fila,
    MAX(CASE WHEN x = 'A' THEN icono ELSE '' END) AS A,
    MAX(CASE WHEN x = 'B' THEN icono ELSE '' END) AS B,
    MAX(CASE WHEN x = 'C' THEN icono ELSE '' END) AS C,
    MAX(CASE WHEN x = 'D' THEN icono ELSE '' END) AS D
FROM tablero GROUP BY y ORDER BY y DESC;
🎮 Guía de Comandos (Cómo Moverte)Para realizar una acción, borra el código en el editor, pega la instrucción del movimiento que deseas realizar y presiona Run.1. Equipar la Espada en D1SQL-- Equipas el arma y aumentas tu multiplicador
UPDATE jugador SET arma_ataque = 5, multiplicador = 2 WHERE id = 1;
UPDATE tablero SET tipo = 'VACIO', valor = 0, icono = '[ . ]' WHERE x = 'D' AND y = 1;

-- Renderizar la pantalla
SELECT ('HP: ' || hp || '/' || hp_max || ' | ARMA: +' || arma_ataque || ' | MULT: x' || multiplicador) AS STATS FROM jugador;
SELECT y AS Fila,
    MAX(CASE WHEN x = 'A' THEN icono ELSE '' END) AS A,
    MAX(CASE WHEN x = 'B' THEN icono ELSE '' END) AS B,
    MAX(CASE WHEN x = 'C' THEN icono ELSE '' END) AS C,
    MAX(CASE WHEN x = 'D' THEN icono ELSE '' END) AS D
FROM tablero GROUP BY y ORDER BY y DESC;
2. Atacar / Moverte a la Casilla B1SQL-- Vaciamos la casilla previa (A1) y movemos al Jugador a (B1)
UPDATE tablero SET tipo = 'VACIO', valor = 0, icono = '[ . ]' WHERE x = 'A' AND y = 1;
UPDATE tablero SET tipo = 'JUGADOR', valor = 0, icono = '[ K ]' WHERE x = 'B' AND y = 1;

-- Renderizar la pantalla
SELECT ('HP: ' || hp || '/' || hp_max || ' | ARMA: +' || arma_ataque || ' | MULT: x' || multiplicador) AS STATS FROM jugador;
SELECT y AS Fila,
    MAX(CASE WHEN x = 'A' THEN icono ELSE '' END) AS A,
    MAX(CASE WHEN x = 'B' THEN icono ELSE '' END) AS B,
    MAX(CASE WHEN x = 'C' THEN icono ELSE '' END) AS C,
    MAX(CASE WHEN x = 'D' THEN icono ELSE '' END) AS D
FROM tablero GROUP BY y ORDER BY y DESC;
🛠️ Requisitos TécnicosFunciona en cualquier motor de base de datos SQL compatible con agregaciones y expresiones condicionales:SQLite 3.xPostgreSQLMySQL / MariaDB / mysql
