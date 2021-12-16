# Capítulo 4. Camada de Aplicação

## Videoaula 14: NFS (Network File System)

* NFS é um sistema que permite a montagem de sistemas de arquivos remotos através de uma rede TCP-IP

* Permite que um sistema tenha acesso a arquivos localizados remotamente de um modo integrado e transparente (como se fosse local)

* Fornece a ilusão que os discos, impressoras ou outros dispositivos, fisicamente localizados em um sistema remoto, estão diretamente conectados ao sistema de arquivos local

* Servidor NFS
  * Determina quais recursos serão compartilhados

* Cliente NFS
  * Quando solicitado, faz um requisição ao servidor que, dependendo das permissões do cliente, responde confirmando a requisição
  * Com NFS a hierarquia de diretórios e arquivos remotos passam a fazer parte do sistema de arquivos local da máquina

* Transparência de localização
  * Arquivos remotos podem estar associados a diferentes caminhos

* Arquivos centralizados

* Softwares comuns
  * Diminui espaço gasto em disco e facilita a gerência

* Dividido em três componentes
  * Protocolo NFS
    * Montagem
    * Proteção dos arquivos
    * Criptografia
  * RPC (Remote Procedure Call)
    * Controla execução de chamadas a procedimentos remotos
    * Permite que seja usado em diferentes máquinas, sistemas e arquiteturas de rede
  * XDR (External Data Representation)
    * Compatibiliza formatos com representação diferentes de dados
    * Permite programadores escreverem aplicações distribuídas

* Três versões em uso
  * NFSv2
    * UDP
    * Stateless
  * NFSv3
    * Stateless
    * TCP
    * Escrita assíncrona
    * Tamanhos de arquivo 64-bits (grandes arquivos)
  * NFSv4
    * Melhoras em desempenho e segurança
    * Mantém o estado

* SMB (Server Message block)
  * Microsoft
  * Sem transparência de localização
  * Oferece mecanismo de Chamade de procedimento remota autenticada
  * Roda sobre TCP (porta 445) ou sobre NetBIOS API
    * Serviços equivales a DNS, TCP e UDP
  * Sistemas unix aparecem como outro sistema MS Windows

* CIFS (Common Internet File System)
  * SMB rebatizado com o nome CIFS, também Microsoft.
  * Tem transparência de localização
  * Ainda usa o SMB como coração do CIFS

* Samba
  * Pra Linux
  * Composto por dois processos
    * smdb é o servidor propriamente dito
    * nmdb é reponsável pela resolução de nomes (via broadcast ou protocolo WINS)
  * Prefenciamente iniciado durante o boot
    * Dois processos rodam como daemons

## Videoaula 15: Email – Protocolo SMTP

* Correio eletrônico tem três grandes componentes:
  * Agentes de usuário (UA)
    * "Leitor de correio"
    * Criar, ler e enviar mensagens de correio (ex: thunderbird, opera, outlook)
  * Servidores de email/correio
    * Mailbox (caixa de correio)
    * Fila de mensagens de saída (a ser enviada)
    * Protocolo smtp: Permite enviar mensagens de um servidor origem para destino. Entre servidores.

  * SMTP (Simple Mail Transfer Protocol)

### SMTP

* TCP (porta 25)
* Transferência direta (Não passa por servidores intermediários)
* É a parte do **envio** do email:
  * UA -> Servidor email remetente -> Servidor email destinatário
* SMTP é o protocolo para envio de emails, do agente usuário para o servidor de email do remetente, e deste último ao servidor de email do destinatário
* Três fases de transferências
  1. Handshaking
  2. Transferência de mensagens
  3. Fechamento
* Interação comando/resposta
  * Comandos: texto ASCII
  * Resposta: código e frase de estado
  * Mensagem em ASCII
