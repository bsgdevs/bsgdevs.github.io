# Ladrilhos e auto-ladrilhamento em jogos
## O que são ladrilhos em desenvolvimento de jogos
O uso de ladrilhos em jogos se refere à uma técnica do qual um cenário é montado a partir da composição de pequenos pedaços de uma imagem encaixados sobre uma grade matemática.

> FAZER: INSERIR IMAGEM ILUSTRATIVA, COM O CONJUNTO DESTACADO E UM MAPA MONTADO A FIM DE DEMONSTRAR O CENÁRIO COMPOSTO PELOS LADRILHOS DE SEU CONJUNTO.

Esses pequenos pedaços são chamados **Ladrilhos** (também conhecidos como *Tiles*), a imagem original usada como fonte pelos ladrilhos é chamada de **Conjunto de Ladrilhos** (ou *Tile Set*) e a composição desses ladrilhos que criam o cenário final é um **Mapa de Ladrilhos** (ou *Tile Map*).

É bastante comum que a forma desses ladrilhos sejam quadrados e logo montadas numa grade de células quadradas, entretanto formas retangulares, losangulares ou hexagonais também existem e assim também suas respectivas grades.

> FAZER: INSERIR IMAGEM DE JOGO BASEADO EM UM MAPA DE LADRILHOS HEXAGONAIS, TAMBÉM INSERIR PARA LADRILHOS LOSANGULARES.

Normalmente a imagem final composta desses ladrilhos não deixa óbvio que o mesmo foi composto por esses pequenos pedaços, dando assim a ilusão de que o cenário foi montado de forma individual.

Por fim, tais grades são montadas matematicamente e normalmente são apenas visíveis no editor de mapas do motor de jogos utilizado pelo desenvolvedor.

> FAZER: CONSIDERAR A INSERÇÃO DE UMA CAPTURA DE TELA (OU GIF) DO EDITOR DE MAPA DA GODOT, PARA DEMONSTRAR A GRADE SENDO EXIBIDA PARA FINS DE EDIÇÃO.

