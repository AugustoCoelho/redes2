# Videoaula 6 do Capítulo 5: TCP (Continuação)

## Temporização

- Não é sempre o mesmo tempo de timeout
- EstimatedRTT: Tempo de RTT (ida e volta) é estimado de tempo em tempo
- Esse valor não é usado diretamente, precisamos ter uma margem de segurança.
  - Tem horas que é preciso uma margem maior ou menor
    - Muita variação na rede
  - Temporização = RTT estimado + 4 * Desvio

## Controle de fluxo

- Pra não mandar mais dados que o receptor não pode receber em um instante
- Receptor envia tamanho da janela (tamanho do espaço livre no buffer) com o ACK.
- *Speed-matching*
  - Informações são enviadas mais devagar se a leitura é lenta

## Congestionamento

- Manifestações
  - Atrasos
  - Pacotes perdidos (quando a fila ficar cheia e chegar mais pacotes)
- Controle fim-a-fim (sem assistência da rede)
- Detecta o congestionamento por um evento de perda
  - Timeout do temporizador
  - 3 ACKs duplicados

Três mecanismos:

- AIMD
  - Additive increase, multiplicative decrease
  - Formato de "serra"

- Partida lenta
  - Crescimento exponencial no tamanho da janela de congestionamento
    - Até ocorrer perda
    - ou Até chegar a um limiar
  - Depois cresce linearmente
    - Até ocorrer perda
      - Tamanho da janela é resetado
  - Algoritmos
    - Tahoe (explicado anteriormente)
      - Muitos retornos à partida lenta
    - Reno
      - Opera melhor em redes com muitas perdas
      - Recuperação rápida: Cancela a fase de partida lenta após uma retransmissão rápida quando ocorre 3 ACKs
      - Em timeout retorna ao início
    - Vegas
      - Aumenta o desempenho do Reno
      - Tenta evitar congestionamento mantendo uma boa vazão
      - Detecta congestionamento antes de ocorrer, e reduz a taxa linearmente
      - Perda eminente é prevista pelo atraso de ida e volta
    - Vários outros. SO que decide qual usado.

- Reação a eventos de esgotamento de temporização

## Equidade

- Divide a banda de acordo com a quantidade de conexões que um programa solicitar
- UDP não tem equidade
