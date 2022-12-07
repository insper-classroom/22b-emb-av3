# 22b - AV3 - Genius

Nesta avaliação vocês irão recriar o jogo Genius (Simon), só que com 3 botões no lugar de 4.

![](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fmedia.mileskimball.com%2Fimages%2Fp350854b.jpg&f=1&nofb=1&ipt=e6f6666673727671295f10315993e6429a66d375e9e2838db6ca4fc0ec662719&ipo=images)

## Descricão

Periféricos:

- PIO
- TC
- OLED

freeRTOS:

- Task
- Queue

### Genius

O jogo consiste em decorar e reproduzir uma sequência que é apresentada de forma visual e a cada nível a sequência fica maior. Se o jogador continua até o jogador errar a ordem.

### Funcionalidades

Para implementarmos o protótipo do jogo iremos usar a placa OLED (LEDs e botões):

![](oled.png)

Para cada LED / botão apertado o jogo deve tocar uma frequência específica no buzzer, o som deve ser reproduzido enquanto o LED estiver aceso. 

O OLED irá exibir o nível atual do jogo e também uma mensagem de erro se o jogar errar e acabar o jogo. O fim de jogo deve acontecer quando o jogador:

1. Apertar um botão enquanto a sequência ainda está sendo exibida
1. Errar a ordem
1. Se o jogador não apertar um botão em 500ms deve dar erro
1. Apertar um botão a mais

Se o jogador fizer a sequência correta, o jogo deve partir para a próxima fase (incremental: 1, 2, 3 ...).

### Firmware

Vocês devem desenvolver o firmware como indicado a seguir:

![](firmware.png)

- **O código base fornecido é o `RTOS-OLED-Xplained-Pro` já com o RTT e TC adicionado no wizard.**

Onde:

- Botões
  - devem ser configurados para funcionar com `callback`
  - devem enviar o ID `int` para a fila `xQueueBtn` 

- LEDs
  - Exibem a sequência a ser seguida
  
- OLED
  - Exibe o nível **atual** e **FIM DE JOGO** quando acabar

- Buzzer
  - Uma frequência por botão: `[2000 2500 3000]`
  - Vibrar enquanto o LED estiver aceso
  - Usar algum timer para vibrar (TC ou RTT)

- `task_game`
  - Responsável por implementar toda a lógica do jogo
  - Lê os botões do usuário via `xQueueBtn`
  - Aciona os LEDs 
  
- `TC1_Handler`
  - Vibrar buzzer na frequência definida para cada LED 

- `int genius_get_sequence(int level, int *sequence)`
  - **Já foi dada pronta, mas é necessário analisar para entender**
  - Função que deve ser utilizada para definir a sequência dos LEDs de acordo com o nível atingido pelo jogador
  - `return int`: Tamanho da sequência 
  - `int level`: nível de `0` até `512`
  - `int *sequence`: Deve ser um endereço de memória de um vetor, onde a sequência será salva.

### Dicas

Uso do `genius_get_sequence()`:

``` c
int nSequence = 0;
int sequence[512];
int level = 0;
nSequence = genius_get_sequence(level, sequence);
```

Passos:

1. Crie a `task_game` e usando a função `genius_get_sequence` crie uma sequência (com a função `genius_get_sequence`) e exiba nos LEDs a sequência
  - Lembre de configurar os botões (`xQueueBtn`) e LEDs
  - Eu testei aqui e o led aceso por `100ms` funciona bem.
1. Usando o TC faća o buzzer vibrar na frequência associada ao LED aceso 
  - Você vai precisar usar o `TC_init` e `tc_start` para comecar tocar o som e `tc_stop` para parar!
  - O buzzer tem que vibrar com no `TC1_Handler`
  - Use o `pin_toggle` para isso dentro do handler!
1. Exiba no OLED o nível atual
1. Comece ler a fila `xQueueBtn` e implemente a lógica do jogo
1. Não esqueça do `timeout` do botão
1. Exiba no OLED se o jogar errou ou se vai comecar um nível novo
  - De um pouco de tempo para o jogar pensar entre um nível e outro 

Agora tudo deve estar funcionando, valide todos os requisitos.
