# Projeto SVG A3

Este projeto envolve a criação de uma página web interativa que gera gráficos SVG dinamicamente com base na entrada do usuário. O foco principal é visualizar diferentes formas e padrões de acordo com um valor hash inserido pelo usuário.

# LINK PARA SITE: https://leopanco.github.io/A3_computacao_grafica/

## Índice
1. [Estrutura do Projeto](#estrutura-do-projeto)
2. [HTML](#html)
3. [CSS](#css)
4. [JavaScript](#javascript)
5. [Dependências](#dependências)
6. [Como Usar](#como-usar)
7. [Melhorias Futuras](#melhorias-futuras)

## Estrutura do Projeto

O projeto consiste nos seguintes arquivos e diretórios:

```
/
├── index.html
├── style/
│   └── style.css
└── script/
    └── main.js
```

## HTML

O arquivo HTML define a estrutura básica da página web, incluindo um contêiner para os elementos SVG e um campo de entrada para o usuário inserir um hash.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SVG A3</title>
    <link rel="stylesheet" href="style/style.css">
</head>
<body>
    <div class="container">
        <div class="container_left">
            <div class="svg-wrapper">
                <div class="svg-container"></div>
            </div>
        </div>
        <div class="inputs">
            <label class="title">Hash:</label>
            <input type="text" class="texto input" value="">
        </div>
    </div>
    <script src="script/main.js"></script>
</body>
</html>
```

## CSS

O arquivo CSS define os estilos para a página, incluindo layout, cores e transições.

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    height: 100vh;
    background: linear-gradient(180deg, #111, #666);
}

.container {
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: space-around;
    flex-direction: column;
}

.container_left {
    width: 100%;
    height: 65%;
    display: flex;
    justify-content: center;
    align-items: center;
}

.inputs {
    background-color: rgba(128, 128, 128, 0.5);
    border: 3px solid #111;
    color: #111;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-around;
    height: 20%;
    width: 50%;
    padding: 1% 0;
    text-transform: uppercase;
    transition: 700ms;
}

.title {
    font-size: 200%;
}

.input {
    height: 40%;
    width: 65%;
    text-align: center;
    font-size: 170%;
}

.svg-wrapper {
    height: 100%;
    width: 50%;
}

.svg-container {
    width: 100%;
    height: 100%;
    position: relative;
}
```

## JavaScript

O arquivo JavaScript contém a lógica para gerar os gráficos SVG com base no hash de entrada do usuário. Ele utiliza a biblioteca SVG.js para desenhar formas e padrões.

### Funções Principais

- **hashStringToIndex**: Converte uma string em um índice de hash.
- **hashStringToNumber**: Converte uma string em um número de hash.
- **getColorIterator**: Gera cores com base na chave hash.
- **widget**: Desenha diferentes formas com base na chave hash.
- **initializeSVG**: Inicializa os elementos SVG e configura os event listeners.

```javascript
/*A parte JavaScript do projeto é responsável por gerar gráficos SVG dinâmicos com base em uma entrada de hash fornecida pelo usuário. Abaixo, explicarei cada função e bloco de código em detalhes:
1. Funções de Hashing
hashStringToIndex(str, modulo)

Esta função converte uma string em um índice de hash dentro de um determinado módulo:

javascript
*/
function hashStringToIndex(str, modulo) {
    let hash = 0;
    for (let i = 0; i < str.length; i++) {
        hash = (hash << 5) - hash + str.charCodeAt(i);
        hash = hash & hash;
    }
    return Math.abs(hash) % modulo;
}

   /* Entrada: Uma string str e um número modulo.
    Processo: Calcula o hash da string deslocando e somando caracteres, e usa a operação de módulo para garantir que o resultado esteja dentro dos limites especificados.
    Saída: Um índice dentro do intervalo de 0 a modulo-1.

hashStringToNumber(str)

Esta função converte uma string em um número de hash:

javascript
*/
function hashStringToNumber(str) {
    let hash = 0;
    for (let i = 0; i < str.length; i++) {
        hash = (hash << 5) - hash + str.charCodeAt(i);
        hash = hash & hash;
    }
    return hash;
}
/*
    Entrada: Uma string str.
    Processo: Calcula o hash da string de maneira semelhante à função anterior, mas retorna o hash completo.
    Saída: Um número inteiro representando o hash da string.

2. Função de Iterador de Cores
getColorIterator(key)

Esta função gera um iterador para produzir cores com base em uma chave hash:

javascript
*/
function getColorIterator(key) {
    const colors = ["red", "green", "blue", "yellow", "purple"];
    let index = hashStringToIndex(key, colors.length);
    let seed = hashStringToNumber(key);

    let next = function() {
        if (index >= colors.length) index = 0;
        return colors[index++];
    };

    let colorIterator = {
        next256: function() {
            seed = (seed * 9301 + 49297) % 233280;
            let value = Math.floor((seed / 233280.0) * 256);
            return Math.abs(value);
        },
        next16: function() {
            seed = (seed * 9301 + 49297) % 233280;
            let value = Math.floor((seed / 233280.0) * 16);
            return Math.abs(value);
        },
        next: function() {
            seed = (seed * 9301 + 49297) % 233280;
            let value = (seed / 233280.0) * 1000 + 1;
            return Math.abs(value);
        }
    };

    return Object.assign(next, colorIterator);
}
/*
    Entrada: Uma string key.
    Processo: Calcula o índice inicial e a semente a partir da chave, fornecendo funções para iterar sobre cores predefinidas e gerar valores numéricos.
    Saída: Um objeto iterador que pode produzir cores e valores numéricos pseudoaleatórios.

3. Função Principal do Widget
widget(key, draw)

Esta função desenha diferentes formas SVG com base na chave hash fornecida:

javascript
*/
function widget(key, draw) {
    let nextColor = getColorIterator(key);
    let icons = ["pacman", "fantasma"];
    let type_icon = icons[nextColor.next16() % icons.length];

    if (type_icon === "pacman") {
        let pos_pacman = [1, 2, 3, 4];
        let type_pos = pos_pacman[nextColor.next16() % pos_pacman.length];

        if (type_pos == 1) {
            let boca_pacman = `rgb(${nextColor.next256()},${nextColor.next256()},${nextColor.next256()})`;
            let square = draw.rect(500, 500).move(0, 0).fill(`rgb(${nextColor.next256()},${nextColor.next256()},${nextColor.next256()})`);
            let square1 = draw.rect(500, 500).move(500, 0).fill(`rgb(${nextColor.next256()},${nextColor.next256()},${nextColor.next256()})`);
            let square2 = draw.rect(500, 500).move(500, 500).fill(boca_pacman);
            let square3 = draw.rect(500, 500).move(0, 500).fill(`rgb(${nextColor.next256()},${nextColor.next256()},${nextColor.next256()})`);
            draw.circle().size(500).move(250, 250).fill(`rgb(${nextColor.next256()},${nextColor.next256()},${nextColor.next256()})`).opacity(1);
            draw.circle().size(70).move(500, 330).fill(nextColor()).opacity(1);
            draw.polygon("800,600 500,500 600,800").fill(boca_pacman);
        }
        // Condições adicionais para //outras posições (2, 3, 4)
    } else if (type_icon === "fantasma") {
        let pos_fantasma = [1, 2];
        let type_pos = pos_fantasma[nextColor.next16() % pos_fantasma.length];

        if (type_pos == 1) {
            let square = draw.rect(500, 500).move(0, 0).fill(`rgb(${nextColor.next256()},${nextColor.next256()},${nextColor.next256()})`);
            let square1 = draw.rect(500, 500).move(500, 0).fill(`rgb(${nextColor.next256()},${nextColor.next256()},${nextColor.next256()})`);
            let square2 = draw.rect(500, 500).move(500, 500).fill(`rgb(${nextColor.next256()},${nextColor.next256()},${nextColor.next256()})`);
            let square3 = draw.rect(500, 500).move(0, 500).fill(`rgb(${nextColor.next256()},${nextColor.next256()},${nextColor.next256()})`);
            let square_big = draw.rect(750, 750).move(120, 150).fill(`#111`).opacity(0.5);
            let cor_fantasma = `rgb(${nextColor.next256()},${nextColor.next256()},${nextColor.next256()})`;
            draw.circle().size(500).move(250, 200).fill(cor_fantasma).opacity(1);
            draw.rect(500, 350).move(250, 450).fill(cor_fantasma);
            let olho_fantasma = `rgb(${nextColor.next256()},${nextColor.next256()},${nextColor.next256()})`;
            draw.circle().size(120).move(350, 400).fill("white").opacity(1);
            draw.circle().size(70).move(375, 425).fill(olho_fantasma).opacity(1);
            draw.circle().size(120).move(550, 400).fill("white").opacity(1);
            draw.circle().size(70).move(575, 425).fill(olho_fantasma).opacity(1);
        }
    }
    // Código omitido para outras posições e condições
}

    Entrada: Uma string key e um objeto draw do SVG.js para desenhar as formas.
    Processo: Determina o tipo de ícone (pacman ou fantasma) e as características visuais (cores, posições) usando o iterador de cores. Desenha as formas SVG correspondentes com base na chave.
    Saída: Desenha gráficos SVG dinâmicos na página.

4. Inicialização e Event Listener
initializeSVG()

Esta função inicializa os elementos SVG e configura os event listeners para reagir à entrada do usuário:

javascript

function initializeSVG() {
    let draw = SVG().addTo('.svg-container').size('100%', '100%');
    let input = document.querySelector('.texto.input');
    input.addEventListener('input', function() {
        draw.clear();
        widget(input.value, draw);
    });
}
initializeSVG();

    Processo:
        Cria um elemento SVG dentro do contêiner especificado.
        Adiciona um event listener ao campo de entrada para reagir às mudanças de texto.
        Limpa o SVG e redesenha as formas sempre que o usuário digita um novo hash.

Resumo

O JavaScript do projeto consiste em funções para calcular hashes a partir de strings, gerar cores e valores pseudoaleatórios, e desenhar formas SVG com base nesses valores. A lógica principal está na função widget, que decide quais formas desenhar e como estilizar com base na entrada do usuário. A função initializeSVG configura a página para reagir às entradas do usuário e atualizar os gráficos SVG dinamicamente.