* Se a mensagem não for enviada por um período (quando já está no servidor), serviço devolve a mensagem ao remetente
* Originalmente podíamos usar a porta 25, e os dados iam sem criptografia e sem autenticação
* Hoje usamos porta 587 (STARTTLS) ou 465 (SSL/TLS)
  * SSL/TLS
    * Criptografia entre dois hospedeiros
    * TLS é o sucessor do SSL
    * Conexão inicia com a negociação da criptografia antes de qualquer transferência
  * STARTTLS
    * Utiliza conexão criptografada (TLS ou SSL)
    * Conexão inicial não criptografada e negocia STARTTLS
* SMTP AUTH: extensão do SMTP que o cliente deve se logar (uma vez que antes não tinha autenticação).
* Mensagem dividida em cabeçalho e corpo
* Conexão persistente
* Usa enter . enter pra determinar o fim da mensagem
* Comparação com HTTP
  * HTTP
    * Puxa. É o cliente que acessa o site e solicita a informação.
    * Cada objeto encapsulado em sua própria mensagem de resposta. Só um corpo pra toda a resposta do servidor.
  * SMTP
    * Empurra. Manda email pra um destinatário sem o destinatário solicitar.
    * Múltiplos objetos enviados na mensagem multiparte.
  * Ambos em ASCII, códigos de estado (até a versão 1.1. codificação da v2 pode ser binária).

## Videoaula 16: SMTP - POP3, IMAP e WebMail

Para a **leitura e gerenciamento** do mailbox é necessário outros protocolos. POP3, IMAP ou HTTP.

* POP (Post Office Protocol): autorização (agente <-> servidor) e download.
* IMAP (Internet Mail Access Protocol): Mais características (mais complexo). Manipulação de mensagens armazenadas no servidor.
* HTTP: gmail, hotmail, etc.

### Protocolo POP3

* Começa quando o agente usuário abre uma conexão TCP com o servidor de email na porta 110.
* Quando a conexão é estabelecida, POP3 prossegue em 3 fases
  1. Autenticação: Cliente manda user e pass. Ocorre em texto plano
  2. Transação:  Cliente pode listar o número de mensagens (list), obter a mensagem pelo número (retr) e apagar (dele)
  3. Atualização

#### Dois modos de operação

* download-e-apaga: Mensagens transferidas para cliente e apagadas do servidor
  * Ruim para usuários nômades (que acessam de vários computadores)
  * Após comando quit o servidor entra na fase de atualização e remove mensagens apagadas do mailbox
  * Praticamente sem estado, mantém apenas quais mensagens foram marcadas como apagadas.
* download-e-manter: Agente usuário deixa as mensagens no servidor. Usuário pode reler seus emails de outros documentos

#### Características

* Mensagens são transferidas do servidor para o computador local quando o usuário se conecta ao servidor.
* Após buscar mensagens a conexão pode ser desfeita e o usuário pode ler mesmo sem a conexão.
* Indicado em conexões de casso discado (via linha telefônica convencional onde se paga impulsos em função do tempo de conexão).
* Servidor POP3 não transfere estados para outras seções POP3: Simplifica implementação.
* POP na porta 110: Tráfego não criptografado. Não garante confidenciabilidade.
  * Para aumentar a segurança, usamos o STARTTLS (pode usar porta 110) ou SSL/TLS (porta 995, POP3S).
* Não permite gerenciar em pastas.

### Protocolo IMAP

* Útil para usuários nômades, que utilizam diferentes computadores.
* Permite manipular mailbox remoto como se fosse local
* Permite criar e manter pastas no servidor de email
* Fornece funcionalidade de busca de mensagens
* Mantém estado.
* Mais complexo que POP3.
* Necessita conexão sempre ativa, diferente do POP3.

#### Sessão IMAP

* Estabelescimento da conexão TCP entre cliente e servidor IMAP
* Um contato inicial entre servidor e cliente
* Interações entre o cliente e servidor: Similar ao POP3 (mas bem mais rica).

#### Estados do servidor IMAP

* nonauthenticated state: estado inicial. Usuário deve fornecer usuário e senha.
* authenticated state. Usuário deve selecionar uma pasta
* selected state. Usuário pode emitir comandos que afetam mensagens.
* logout state. Término da sessão.

