# Videoaula 1 do Capítulo 5: Introdução

## Dois protocolos de transporte na pilha TCP/IP: TCP e UDP

**Finalidade**: Fornece comunicação lógica entre processos de aplicação rodando em hosts diferentes

## Protocolos de transporte rodam em sistemas finais

- **Lado emissor**: quebra as mensagens da aplicação em segmentos e envia para a camada de rede
- **Lado receptor**: remonta os segmentos em mensagens e passa para a camada de aplicação

## Diferenças entre camada de transporte e de rede

- **Camada de rede**: comunicação lógica **entre os hospedeiros** (computadores). Mais precisamente: **entre interfaces de rede** (equipamentos podem ter mais de uma interface!)

- **Camada de transporte**: comunicação lógica **entre os processos**. Depende dos serviços da camada de rede

## Serviços de transporte da Internet:

- TCP: Confiável, liberação **em ordem** unicast
  - Configuração de conexão
  - Sequenciamento de bytes, garante que pacotes sejam entregues em ordem
  - Controle de fluxo e de congestionamento, controla também de acordo com a capacidade de recepção
  - **Não possui** serviço confirmando que confirma a entrega de pacotes
  - **Não garante taxa de bits**

- UDP: Não confiável, liberação **fora de ordem** unicast ou multicast
  - **Envio sem garantia de entrega**
  - **Não garante entrega em uma determinada taxa**
  - Mas permite **enviar a uma determinada taxa**

- **Serviços não disponíveis** no TCP e UDP:
  - Tempo-real/atraso
  - Garantias de banda
  - Multicast confiável

## Multiplexação/demultiplexação

- Demultiplexão no hospedeiro receptor: a partir de uma unica mensagem, conseguir mandar para um processo de destino
  - Entrega os segmentos recebidos ao socket correto

- Multiplexação no hospedeiro emissor: coleta dados de múltiplos sockets, envelopa os dados com cabeçalho (usado depois para demultiplexação). Usar uma única interface de rede para vários processos.

- Baseado no número da porta do emissor e receptor, e endereços IP
  - Números de portas origem e destino em cada segmento (16 bits: 0..65535)

Número da porta destino: identifica o processo receptor da mensagem

Endereço IP: identifica o computador receptor na Internet

### Portas de protocolo

- Última fonte/destino de/para uma mensagem é uma porta de protocolo
  - Um processo envia via/ouve uma porta de protocolo (identificado por um inteiro)
  - Muitos sistemas operacionais fornecem acesso síncrono as portas (Um processo pode se bloquear aguardando chegada de mensagens na porta)

- Em geral, portas são bufferizadas
  - Dados chegando antes da operação de leitura de um processo é colocada em uma fila (finita)

- Para se comunicar com uma porta, um emissor necessita conhecer o Endereço IP e a Porta do processo receptor
  - Combinação de endereço IP e porta é chamado de socket

### Número de portas

- 1-1023: Portas bem conhecidas, atribuídas pela Internet Assigned Numbers Authority (IANA)
- 1024-49151: Portas registradas
- 49152-65535: Portas dinâmicas

- Servidores são normalmente conhecidos por portas bem conhecidas (por
exemplo, 80 para HTTP)
- Portas dinâmicas podem ser usados por qualquer processos (normalmente usados por processos clientes)

### Demultiplexação não orientada à conexão

1. Emissor e receptor criam sockets com números de porta
2. Socket UDP identificado pela porta que ele utiliza.
3. Na recepção o hospedeiro:
4. Verifica o número da porta de destino no segmento
5. Direciona o segmento UDP para o socket com este número de porta

### Demultiplexação orientada à conexão

1. No servidor (servidor.com.br) é instanciado um ServerSocket
2. No cliente se comunicando com servidor é instanciado um **socket de conexão**

- Hospedeiro servidor pode suportar vários sockets TCP simultâneos

- Cada **socket de conexão** é identificado pelos seus próprios 4 valores
  - End. IP de origem
  - End. porta de origem
  - End. IP de destino
  - End. porta de destino
  - Hospedeiro receptor usa os quatro valores para direcionar o segmento ao
socket apropriado

- Servidores Web possuem sockets diferentes para cada cliente conectado

- HTTP não persistente terá um socket diferente para cada requisição

### Diferença entre as duas

- Não há muita diferença 

- Demux não orientada a conexão: o processo não precisa identificar a conexão
  - Vários computadores podem mandar para um destino
  - Precisa apenas da porta de destino

- Demux orientada a conexão: Precisa do End. IP de origem, End. porta de origem, End. IP de destino, End. porta de destino