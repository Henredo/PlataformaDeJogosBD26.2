>[!SUMMARY] Table of Contents
>    - [[PlataformaDeJogosBD#Objetivo|Objetivo]]
>    - [[PlataformaDeJogosBD#Utilizando postgresql no terminal|Utilizando postgresql no terminal]]
>    - [[PlataformaDeJogosBD#Criando a database com terminal|Criando a database com terminal]]
>        - [[PlataformaDeJogosBD#Acessando database|Acessando database]]
>    - [[PlataformaDeJogosBD#Criando tabelas|Criando tabelas]]
>    - [[PlataformaDeJogosBD#Populando|Populando]]
>        - [[PlataformaDeJogosBD#adicionando Hollow Knight(https://steamdb.info/app/367520/)|adicionando Hollow Knight]]
>            - [[PlataformaDeJogosBD#Atualizando preço|Atualizando preço]]
>        - [[PlataformaDeJogosBD#Adicionando Total War: WARHAMMER III(https://steamdb.info/app/1142710/charts/)|Adicionando Total War: WARHAMMER III]]
>            - [[PlataformaDeJogosBD#Atualizando preço|Atualizando preço]]
>    - [[PlataformaDeJogosBD#Compilado de scripts|Compilado de scripts]]
>    - [[PlataformaDeJogosBD#Visão|Visão]]
## Informação
### Grupo 4
	- Henry Monteiro
	- Raul Ramalho
	- Luiz Mantovani
## Objetivo
Esse arquivo tem como objetivo descrever todo o processo utilizado na criação da database para a matéria de Banco de Dados de forma facil de entender.
Além de fornecer o [[PlataformaDeJogosBD#Compilado de scripts|Compilado de scripts]] que permite recriar o banco de dados e popular ele de vez.
%%
### Usefull Info

Documentação da plataforma [[Banco_de_dados-2.pdf]]

> [!NOTE]
> 	## Tipos de dados
> 	INTEIROS
> 		SMALLINT
> 			inteiro, 2 bytes
> 		INT
> 			inteiro, 4 bytes
> 		BIGINT
> 			inteiro, 8 bytes
> 	CHARS
> 		VARCHAR(n)
> 			string, tamanho variavel e de max n
> 		TEXT
> 			 string, sem limite de tamanho (até 1gb)
> 		CHAR
> 			duh
> 	BOOLEAN
> 		é isso (TRUE/FALSE/NULL)
> 	ENUM
> 		estrutura customizavel com multiplos valores setados em sting
> 		ex:
> 			`cargo_usuario AS ENUM('plebe','plebe_plus','ser_humano');`
> 	TIMESTAMP
<img
    src="https://media.geeksforgeeks.org/wp-content/uploads/20260416144508248850/sql_data_types.webp"
>%%
## Utilizando postgresql no terminal
inicializar
`psql -U postgres`
%%`6969`%%
ver databases
`\l`
## Criando a database com terminal
```sql
CREATE DATABASE plataforma_jogos;
```
### Acessando database
`\c plataforma_jogos`
## Criando tabelas
primeiro fazer a publicadora pq jogo usa chave estangeira de id_publicadora e id_franquia
publicadora
```sql
CREATE TABLE publicadora(id_publicadora INT PRIMARY KEY NOT NULL, nome VARCHAR(100) NOT NULL, pais VARCHAR(50), site VARCHAR(150));
```

`\d` listar relações
franquia
```sql
CREATE TABLE franquia(id_franquia INT PRIMARY KEY NOT NULL, nome VARCHAR(150) NOT NULL);
```
jogo
```sql
CREATE TABLE jogo(id_jogo INT PRIMARY KEY NOT NULL, titulo VARCHAR(150) NOT NULL, preco DECIMAL(10,2) NOT NULL, data_lancamento DATE NOT NULL, descricao TEXT, classificacao VARCHAR(10), id_publicadora INT NOT NULL, id_franquia INT, FOREIGN KEY(id_publicadora) REFERENCES publicadora(id_publicadora), FOREIGN KEY(id_franquia) REFERENCES franquia(id_franquia));
```
%%[issue 1]tentar SERIAL em id_jogo%%
%%alterado maximo de characteres para 150, afim de acomodar AVIÃOZINHO DO TRÁFICO 3:ABRI UM PORTAL PRO INFERNO NA FAVELA TENTANDO REVIVER MIT AIA E PRECISO FECHAR%%
desenvolvedora
```sql
CREATE TABLE desenvolvedora(id_desenvolvedora INT PRIMARY KEY NOT NULL, nome VARCHAR(100) NOT NULL, pais VARCHAR(50), site VARCHAR(150));
```
categoria
```sql
CREATE TABLE categoria(id_categoria INT PRIMARY KEY NOT NULL, nome_categoria VARCHAR(40) NOT NULL, descricao TEXT);
```
jogo_categoria
```sql
CREATE TABLE jogo_categoria(id_jogo INT NOT NULL, id_categoria INT NOT NULL, PRIMARY KEY(id_jogo, id_categoria), FOREIGN KEY(id_jogo) REFERENCES jogo(id_jogo), FOREIGN KEY(id_categoria) REFERENCES categoria(id_categoria));
```
jogo_desenvolvedora
```sql
CREATE TABLE jogo_desenvolvedora(id_jogo INT NOT NULL, id_desenvolvedora INT NOT NULL, PRIMARY KEY(id_jogo, id_desenvolvedora), FOREIGN KEY(id_jogo) REFERENCES jogo(id_jogo), FOREIGN KEY(id_desenvolvedora) REFERENCES desenvolvedora(id_desenvolvedora));
```
preco_regional
```sql
CREATE TABLE preco_regional(id_jogo INT NOT NULL, pais CHAR(2) NOT NULL, moeda VARCHAR(3) NOT NULL, preco DECIMAL(10,2) NOT NULL, PRIMARY KEY(id_jogo, pais), FOREIGN KEY(id_jogo) REFERENCES jogo(id_jogo));
```

## Populando
### adicionando [Hollow Knight](https://steamdb.info/app/367520/)
publicadora
```sql
INSERT INTO publicadora(id_publicadora, nome, pais) VALUES('1', 'Team Cherry', 'AU');
```
franquia
```sql
INSERT INTO franquia(id_franquia, nome) VALUES('1', 'Hollow Knight');
```
jogo
```sql
INSERT INTO jogo(id_jogo, titulo, preco, data_lancamento, descricao, classificacao, id_publicadora, id_franquia) VALUES('1', 'hollow_knight', '20.00', '24/02/2017', 'Grubies', '6', '1', '1');
```
desenvolvedora
```sql
INSERT INTO desenvolvedora(id_desenvolvedora, nome) VALUES('1','Team Cherry');
```
jogo_desenvolvedora
```sql
INSERT INTO jogo_desenvolvedora(id_jogo, id_desenvolvedora) VALUES('1','1');
```
categorias
```sql
INSERT INTO categoria(id_categoria, nome_categoria, descricao) VALUES ('1','Metroidvania','Metroidvania is a sub-genre of action-adventure games named after the Metroid and Castlevania franchises. These games emphasize interconnected world maps that gradually open up as players acquire new abilities, items, or power-ups that grant access to previously unreachable areas. Exploration, backtracking, and non-linear progression are core elements of the genre. This page lists all Steam games tagged as Metroidvania and is updated automatically.');
```

```sql
INSERT INTO categoria(id_categoria, nome_categoria, descricao) VALUES ('2','Platformer','Platformer is a genre of video games where the player character typically moves by jumping from one platform to another in a side-scrolling perspective. These games often involve precise timing and quick reflexes, making for engaging and challenging gameplay experiences.');
```

```sql
INSERT INTO categoria(id_categoria, nome_categoria, descricao) VALUES ('3','Souls_like','These games typically feature challenging combat mechanics, intricate world designs, and complex narrative structures that reward exploration and experimentation. Players are often encouraged to learn from their mistakes as death is a common consequence of failure, adding to the sense of immersion and achievement.');
```
jogo_categorias
```sql
INSERT INTO jogo_categoria(id_jogo, id_categoria) VALUES ('1','1');
```

```sql
INSERT INTO jogo_categoria(id_jogo, id_categoria) VALUES ('1','2');
```

```sql
INSERT INTO jogo_categoria(id_jogo, id_categoria) VALUES ('1','3');
```
preco_regional
```sql
INSERT INTO preco_regional(id_jogo, pais, moeda, preco) VALUES ('1', 'BR', 'BRL', '46.99');
```

```sql
INSERT INTO preco_regional(id_jogo, pais, moeda, preco) VALUES ('1', 'EU', 'EUR', '87.55');
```

```sql
INSERT INTO preco_regional(id_jogo, pais, moeda, preco) VALUES ('1', 'US', 'USD', '77.67');
```

#### Atualizando preço
```sql
UPDATE jogo SET preco = (SELECT AVG(preco) FROM preco_regional WHERE id_jogo = 1) WHERE id_jogo = 1;
```

### Adicionando [Total War: WARHAMMER III](https://steamdb.info/app/1142710/charts/)
%%[issue 2]esse jogo tem 2 developer e 2 publisher e 2 franquias%%
publicadora
```sql
INSERT INTO publicadora(id_publicadora, nome, pais) VALUES('2', 'SEGA', 'JP');
```
%%[issue 2]também é publicado pela Feral Interactive%%
franquia
```sql
INSERT INTO franquia(id_franquia, nome) VALUES('2', 'Warhammer');
```
%%[issue 2]também é da franquia Total War%%
jogo
```sql
INSERT INTO jogo(id_jogo, titulo, preco, data_lancamento, descricao, classificacao, id_publicadora, id_franquia) VALUES('2', 'Total War: WARHAMMER III', '249.90', '17/02/2022', 'The cataclysmic conclusion to the Total War: WARHAMMER trilogy is here. Rally your forces and step into the Realm of Chaos, a dimension of mind-bending horror where the very fate of the world will be decided. Will you conquer your Daemons… or command them?', '12', '2', '2');
```
desenvolvedoras
```sql
INSERT INTO desenvolvedora(id_desenvolvedora, nome) VALUES('2','CREATIVE ASSEMBLY');
```
```sql
INSERT INTO desenvolvedora(id_desenvolvedora, nome) VALUES('3','Feral Interactive');
```
jogo_desenvolvedora
```sql
INSERT INTO jogo_desenvolvedora(id_jogo, id_desenvolvedora) VALUES('2','2');
```
```sql
INSERT INTO jogo_desenvolvedora(id_jogo, id_desenvolvedora) VALUES('2','3');
```
categorias
```sql
INSERT INTO categoria(id_categoria, nome_categoria, descricao) VALUES ('4','Strategy','These games often require players to think critically and plan ahead to achieve goals. They may involve managing resources, building structures, leading armies, or navigating complex diplomatic relationships. The Strategy tag encompasses a wide range of sub-genres such as real-time strategy (RTS), turn-based strategy (TBS), grand strategy, and 4X games. Whether youre commanding an army in real time, planning your next move in a turn-based game, or ruling over a vast empire, the Strategy tag has you covered.');
```

```sql
INSERT INTO categoria(id_categoria, nome_categoria, descricao) VALUES ('5','Baseado_em_turnos','Turn-Based Strategy games are a genre of strategy video games where players take turns making moves or decisions. These games require careful planning, resource management, and tactical thinking to outmaneuver opponents. The turn-based nature allows players to thoughtfully consider their actions before executing them, adding depth and strategy to the gameplay experience.');
```

```sql
INSERT INTO categoria(id_categoria, nome_categoria, descricao) VALUES ('6','Fantasia','The Fantasy genre encompasses imaginary worlds filled with magic, mythical creatures, and intricate stories. These games transport players to new realms, allowing them to embark on epic adventures, engage in thrilling battles, and explore enchanted lands.');
```
jogo_categorias
```sql
INSERT INTO jogo_categoria(id_jogo, id_categoria) VALUES ('2','4');
```

```sql
INSERT INTO jogo_categoria(id_jogo, id_categoria) VALUES ('2','5');
```

```sql
INSERT INTO jogo_categoria(id_jogo, id_categoria) VALUES ('2','6');
```
preco_regional
```sql
INSERT INTO preco_regional(id_jogo, pais, moeda, preco) VALUES ('2', 'BR', 'BRL', '249.99');
```

```sql
INSERT INTO preco_regional(id_jogo, pais, moeda, preco) VALUES ('2', 'EU', 'EUR', '355.14');
```

```sql
INSERT INTO preco_regional(id_jogo, pais, moeda, preco) VALUES ('2', 'US', 'USD', '310.85');
```

#### Atualizando preço
```sql
UPDATE jogo SET preco = (SELECT AVG(preco) FROM preco_regional WHERE id_jogo = 2) WHERE id_jogo = 2;
```

## Compilado de scripts
Compilado de todos os scripts até aqui que em um commando deve criar o banco de dados
```sql
CREATE DATABASE plataforma_jogos;
```

`\c plataforma_jogos`

CRIAÇÃO DE RELAÇÕES
```sql
CREATE TABLE publicadora(id_publicadora INT PRIMARY KEY NOT NULL, nome VARCHAR(100) NOT NULL, pais VARCHAR(50), site VARCHAR(150));CREATE TABLE franquia(id_franquia INT PRIMARY KEY NOT NULL, nome VARCHAR(150) NOT NULL);CREATE TABLE jogo(id_jogo INT PRIMARY KEY NOT NULL, titulo VARCHAR(150) NOT NULL, preco DECIMAL(10,2) NOT NULL, data_lancamento DATE NOT NULL, descricao TEXT, classificacao VARCHAR(10), id_publicadora INT NOT NULL, id_franquia INT, FOREIGN KEY(id_publicadora) REFERENCES publicadora(id_publicadora), FOREIGN KEY(id_franquia) REFERENCES franquia(id_franquia));CREATE TABLE desenvolvedora(id_desenvolvedora INT PRIMARY KEY NOT NULL, nome VARCHAR(100) NOT NULL, pais VARCHAR(50), site VARCHAR(150));CREATE TABLE categoria(id_categoria INT PRIMARY KEY NOT NULL, nome_categoria VARCHAR(40) NOT NULL, descricao TEXT);CREATE TABLE jogo_categoria(id_jogo INT NOT NULL, id_categoria INT NOT NULL, PRIMARY KEY(id_jogo, id_categoria), FOREIGN KEY(id_jogo) REFERENCES jogo(id_jogo), FOREIGN KEY(id_categoria) REFERENCES categoria(id_categoria));CREATE TABLE jogo_desenvolvedora(id_jogo INT NOT NULL, id_desenvolvedora INT NOT NULL, PRIMARY KEY(id_jogo, id_desenvolvedora), FOREIGN KEY(id_jogo) REFERENCES jogo(id_jogo), FOREIGN KEY(id_desenvolvedora) REFERENCES desenvolvedora(id_desenvolvedora));CREATE TABLE preco_regional(id_jogo INT NOT NULL, pais CHAR(2) NOT NULL, moeda VARCHAR(3) NOT NULL, preco DECIMAL(10,2) NOT NULL, PRIMARY KEY(id_jogo, pais), FOREIGN KEY(id_jogo) REFERENCES jogo(id_jogo));
```
POPULANDO
```sql
INSERT INTO publicadora(id_publicadora, nome, pais) VALUES('1', 'Team Cherry', 'AU');INSERT INTO franquia(id_franquia, nome) VALUES('1', 'Hollow Knight');INSERT INTO jogo(id_jogo, titulo, preco, data_lancamento, descricao, classificacao, id_publicadora, id_franquia) VALUES('1', 'hollow_knight', '20.00', '24/02/2017', 'Grubies', '6', '1', '1');INSERT INTO desenvolvedora(id_desenvolvedora, nome) VALUES('1','Team Cherry');INSERT INTO jogo_desenvolvedora(id_jogo, id_desenvolvedora) VALUES('1','1');INSERT INTO categoria(id_categoria, nome_categoria, descricao) VALUES ('1','Metroidvania','Metroidvania is a sub-genre of action-adventure games named after the Metroid and Castlevania franchises. These games emphasize interconnected world maps that gradually open up as players acquire new abilities, items, or power-ups that grant access to previously unreachable areas. Exploration, backtracking, and non-linear progression are core elements of the genre. This page lists all Steam games tagged as Metroidvania and is updated automatically.');INSERT INTO categoria(id_categoria, nome_categoria, descricao) VALUES ('2','Platformer','Platformer is a genre of video games where the player character typically moves by jumping from one platform to another in a side-scrolling perspective. These games often involve precise timing and quick reflexes, making for engaging and challenging gameplay experiences.');INSERT INTO categoria(id_categoria, nome_categoria, descricao) VALUES ('3','Souls_like','These games typically feature challenging combat mechanics, intricate world designs, and complex narrative structures that reward exploration and experimentation. Players are often encouraged to learn from their mistakes as death is a common consequence of failure, adding to the sense of immersion and achievement.');INSERT INTO jogo_categoria(id_jogo, id_categoria) VALUES ('1','1');INSERT INTO jogo_categoria(id_jogo, id_categoria) VALUES ('1','2');INSERT INTO jogo_categoria(id_jogo, id_categoria) VALUES ('1','3');INSERT INTO preco_regional(id_jogo, pais, moeda, preco) VALUES ('1', 'BR', 'BRL', '46.99');INSERT INTO preco_regional(id_jogo, pais, moeda, preco) VALUES ('1', 'EU', 'EUR', '87.55');INSERT INTO preco_regional(id_jogo, pais, moeda, preco) VALUES ('1', 'US', 'USD', '77.67');UPDATE jogo SET preco = (SELECT AVG(preco) FROM preco_regional WHERE id_jogo = 1) WHERE id_jogo = 1;INSERT INTO publicadora(id_publicadora, nome, pais) VALUES('2', 'SEGA', 'JP');INSERT INTO franquia(id_franquia, nome) VALUES('2', 'Warhammer');INSERT INTO jogo(id_jogo, titulo, preco, data_lancamento, descricao, classificacao, id_publicadora, id_franquia) VALUES('2', 'Total War: WARHAMMER III', '249.90', '17/02/2022', 'The cataclysmic conclusion to the Total War: WARHAMMER trilogy is here. Rally your forces and step into the Realm of Chaos, a dimension of mind-bending horror where the very fate of the world will be decided. Will you conquer your Daemons… or command them?', '12', '2', '2');INSERT INTO desenvolvedora(id_desenvolvedora, nome) VALUES('2','CREATIVE ASSEMBLY');INSERT INTO desenvolvedora(id_desenvolvedora, nome) VALUES('3','Feral Interactive');INSERT INTO jogo_desenvolvedora(id_jogo, id_desenvolvedora) VALUES('2','2');INSERT INTO jogo_desenvolvedora(id_jogo, id_desenvolvedora) VALUES('2','3');INSERT INTO categoria(id_categoria, nome_categoria, descricao) VALUES ('4','Strategy','These games often require players to think critically and plan ahead to achieve goals. They may involve managing resources, building structures, leading armies, or navigating complex diplomatic relationships. The Strategy tag encompasses a wide range of sub-genres such as real-time strategy (RTS), turn-based strategy (TBS), grand strategy, and 4X games. Whether youre commanding an army in real time, planning your next move in a turn-based game, or ruling over a vast empire, the Strategy tag has you covered.');INSERT INTO categoria(id_categoria, nome_categoria, descricao) VALUES ('5','Baseado_em_turnos','Turn-Based Strategy games are a genre of strategy video games where players take turns making moves or decisions. These games require careful planning, resource management, and tactical thinking to outmaneuver opponents. The turn-based nature allows players to thoughtfully consider their actions before executing them, adding depth and strategy to the gameplay experience.');INSERT INTO categoria(id_categoria, nome_categoria, descricao) VALUES ('6','Fantasia','The Fantasy genre encompasses imaginary worlds filled with magic, mythical creatures, and intricate stories. These games transport players to new realms, allowing them to embark on epic adventures, engage in thrilling battles, and explore enchanted lands.');INSERT INTO jogo_categoria(id_jogo, id_categoria) VALUES ('2','4');INSERT INTO jogo_categoria(id_jogo, id_categoria) VALUES ('2','5');INSERT INTO jogo_categoria(id_jogo, id_categoria) VALUES ('2','6');INSERT INTO preco_regional(id_jogo, pais, moeda, preco) VALUES ('2', 'BR', 'BRL', '249.99');INSERT INTO preco_regional(id_jogo, pais, moeda, preco) VALUES ('2', 'EU', 'EUR', '355.14');INSERT INTO preco_regional(id_jogo, pais, moeda, preco) VALUES ('2', 'US', 'USD', '310.85');UPDATE jogo SET preco = (SELECT AVG(preco) FROM preco_regional WHERE id_jogo = 2) WHERE id_jogo = 2;
```
## Visão
criando visão que mostra os jogos e os preços em R$
```sql
CREATE VIEW jogo_brasil (Jogo, Preço_em_real) AS SELECT jogo.titulo, pbr.preco FROM jogo JOIN preco_regional pbr ON jogo.id_jogo = pbr.id_jogo WHERE pbr.pais = 'BR' ORDER BY pbr.preco ASC;
```
%% jogo.titulo AS jogo %%

%%## ODBC%%
