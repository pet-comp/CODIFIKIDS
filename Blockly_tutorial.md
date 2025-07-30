# Customização no Blockly Games

O Blockly-Games, bem como a maioria das aplicações de programação em blocos, é baseada na biblioteca Blockly, o código é open source e pode ser acessado pelo [git](https://github.com/NeilFraser/blockly-games.git)

## Primeiros passos

O primeiro passo para customizar o Blockly Games é rodar o programa localmente. Siga os passos abaixo:

1.  **Faça uma cópia do repositório do Git:**

    ```bash
    git clone https://github.com/NeilFraser/blockly-games.git
    ```

    Execute este comando dentro da pasta onde você deseja armazenar o código do programa.

2.  **Entre na pasta:**

    ```bash
    cd blockly-games/
    ```

3.  **Verifique as dependências:**

    ```bash
    make deps
    ```

    Caso o comando `python` não seja reconhecido, e você tem ele instalado, instale a biblioteca `python-is-python3`. Isso acontece porque nas versões mais recentes de python o comando padrão para acessa-lo é `python3`

    ```bash
    sudo apt update
    sudo apt install python-is-python3
    ```

4.  **Construa os jogos:**

    ```bash
    make games
    ```


5.  **Crie um servidor local:**

    ```bash
    python3 -m http.server 8000
    ```

    Isso criará um servidor local na porta 8000. Para acessá-lo no navegador, vá para `http://localhost:8000/` e complete o link com o caminho do arquivo que você quer ver.

### Locais importantes

  #### Página inicial dos jogos:

  `blockly-games/server/html/index.html`

  Então se você abriu o servidor na pasta `blockly-games` para acessar os jogos você deve colar no seu navegador:

  `http://localhost:8000/server/html/index.html`


  #### Página admin:

  `blockly-games/server/html/admin.html`

  No modo de debug, selecione a opção `Use slow uncompressed code (Hackers only)`. Isso fará com que o site seja recompilado automaticamente a cada refresh, evitando a recompilação manual.

### Dicas e problemas comuns:

  1. É conveniente criar o servidor dentro da pasta `blockly-games/server/html`, visto que os dois locais mais importantes estão nessa pasta, então seu site ficará bem mais curto, ex: `http://localhost:8000/index.html`.

  2. `make: command not found`: Instale com `sudo apt install build-essential`.

  3. `python: command not found`: Instale com `sudo apt install python-is-python3`.

  4. Mudanças não aparecem?: Ative o modo debug em `admin.html` e recarregue a página.

## Como exportar as alterações feitas?

Duas soluções são propostas:

1.  Salvar o código alterado (por exemplo, no Git) e instalá-lo localmente nas máquinas.
2.  Hospedar o site localmente.

### Hospedagem local (acesso na mesma rede)

Para compartilhar o site com pessoas conectadas à mesma rede, use o seguinte comando:

```bash
python3 -m http.server 8000 --bind 0.0.0.0
```

Isso permitirá o acesso através de um link como `http://192.168.0.123:8000/server/html/index.html`. Substitua `192.168.0.123` pelo seu IP, que pode ser descoberto com o comando `hostname -I`.

### Hospedagem com acesso externo (ngrok)

Para acesso em redes externas, use o ngrok para criar uma conexão segura entre a internet e o seu computador.

1.  Instale a aplicação do ngrok a partir do link: `https://ngrok.com/download`.
2.  Crie uma conta em: `https://ngrok.com/`.
3.  No terminal, configure seu token de autenticação:
    ```bash
    ./ngrok config add-authtoken SEU_TOKEN_AQUI
    ```
    **Este procedimento não precisa ser repetido.**
4.  Crie o servidor local aberto para a sua rede, como na opção anterior.
5.  Abra um túnel com o ngrok:
    ```bash
    ngrok http 8000
    ```
    Isso irá gerar um link público como `https://random-name.ngrok.io`. 
    
    Agora basta completar com o local desejado como na hospedagem local.

## Arquivos principais de um jogo

Cada jogo possui três aquivos principais:

`blocks.js`: Define os blocos e seus comportamentos.

`html.js`: Define como o jogo irá aparecer (isso inclui quais blocos irão aparecer em cada fase) .

`main.js`: Define todo o comportamento do jogo.

Esses três arquivos ficam dentro da pasta `scr`, que fica dentro da pasta do respectivo jogo, dentro da pasta `html`, que fica dentro da pasta `server`.

Portanto o caminho para esses arquivos será: `blockly-games/server/html/nome_do_jogo/src`

## Personalizações no jogo do labirinto

### Alterar um mapa

A partir da linha 100 do arquivo `main.js`, os mapas dos 10 níveis são definidos. Os números representam:

  * `0` = parede
  * `1` = caminho
  * `2` = Ponto inicial do personagem
  * `3` = Chegada do personagem

Basta alterar o código do mapa, salvar e atualizar o site.

### Alterar sprites

Os sprites dos personagens jogáveis estão na pasta `blockly-games/server/html/maze`. Para alterá-los, basta substituir a imagem original por uma nova, mantendo o mesmo nome.

### Adicionar uma skin

A partir da linha 43 do `main.js`, as skins são definidas. Para criar uma nova skin, adicione uma nova estrutura seguindo o formato existente.

### Alterar o limite de blocos

Na linha 36 do arquivo `main.js`, há um vetor que define o limite de blocos para cada fase. Para alterar o limite, basta modificar este vetor.

### Alterações em blocos

Os blocos do labirinto estão no arquivo `blockly-games/server/html/maze/blocks.js`. A [documentação da biblioteca Blockly](https://developers.google.com/blockly/guides/get-started/blocks?hl=pt-br#block_definition) pode ser consultada para aprender mais sobre como criar ou modificar blocos.

#### Alterar o nome de um bloco

Existem duas opções para alterar o nome de um bloco:

1.  **Força bruta**: A partir da linha 71 do `blocks.js`, a propriedade `"message0"` define o nome do bloco. Você pode substituir a função de tradução por uma string fixa.

2.  **Trocando a tradução**: O arquivo `pt-br.json`, localizado em `blockly-games/json`, contém a tradução de todas as mensagens. Para alterar a tradução, modifique o valor associado à mensagem no arquivo. 
**Importante**: Após a alteração, é necessário recompilar o código manualmente. Feche o servidor, execute `make maze` e reabra o servidor.

#### Alterar a cor de um bloco

As cores dos blocos são definidas na mesma estrutura do arquivo `blocks.js`, no campo `color`. Esse campo está vinculado a um número de 0 a 360 que corresponde ao “hue” de uma cor no sistema HSV (hue saturation value). A saturação e o valor(brilho) são fixos, o hue é quantos graus da origem de um círculo cromático ele está

Cada bloco está vinculado a um tipo de bloco, como blocos de ação, blocos de repetição e assim por diante. DEssa forma, é possível alterar tanto a cor de um conjunto de blocos, como de um bloco em específico.

  * **Bloco específico**: Substitua a constante de cor por um número entre 0 e 360.
  * **Conjunto de blocos**: Altere o valor do "hue" para um determinado tipo de bloco a partir da linha 25.

#### Alterar a funcionalidade de um bloco

O código dos blocos é uma função definida a partir da linha 181 do arquivo `blocks.js`. Para alterar a funcionalidade, modifique o que a função retorna. Por exemplo, é possível fazer um bloco `mover_para_frente` mover o personagem infinitamente, alterando a função para `return 'while(1) moveForward('block_id_${block.id}');\n';`.

Obs: Note que ao andar infinitamente, ele não para ao chegar no destino e perde.

### Definindo os blocos permitidos em uma fase

A toolbox dos níveis é definida a partir da linha 73 do arquivo `blockly-games/server/html/maze/src/html.js`.

### Criando um novo bloco

1.  **Defina como o bloco será exibido**: Adicione um novo objeto JSON no arquivo `blocks.js` a partir da linha 71, seguindo o formato existente. Aqui será definido se ele irá pedir algum valor ao usuário, qual o nome dele, etc.
2.  **Defina o código do bloco**: A partir da linha 181 do `blocks.js`, adicione o comportamento do seu novo bloco. (o código em Java Script que ele retorna)
3.  **Ative o bloco nas fases**: Adicione seu novo bloco à toolbox das fases no arquivo `html.js`, a partir da linha 73, para que ele esteja disponível.

## O que falta descobrir

  * Como criar novas fases.
  * Como criar novos jogos.
  * Como mesclar os jogos em uma seleção.
  * Como adicionar um tipo de bloco diferente.