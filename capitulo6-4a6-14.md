# Videoaula 4 a 14 do Capítulo 6

## Videoaula 4 do Capítulo 6: NAT (Network Address Translator)

- Permite conectar IP privados à internet

- Modifica IP de acordo com tabela

## Videoaula 5 do Capítulo 6: Protocolo IP e seus Serviços

- Fragmentação e remontagem, etc

## Videoaula 6 do Capítulo 6: ICMP e Traceroute

- ICMP: Usado por hosts e routers para comunicações a nível de rede
  - Relato de erros
  - Ping

- Traceroute
  - Usa campo de cabeçalho TTL para limitar o número de roteadores em cada rodada de teste de atraso

## Videoaula 7 do Capítulo 6: Arquitetura de um Roteador

- Executa algoritmos/protocolos de roteamento
- Comuta datagramas de enlaces/porta de entrada para enlaces/porta de saída. Elemento de Comutação.

## Videoaula 8 do Capítulo 6: Roteamento

- Principal função do protocolo IP
- Roteadores escolhem caminho de menor custo
- Informações mantidas em **tabelas de rotas**
  - Tabelas estáticas ou dinâmicas
- Se está na mesma rede, envia ARP para obter MAC destino e depois envia o pacote
  - Senão, envia ARP pra descobrir o roteador da rede, envia pacote com endereço destino do computador destino, mas com endereço MAC do roteador
    - Roteadores CISCO descartam o pacote e enviam ARP pra descobrir destino
    - Ping falha, mas na próxima execução do comando o roteador vai ter o MAC do destino e funciona.
- Algorimos de roteamento link state (info global, tem todas informações dos caminhos), distance vector (info descentralizada, só tem informações sobre vizinhos e enlaces)

## Videoaula 9 do Capitulo 6: Algoritmos de roteamento: Link State e Distance Vector

- Link state: Info global, tem todas informações dos caminhos. Topologia e custo.
- Distance vector: info descentralizada, só tem informações sobre vizinhos e enlaces

### Link state

- Algoritmo Dijkstra
  - Pode ter oscilações
  - Mais robusto comparado ao distance Vector

### Vetor de distância (Distance Vector)

- Bellman-Ford
  - Loops
  - Problema de contagem ao infinito
  - Propaga erros

## Videoaula 10 do Capítulo 6: Protocolos de Roteamento RIP e OSPF

### RIP

- Usa Distance Vector
  - Custo em número de saltos
  - Maior custo possível é 15 (não suporta mais que isso)
    - 16 representa infinito

- Mensagens RIP são UDP
- Nem sempre traz bons resultados
- Usa muita largura de banda

### OSPF (Open Shortest Path First)

- Usa Link State
- Usa IP diretamente Não usa UDP ou TCP
- Usa multicast
- Usa LSP
- Usa mais memória
- Consegue balancear tráfego
  - Hora enviar por um enlace, e outra por outro
- Mais fácil de gerenciar
- Permite estruturar a rede em área de roteamento

## Videoaula 11: Roteamento Hierárquico e Protocolo BGP

- Sistemas Autônomos (AS): Agregação de roteadores em regiões
  - Roteadores de mesma AS rodam mesmo protocolo de roteamento

- Roteadores de borda rodam **inter** e **intra AS**
  - Intra-AS: decisões mais simples; se preocupa com desempenho
  - Inter-AS: decisões políticas; regras do mercado podem ser mais importantes que desempenho

- IGP: Interior Gateway Protocols
  - RIP, OSPF...

- EGP: External Gateway Protocols
  - BGP-4

- Roteamento hierárquico poupa espaço da tabela de rotas e reduz tráfego de atualização

## Videoaula 12 do Capítulo 6: IPv6 Razões para a migração

- Espaço de endereçamento do IPv4 é insuficiente
- Crescimento exponencial
- Endereços desperdiçados
  - Medidas paliativas: CIDR
- Outras medidas paliativas
  - RFC 1918
  - NAT
    - Dificulta implementações de aplicações
    - Acaba com modelo de funcionamento fim a fim
    - Outros problemas técnicos
  - DHCP

- IPv6 é formado por 128 bits

- Simplifica cabeçalho protocolo IP
- IPSec: Segurança
- ICMP melhor
- Protocolo de descoberta de vizinhanças
- Melhor suporte a conexões móveis
- Fragmentação apenas na origem, agilizando o roteamento dos pacotes

## Videoaula 13 do Capítulo 6: IPv6 – Protocolo

- Cabeçalho IPv6
  - Tamanho fixo
  - Mais simples
  - Mais flexível
  - Não tem checksum
  - TTL agora é hop limit

- Cabeçalhos de extensão

- Compreensão de zeros: zeros podem ser substituídos por "::"
  - Só pode ser realizada uma vez

- Agregatable Global Unicast Address (endereços unicast globais agregáveis)

- Endereço de link local
  - FE80::/10

- Multicast
  - FF

## Videoaula 14 do Capítulo 6 ICMPv6, Mobilidade, Segurança e Transição

### ICMPv6

- Mesmas funções básicas da v4

- Cabeçalho: Tipo, código, checksum, dados

- Além das funções básicas
  - Descoberta de vizinhança
  - Gerenciamento de grupo
  - Mobilidade ipv6
  - Descoberta do path MTU
- Fragmentação é feita na fonte

### Mobilidade IPv6

- Nó muda de sub-rede para outra de forma transparente

### Segurança IPv6

- Implantaçãop do IPSec é mais simples e tem mesmas funções do IPv4
  - Pois no ipv4 o usuário geralmente usa o NAT

### Transição de IPv4 para IPv6

- Migração suave
- Pilha dupla, tunelamento, tradução
