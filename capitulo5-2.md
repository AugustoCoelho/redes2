# Videoaula 2 do Capítulo 5: Protocolo UDP

- Protocolo de transporte da Internet mínimo, “sem frescura”
- Serviço “melhor esforço”. UDP
  - Não garante taxa, atraso e taxa de perdas de pacotes
  - Pacotes podem ser entregues à aplicação fora de ordem do envio

- Sem conexão
  - não há “setup” UDP entre remetente e receptor
  - tratamento independente de cada datagrama UDP pela rede

- Permite transmissão em multicast

- Permite enviar a uma taxa determinada

- Não permite o receptor ser informado da entrega do pacote

## Por que existe UDP?

- Elimina estabelecimento de conexão (o que pode causar retardo). Um exemplo é o DNS
- É um protocolo simples, não se mantém “estado” da conexão no remetente/receptor
  - Latência menor e uso menor de memória
- Usa mais eficientemente a banda da rede
  - Cabeçalho por segmento é menor
  - Sem controle de congestionamento: permite usar a banda de maneira mais eficiente
  - Mas pode provocar taxa de perdas altas
- Muito usado para aplicações multimídia de streaming
  - Tolerantes a perda e Sensíveis a taxa
- Outras aplicações que usam UDP: DNS, NFS, SNMP e Protocolo de roteamento (RIP)
- É possível fazer uma transferência confiável sobre UDP: adicionar confiabilidade na camada de aplicação
  - Recobrimento de erro específico de aplicação

## Formato

<--- 32 bits --- >  
Porta origem | Porta destino  
Tamanho | Checksum  
Dados

**Porta Origem e Porta Destino**: identificam o processo de aplicação que está enviando dados e o processo de aplicação que irá receber os dados. 16 bits cada.

**Tamanho** é representa o tamanho total do frame UDP

**Checksum** é calculado usando o header UDP e também a área de dados, e destina-se a verificação de erros de transmissão.

## Tamanho máximo do datagrama

- Teoricamente o tamanho máximo é de 64K, porque o campo tamanho é de 16 bits.

- Mas deve-se considerar que no IP o tamanho também é de 16 bits, e estão sendo calculado
  - Tamanho do Cabeçalho IP (20 bytes)
  - Cabeçalho UDP (8 bytes)
- Assim, o **tamanho máximo é de 65507 bits**
- Mas geralmente evita-se fragmentação do pacote UDP na fonte, assim o tamanho do quadro (camada 2) limita o tamanho do pacote UDP

---
**Exemplo**: Ethernet 802.3

O payload é de 1500 Bytes, cabeçalho IP = 20B, cabeçalho UDP=8B

Tamanho máximo de dados para não ocorrer fragmentação é de 1500-28 = 1472B

---

## Checksum UDP

- Detecta erros no segmento transmitido

- Trata conteúdo do segmento como uma sequência de inteiros de 16 bits

- checksum: adição do conteúdo do segmento, resultado é complementado de 1

- Emissor coloca o valor checksum no campo UDP checksum

- Receptor
  - Soma toda a sequência (incluindo checksum)
  - Se tudo for 0000 hex, checksum será FFFF hex.

- Checksum é opcional
  - Checksum = 0, não executa verificação
  - Checksum != 0, executa verificação

### Checksum e pseudocabeçalho

- UDP Checksum cobre dado da aplicação, cabeçalho UDP, um pseudocabeçalho

- Propósito de incluir o pseudo-cabeçalho
  - Checar se o pacote chegou no destino correto
  - Checar se o IP entregou para o protocolo correto (UDP/TCP)

Pseudocabeçalho: Endereço IP fonte (32bits), Endereço IP destino (32 bits), protocolo (8 bit), tamanho total (16 bits)

Cabeçalho UDP: porta fonte (16 bits), porta destino (16 bits), tamanho total UDP (16 bits), checksum (16 bits)
