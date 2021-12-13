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
