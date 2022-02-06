# Videoaula 4 do Capítulo 5: Transferência confiável de dados (Continuação)

## Desempenho do rdt3.0

- rdt3.0 funciona, mas o desempenho é sofrível

Exemplo: enlace de 1 Gbps, 15 ms de atraso de ida ao destino, pacotes de 1 KB:

Serialização = L(tamanho do pacote em bits)/R(taxa de transmissão em bps) = (1024*8 bits/pkt)/10^9 b/s = 8 microssed

U remet = (L/R) / (RTT+L/R) = 0,008/30,008 = 0,00027

U remet: utilização — fração de tempo do transmissor ocupado

Um pacote de 1 KB cada 30 ms -> 273kbps de vazão sobre um canal de 1 Gbps

O protocolo de rede limita o uso dos recursos físicos!

- Existe um tempo longo (a nível de rede) para o pacote chegar no receptor e o transmissor receber a confirmação da entrega deste pacote. E neste tempo o transmissor não usa a rede.

## Protocolos com paralelismo (pipelining)

- Transmissor envia vários pacotes ao mesmo tempo, todos esperando para serem reconhecidos

- Faixa de números de sequência deve ser aumentada

- Duas formas genéricas de protocolos com paralelismo
  - go-Back-N
  - retransmissão seletiva

Se mandarmos 3 pacotes por vez

U remet = 3 * (L/R) / (RTT+L/R) = 0,024/30,008 = 0,0008

## Go-Back-N

### Transmissor GBN

- Número de sequência com k bits no cabeçalho do pacote
- “janela” de até N pacotes que podem ser enviados sem esperar confirmação dos anteriores
- ACK(n) Cumulativo: reconhece todos os pacotes até o número de sequência confirmado
- Janela desliza na confirmação do(s) pacote(s)
- Lança temporizador para cada pacote enviado
  - Tempo de confirmação (do pacote n): retransmite pacote n e todos os pacotes com número de sequência maior que estejam dentro da janela

### Receptor GBK

- Pacotes fora de ordem
  - Descarta (não armazena)
  - Reconhece pacote com o mais alto número de sequência em ordem

## Retransmissão seletiva

### Transmissor seletivo

- Janela de transmissão
  - N números de sequência consecutivos : limita a quantidade de pacotes enviados sem confirmação
- Transmissor temporiza cada pacote não reconhecido
- Somente reenvia os pacotes para os quais um ACK não foi recebido

### Receptor seletivo

- reconhece individualmente todos os pacotes recebidos corretamente
- Armazena pacotes, quando necessário, para eventual entrega em ordem para a camada superior
- Tem janela de recepção
  - Pacotes recebidos são buferizados