#### Segurança do IMAP

* Originalmente sem segurança (sem criptografia)
* Podemos aumentar a segurança usando STARTTLS (porta 143) ou SSL/TLS (porta 993).

### Webmail

* Usuário agente é um browser
* Usuário se comunica com o mailbox no seu servidor de email via HTTP (não com SMTP, POP ou IMAP).
* Como no IMAP pode organizar mensagens e etc.
* HTTP substitui POP e IMAP do ponto de vista do agente de usuário. Usa direto HTTP.
  * POP/IMAP são úteis para importação de emails em outros agentes usuários e servidores.
  * Mas ainda podemos acessar os emails através do POP/IMAP. Por exemplo, se quiser fazer um backup dos emails do gmail.
* **SMTP ainda é usado para enviar o email** a menos que seja entre mesmo servidor como por exemplo gmail->gmail. Se são servidores diferentes o uso é obrigatório).

## Videoaula 17: P2P - Definição e Redes Sobrepostas

A rede Peer-to-Peer surgiu para mudar o modelo cliente/servidor e aumentar a escalabilidade.

> Pela definição da IETF de P2P: "o compartilhamento de recursos e serviços computacionais diretamente entre sistemas

* Rede P2P pura: Sistemas totalmente distribuídos, ou seja, não depende de nenhum centro ou organização, e todos os nós têm as mesmas funcionalidades e tarefas.

* São sistemas distribuídos, formados por nodos interconectados capazes de se auto-organizar em redes sobrepostas

* Rede precisa funcionar com conectividade e desempenho aceitável

* Tem como objetivo compartilhar recursos como conteúdo, ciclos de CPU, armazenamento e largura de banda

---

**A rede P2P é uma rede que chamamos de sobreposta**

É uma rede sobre outra rede. Os nós da rede sobreposta podem ser vistos como sendo conectados através de conexões virtuais (cada conexão corresponde a um caminho através de um número de conexões físicas na rede subjacente)

Uma rede sobreposta oferece funcionalidades aos seus componentes:

* Arquitetura de roteamento WAN robusta
* Busca eficentes de dados
* Seleção de pares próximos
* Armazenamento redundante
* Durabilidade dos dados
* Autenticação
* Anonimato
* Escalabilidade e tolerância a falhas

---

Não há um consenso pra definir o que é P2P. Existem muitas arquiteturas diferentes, entre elas algumas impuras que utilizam super-nós (nós que ficam indexando nós-folha) e outros utilizam servidores centralizados.


* Muitos sistemas são chamados de P2P pela forma como são percebidos pelos usuários e não pela arquitetura. Por exemplo VOIP em sistemas convencionais (SIP)

### Requisitos

* Nós podem estar localizados na borda das redes

* Capacidade dos nós se comunicarem diretamente uns com os outros

* Nós com conectivade variável (nós que entram e sai da rede: nós transientes) ou temporária e endereços também variáveis (dinâmicos)

* Capacidade de lidar com diferentes taxas de transmissão entre nós

* Nós com autonomia parcial ou total em relação a um servidor centralizado

* Em sistemas P2P puros, pela capacidade dos pares localizar dados sem necessitar de um servidor de indexação centralizado, evita existênmcia de pontos de falha únicos no sistema

* Assegurr que os nós possuem capacidades iguais de fornecer e consumir recursos de seus peers (seria o ideal)

* Deve ser escalável

---

**A Natureza distribuída da rede P2P garante robustez** no caso de falhas devido a replicação dos dados através de múltiplos pares

---

---

**Uma rede P2P suporta grande números de clientes** diferentemente de uma rede com modelo cliente/servidor. Se tivéssemos um datacenter, por exemplo, quanto mais cliente temos, mais sobrecarga no servidor.

---

### Arquitetura de redes P2P sobreposta