## Uso de ladrilhos
> FAZER: DEFINIR O POR QUÊ LADRILHOS SÃO USADOS (OTIMIZAÇÃO DE MEMÓRIA E EFICIÊNCIA DE PRODUÇÃO

## Implementação de ladrilhamento
> FAZER: CRIAR IMPLEMENTAÇÃO BÁSICA EM CÓDIGO DE UM MAPA DE LADRILHOS, O CÓDIGO DEVERÁ SER O MAIS BÁSICO POSSÍVEL PARA FINS DE DIDÁTICA

## Auto-ladrilhamento
> FAZER EXPLICAR O QUE É AUTO-LADRILHAMENTO E COMO FUNCIONA (PADRÕES, MAPA DE BOOLIANOS, CONVERSÃO PARA UM MAPA



## Fontes
[Jogo eletrônico baseado em ladrilho](https://en.wikipedia.org/wiki/Tile-based_video_game)
[How to Use Tile Bitmasking to Auto-Tile Your Level Layouts](https://gamedevelopment.tutsplus.com/tutorials/how-to-use-tile-bitmasking-to-auto-tile-your-level-layouts--cms-25673)
[An Introduction to Creating a Tile Map Engine](https://gamedevelopment.tutsplus.com/tutorials/an-introduction-to-creating-a-tile-map-engine--gamedev-10900)




---------------------
---------------------
> Versão antiga, feito em cima do React, convertido daqui: https://github.com/Andre-LA/my-autotiler-toy/blob/2e1f3e2bd4fe840043831b76cd774d73c2e7db1c/src/App.js#L196-L320

# Auto-ladrilhamento

## Explicação da implementação

### O que é um Tilemap e Autotiling?

Há várias formas de se construir um cenário de um jogo, e uma das mais clássicas é
através dos chamados _Tilemaps_, que são a construção de um cenário através de pequenas
peças chamadas de _tiles_, essas tiles são usadas a partir de um conjunto dessas peças,
chamado de _tileset_.

Por exemplo, um tilemap de praia simples poderia ser criada a partir de um tileset de
apenas três tiles (mar, areia e espraiamento).

Entretanto, todo tilemap mais enxuto contém diversas tiles que se conectam e
precisam **respeitar um certo padrão**, usando o exemplo do editor acima,
num jogo 2D lateral onde temos uma tile de terra e outra também de terra, mas com sua
superfície coberta de neve, essa tile com neve só poderia ser utilizada se ela for
uma superfície, ou seja, não há tiles em cima dela.

Ou seja, existe a necessidade de colocar tiles específicos adequadamente, e este processo,
se feito de forma completamente manual, pode ser bem entediante e consumir muito tempo, além
de ser sucetível a erros. Felizmente há algumas técnicas para automatizar esse processo,
e por isso são chamadas de _autotiling_.

### Como funciona um Autotiler de forma geral
Existem diferentes implementações de autotiling, algumas simples como a desta página,
e [outras mais complexas](https://twitter.com/x_rxi/status/1276576637333770244).

Autotilers mais complexos te permite maiores possibilidades de automatização para tilemaps
complexos, entretanto, para tilemaps simples essa complexidade pode também não compensar,
então sempre depende do seu caso específico.

Apesar das diferenças entre as implementações, a maioria (pra não dizer todas) se baseiam
no mesmo princípio, que é associar as tiles de uma tileset com padrões, também chamada de
regras em algumas implementações.

Um padrão (ou regra) por si só é um dado que guarda a existência ou inexistência de tiles
adjacentes à tile correspondente ao padrão, entretanto ele não é mutável e não guarda
"se há uma tile adjacente" em tempo de execução do jogo, ele apenas guarda a existência
e inexistência de tiles adjacentes, como se fosse um tipo de código ou identificação.

Esse padrão é utilizado para indicar quando uma certa tile deverá ser aplicada pelo
autotiler, usando exemplo anterior, uma tile coberta de neve só poderia ser
utilizada se não houver uma tile adjacente em cima; sendo assim, o padrão dessa tile
é a de que não deverá existir uma tile adjacente acima.

Sendo assim, o que um _autotiler_ básico faz é receber um tilemap simples que
simplesmente indica a existência ou não de tiles, e então é convertido para um tilemap
mais complexo a partir da correspondências de cada tile com sua combinação de adjacências.

Como dito anteriormente, essa é uma premissa geral, como um autotiler realmente funciona
de forma mais detalhada depende da implementação, dois exemplos são os
softwares [LDtk](https://ldtk.io/) e [Tilekit](https://rxi.itch.io/tilekit|),
que são ferramentas de criação de tilemaps com a funcionalidade de autotiling.

### Implementação deste autotiller
Essa seção abordará da implementação específica utilizada no editor acima, é recomendável
a leitura de outras fontes (citadas abaixo) para um entendimento melhor, mais abrangente
e com maiores horizontes, entretanto não é interesse desse artigo explicar a implementação
linha por linha, para isso, recomendo
a <a href="https://github.com/Andre-LA/my-autotiler-toy/blob/main/src/App.js">
leitura do código fonte
</a> diretamente, que está documentada de forma mais específica.

Como dito anteriormente, um autotiler obtém como entrada um "tilemap booliano" e um
conjunto de padrões (ou regras) associados às tiles de um tileset.

Com isso, ele consegue corresponder cada tile do tilemap com um dos padrões de acordo
com as tiles adjacentes à tile do tilemap, isto resulta numa tile do tileset, que
então será aplicada na posição da tile do tilemap, no final do processo um novo tilemap
será criado e finalmente retornado.

O padrão em si é representado por
um <a href="https://en.wikipedia.org/wiki/Bit_array">bitset</a> (também chamado de bitmask),
enquanto que o tilemap, no caso desta implementação, é inicialmente um arranjo de boolianas
e ao final do processo de autotiling se torna um arranjo de coordenadas (que se referem a posição
dos tile vindos do tileset).

Observação do Autor: Na verdade o autotiler não está retornando um arranjo de coordenadas, e sim
criando os tiles diretamente porque o autotiler está aplicado nas tiles e não como uma função
separada, eu preciso corrigir isto, pois acredito que modularizar esse processo numa função
tornará a implementação mais adequada.
