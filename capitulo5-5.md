# Videoaula 5 do Capítulo 5: Protocolo TCP

- Orientado à conexão
  - Apresentação (troca de mensagens de controle)
  - Inicia o estado do transmissor e do receptor antes da troca de dados
    - Buffers de transmissão e de recepção
- Ponto-a-ponto
  - Um transmissor, um receptor
- Dados full-duplex
  - Transmissão bidirecional na mesma conexão

- Pipelined
  - Transporte confiável de pacotes via Acks
  - Controle de congestionamento e de fluxo definem tamanho da janela
- Fluxo de bytes sequencial (em ordem)
- Controle de fluxo
  - Transmissor não esgota a capacidade do receptor
- Controle de congestionamento
  - Tenta evitar a sobrecarga da rede

## Buffer de emissão e recepção

- Processo Cliente envia uma string de dados através da porta do processo
- String de dados é entregue ao processo TCP
  - Que envia a string para o buffer emissor TCP
  - De tempos-em-tempos pedaços de dados são extraídos do buffer
    - MSS: tamanho máximo do segmento - tamanho escolhido para evitar fragmentação no IP
  - TCP adiciona o cabeçalho formando o segmento TCP
  - Envia via rede para o buffer receptor TCP
- No host receptor
  - Segmento é colocado no buffer receptor TCP
  - Aplicação lê dados
- Tanto no emissor como receptor existem buffers de emissão e recepção
  - Fluxo de dados bidirecional na mesma conexão

## Número de sequência

### Tratamento do tamanho dos segmentos

- TCP deve manipular a diversidade de tamanhos das mensagens geradas pelas aplicações
- Blocos de dados muito grandes
  - devem ser fragmentados
- Blocos de dados pequenos
  - TCP "bufferiza" os segmentos para conduzi-los juntos num mesmo segmento TCP
  - Redução do "overhead" de transmissão

### Numeração dos segmentos

- Conceitualmente cada byte da mensagem é associado a um número de sequência
- Número de sequência do primeiro byte dos dados contidos em um segmento é transmitido junto com o segmento e é denominado de *sequência do segmento*

### Sequenciamento

- Número de sequência é usado para ordenar os segmentos que porventura tenham sido recebidos fora de ordem e para eliminar segmentos duplicados

Exemplo Segmento de 3000 bytes fragmentado em 3 datagramas de 1000 bytes (MSS)

Datagrama 1: Num. Seq. = 0
Datagrama 2: Num. Seq. = 1000
Datagrama 3: Num. Seq. = 2000

## Estabelecimento de conexão

- Usa os bits R (RST), S (SYN) e F (FIN) da estrutura TCP.
- Conexão apresentação de três vias (3-way handshake)

1. Socket clientSocket = new Socket (“nomeHost”, numPorta)
2. Conexão é estabelecida
3. Cliente envia segmento TCP especial
4. Servidor responde com outro segmento especial
5. Cliente responde com outro segmento especial
6. Carrega carga útil (dados da aplicação)

## Gerenciamento da conexão TCP

- Emissor e Receptor TCP estabelecem uma conexão antes da troca de segmentos
- Inicializa variáveis TCP
  - Números de sequência inicial
  - Buffers, informações de controle de fluxo (e.g. RevWindow)
  - Etc
- Cliente: Inicializador da conexão
  - Socket clientSocket = new Socket(“nomeHost",“numPorta");
- Servidor: contactado pelo cliente
  - Socket connectionSocket = welcomeSocket.accept();

- **Algoritmo three-way handshake**
  - Detalhado na página 65 dos slides

### Fechando uma conexão

- Cliente fecha socket: clientSocket.close();

1. cliente envia segmento de controle TCP FIN para o servidor
2. servidor recebe FIN, responde com ACK e envia FIN.
3. cliente recebe FIN, responde com ACK. (inicia espera)
4. receptor recebe ACK. Conexão fechada.

## Reconhecimento

- Número do próximo byte aguardado pelo receptor
- Consiste do número de sequência do próximo byte que a entidade TCP emissora espera receber do TCP receptor
  - Exemplo: Se número de reconhecimento X for transmitido no ACK
    - Indica que a entidade TCP receptora recebeu corretamente os bytes com número de sequência menores que X
    - Ela espera receber o byte X na próxima mensagem

- Segmentos carregam de carona (piggybacking) um reconhecimento

---
Número de sequência: Número do primeiro byte da string  
Reconhecimento: Número de sequência do próximo byte esperado  
Sequenciamento: especificação do TCP não define, deixado ao implementador.

---

---
Se A envia um pacote com número de sequência 222, e este pacote contém 12 bytes, qual será o valor do campo número de reconhecimento confirmando a entrega deste pacote?

222+12=234

---

## Retransmissões

- Quando uma entidade TCP transmite um segmento
  - Ela coloca uma cópia do segmento em uma fila de retransmissão e dispara um temporizador
  - Caso o reconhecimento do segmento é recebido, o segmento é retirado desta fila
  - Caso o reconhecimento não ocorra antes do temporizador expirar, o segmento é retransmitido

## Número de Sequência e de Reconhecimento

- Números iniciais são escolhidos aleatoriamente no estabelecimento da conexão
  - De maneira e evitar quebras de criptografia
  - Feito para minimizar a possibilidade que um segmento inválido seja confundido como válido
  - Que pertencente a uma conexão já terminada e chegue no receptor após estabelecida uma nova conexão entre os mesmos receptor e transmissor na mesma porta

## TCP Geração do Reconhecimento [RFC 1122, RFC 2581]

- Evento: Chegada de segmento em ordem, sem gaps, segmentos anteriores já confirmados
  - Ação do receptor TCP: ACK atrasado. Aguarda até 500ms pelo próximo segmento. Se o segmento não chegar, enviar ACK.

- Chegada de segmento em ordem, sem gaps, existe ACK atrasado
  - Ação do receptor TCP: Enviar imediatamente um único ACK acumulativo

- Evento: Chegada do segmento fora de ordem maior que o número de seq esperado (gap detectado)
  - Ação do receptor TCP: Envia ACK duplicado, indicando o número de seq. do próximo byte esperado

- Evento: Chegada de um segmento que preenche parcialmente ou complentamente um gap
  - Ação do receptor TCP: ACK imediato se o segmento inicia na parte inicial mais baixa do gap

---
Se A envia para B dois pacotes com 10 bytes cada. O primeiro tem número de sequência 100, e o segundo com número de sequênca 110. Para A ter esses dois pacotes confirmados, ele deve receber um de B:

[x] Um pacote com número de reconhecimento maior que 120.

[ ] Um pacote com número de reconhecimento igual a 110.

[ ] Dois pacotes, um com número de reconhecimento igual a 100 e outro igual a 110.

---