| Camadas de redes| 
|---|
| Camada de aplicação <- Sobreposta|
| Camada de serviços específicos <- Sobreposta|
| Camada de gerência de funções <- Sobreposta|
| Camada de gerência de nós sobrepostos <- Sobreposta|
| Camada de rede de comunicação <- Rede subjacente|
| |

* Camada de rede de comunicação: a rede que provê a comunicação para a rede sobreposta

* Camada de gerência de nós sobrepostos: Gerencia os pares, incluindo detecção de pares e algoritmos de roteamento para otimização do sistema, além da localização de recursos

* Camada de gerência de funções: Lida com segurança, confiabilidade e resiliência a faltas e aspectos de disponibilidade de recursos para a manutenção da robustez do sistema P2P

* Camada de serviços específicos: dá suporte à infraestrutura P2P subjacente e aos componentes específicos das aplicações através de agendamento de serviços, gerência de conteúdo e arquivos

* Camada de aplicação: composta pelas ferramentas, aplicações e serviços que são implementados com funcionalidades específicas sobre a infraestrutura P2P sobreposta

### Aplicações

* Compartilhamento de recursos
* Mensagens instantâneas
* Computação distribuída
* CDNs (Content Delivery Networks)
* Armazenamento distribuído
* Anonimato, resistência a censura
* Aplicação de trabalho colaborativo
* Criptomoedas

## Videoaula 18: P2P - Classificações

### 1. Classicação quanto a distinção das funções realizadas pelos nós

#### Descentralizado (p2p pura)

* Rede onde não há um nó central: todo nó na rede p2p tem a mesma função

#### Semicentralizado

* Existem nós com funções especifíficas
* Nós centrais para controle
* Conjunto de supernós: Utitilizados na busca de informações e mantém dados dos nós folhas para a busca, normalmente usando TabelaHash Distribuída (DHT)

### 2. Quanto ao modelo de busca

#### Busca centralizada

* Rede com um ponto central de busca e nós consultam esse ponto
* Troca de informações é diretamente entre os nós

#### Busca por inundação

* Busca limitada à vizinhança mais próxima do nó que fez a busca.
* Busca é escalável, mas não é completa

#### Busca centralizada por tabela hash distribuída (DHT)

* Serviço de lookup similar a uma table hash: pares (chave, valor)
* Dados diferentes geram chaves diferentes, permitindo identificar unicamente um dado
* Qualquer nó participante pode recuperar o vaor associado a uma chave
* Responsabilidade de manter o mapeamento de chaves para valores é distribuída entre os nós
* Para cada chave k, o nó ou é dono da chave k ou tem um link para um nó perto de k em termos de distância do espaço de chaves
  * Em cada passo, encaminhe a mensagem para o vizinho cujo ID seja o mais próximo de k
  * Quando não houver tal vizinho, chegou-se ao dono da chave k

### **3. Baseada tantos nos modos de busca quanto na diferenciação dos papéis (utilizada no teste)**

#### P2P Centralizada

* Busca centralizada onde um nó central mantém um índice central com informações atualizadas. 

* Exemplos: 
  * sistemas de compartilhamento de arquivos Napster (mais informações sobre Napster nos slides)
  * Tor

Problemas:

* Ponto único de falha
* Gargalo de desempenho
* Violação de direitos autorais
* Transferência de arquivos é descentralizada, mas a localização do conteúdo é altamente centralizada

#### P2P Descentralizada e Estruturada

* Não possui servidor centralizado de diretório de informações, mas tem uma estruturação entre os nós
* Topologia é controlado e arquivos são posicionados em locais que posteriormente tornam fácil a sua localização
* Em geral usa busca baseada em DHTs
  * Protocolos e implementações: Chord, Kademlia, Pastry, Tapestry e CAN, Viceroy
  * Aplicações: BitTorrent, eMule

* Cada nó é um líder de grupo ou está associado a um líder de grupo (nó filho)
* Nó consulta líder do grupo, e o líder por sua vez pode consultar outros líderes de grupo

* Gnutella2: Divide nós em dois grupos: folhas (leaves) e concentradores (hubs)
  * Quando uma pesquisa é iniciada, o nó obtém uma lista de concentradores se necessário, e contacta todos os concentradores da lista até o fim da lista ou se atingir um limite definido na consulta
  * Exemplo: Limeware

* KaZaA (descontinuado): Mais informações nos slides

* Rede sobreposta
* Nó de inicialização: o parceiro que está se conectando ou é alocado a um líder de grupo ou é designado como líder
* Vantagens:
  * Ausência de servidor de diretórios centralizado
* Desvantagens:
  * É necessário um nó de inicialização
  * Líderes de grupos podem ficar sobrecarregados


#### P2P Descentralizada e não Estruturada

* Não possui servidor centralizado, nem controle preciso sobre a topologia e localização/busca de documentos

* Exemplo: Gnutella
  * Sem hierarquias
  * Envia consulta aos vizinhos (busca por inundação)

* Vantagens:
  * Nós tem mesmas responsabilidades
  * Altamente descentralizado
  * Nenhum parceiro mantém informação de diretório

* Desvantagens:
  * Tráfego excessivo de consultas
  * Necessário definir raio da consulta
  * Nó de inicialização
  * Manutenção da rede sobreposta: difícil manter

---

Normalmente o tráfego da internet é assimétrico (tráfego núcleo->borda), e essa assimetria está aumentando cada vez mais. No entanto, a rede P2P é uma aplicação simétrica (tráfego borda<->borda), o que pode afetar a qualidade de P2P.

---

## Videoaula 19: Programação com a API Socket (**não cobrado em prova**)

Socket: Uma interface controlada pelo S.O., local ao host, que é criada e controlada pela aplicação. Uma porta na qual o processo de aplicação pode enviar e receber dados de outros processo de aplicação; Uma porta entre o processo de aplicação e o protocolo de transporte fim-a-fim (TCP ou UDP).

Dois tipos de serviço de transporte via socket API:

* Datagrama não confiável (UDP)
* Confiável e orientado a fluxo de bytes (TCP, bytes em ordem)

### Programação de Socket usando TCP

* Cliente deve contactar servidor
  * Processo servidor deve estar antes em execução
  * Servidor deve antes ter criado socket (porta) que aguarda contato do cliente
* Cliente cria socket TCP
  * Socket TCP local ao cliente
  * Especificar endereço IP número de porta do processo servidor 
  * TCP do cliente estabelece conexão com TCP do servidor
  * Quando contatado pelo cliente, o TCP do servidor cria socket novo

#### Interação socket cliente/servidor TCP

1. Servidor (rodando em hostid): Cria socket porta=x ServerSocket
2. Servidor: Espera por requisição welcomeSocket.accept()
3. Cliente: Criar socket conectado a hostid, port=x, Socket()
4. Cliente: Envia requisão por clientSocket
5. Servidor: Ler requisião connectSocket
6. Servidor: Escrever resposta para connectionSocket
7. Cliente: Le resposta de clientSocket
8. Servidor: Fecha connectionSocket
9. Cliente: Fecha clientSocket

### Programação via socket com UDP

* Transferência não confiável
* Emissor associa explicitamente endereço IP e porta do destino
* Servidor deve extrair endereço IP e porta do emissor do datagrama recebido
* Dado pode ser recebido fora de ordem, alguns podem acabar sendo perdidos

#### Interação socket cliente/servidor UDP

1. Servidor: Cria socket, porta x, DatagramSocket()
2. Cliente: cria socket, DatagramSocket()
3. Cliente: Cria datagram, endereço (hostid, porta=x), envia datagram de pedido usando clientSocket
4. Servidor: Lê pedido de serverSocket
5. Servidor: Escreve resposta para serverSocket, especificando endereço e número de porta do cliente. Retorna para 4.
6. Cliente: Lê reposta de clientSocket
7. Cliente: Fecha clientSocket
