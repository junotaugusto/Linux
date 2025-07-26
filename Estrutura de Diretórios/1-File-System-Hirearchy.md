# Aula: Estrutura de Diretórios no Linux

## Introdução

Para conseguir trabalhar de maneira eficiente em sistemas baseados em Linux, é fundamental entender a **estrutura de diretórios**. Essa estrutura organiza os arquivos e diretórios de forma padronizada, e esse padrão é conhecido como **FHS** – *File System Hierarchy* (Hierarquia de Sistema de Arquivos).

Esse padrão é herdado dos sistemas Unix e define onde devem ficar arquivos do sistema, bibliotecas, arquivos temporários, arquivos de configuração, arquivos de usuário, e assim por diante.

---

## Comparação: Linux vs Windows

Muita gente que vem do Windows acha que o Linux é confuso, mas, na verdade, ambos têm uma hierarquia. A diferença está **em como essa hierarquia é organizada e usada**.

No **Windows**, tudo está dentro da unidade `C:\`, onde:

- `C:\Windows\` → arquivos do sistema operacional;
- `C:\Program Files\` → programas instalados;
- `C:\Users\` → dados de usuários;
- `C:\Temp\` → arquivos temporários;

No **Linux**, tudo está dentro do diretório `/` (barra), chamado de **root directory** (diretório raiz), e a partir dele se organiza o restante:

- `/bin` → comandos essenciais;
- `/etc` → arquivos de configuração;
- `/home` → diretórios dos usuários;
- `/tmp` → arquivos temporários;
- `/usr` → programas e bibliotecas de usuário;
- `/var` → arquivos variáveis (logs, cache etc).

Portanto, a lógica é parecida: tudo começa de um ponto central. No Windows, é `C:\`; no Linux, é `/`. A **diferença está no conteúdo e no padrão de organização**.

---

## O que é o FHS?

O **FHS** (File System Hierarchy) é um padrão que define onde cada tipo de arquivo deve ser armazenado em sistemas Linux e Unix. 

Esse padrão ajuda a manter os sistemas organizados e torna mais fácil para administradores e desenvolvedores entenderem onde procurar ou instalar arquivos.

### Exemplo de diretórios definidos pelo FHS:

| Diretório     | Função                                                |
|---------------|--------------------------------------------------------|
| `/bin`        | Comandos essenciais do sistema                         |
| `/sbin`       | Comandos administrativos (root)                        |
| `/etc`        | Arquivos de configuração                               |
| `/home`       | Diretórios dos usuários                                |
| `/var`        | Arquivos variáveis (logs, fila de impressão, cache)   |
| `/tmp`        | Arquivos temporários                                   |
| `/usr`        | Aplicações e arquivos de usuários                      |
| `/lib`        | Bibliotecas compartilhadas essenciais                  |
| `/boot`       | Arquivos de inicialização do sistema                   |
| `/root`       | Diretório pessoal do usuário root                      |
| `/dev`        | Arquivos de dispositivos                               |
| `/media`      | Pontos de montagem automática (pendrive, CD etc.)     |
| `/mnt`        | Ponto de montagem manual                               |
| `/opt`        | Softwares adicionais/fora do padrão                    |
| `/proc`       | Informações do kernel e processos (virtual)           |
| `/sys`        | Informações do sistema (virtual, como `/proc`)        |

---

## O que é uma Biblioteca?

Uma **biblioteca**, em sistemas operacionais, é um conjunto de funções ou recursos que podem ser utilizados por diferentes programas. Ela **evita que o código seja duplicado** em cada aplicação.

### Explicando de forma simples:

Imagine que vários programas precisam imprimir algo na tela. Ao invés de cada um ter seu próprio código para isso, todos eles podem usar uma **biblioteca compartilhada** com essa função.

No **Windows**, muitas vezes os programas vêm com suas próprias bibliotecas embutidas. Isso pode causar duplicação e mais consumo de espaço.

No **Linux**, as bibliotecas ficam centralizadas em diretórios como `/lib` ou `/usr/lib`, e **vários programas usam as mesmas**. Isso torna o sistema mais enxuto e eficiente.

---
## Um pouco sobre diretórios

### `/`

É o diretório raiz (root directory). Tudo no Linux começa a partir dele. Diferente do Windows, que possui múltiplas unidades (C:\, D:\ etc), o Linux tem uma única raiz e todas as demais pastas (inclusive discos e dispositivos) são montadas dentro dela.

---

### `/bin`

Significa **binários**. Contém arquivos executáveis essenciais que estão disponíveis para **todos os usuários** (inclusive em modo de recuperação). Por exemplo, comandos como `ls`, `cp`, `mv`, `rm`, `cat` estão aqui.

---

### `/boot`

Armazena os **arquivos necessários para o boot (inicialização)** do sistema, incluindo o kernel (por exemplo: `vmlinuz`), o `initrd`, o `grub` e outros arquivos de inicialização.

---

### `/dev`

Contém os arquivos de **dispositivos** (device files). No Linux, quase tudo é tratado como um arquivo, inclusive dispositivos como discos (`/dev/sda`), partições (`/dev/sda1`), terminal (`/dev/tty`), entre outros. Pen drives, teclados, mouse, todos dispositivos terão entradas no "/dev".

---

### `/etc`

Armazena arquivos de **configuração** do sistema e dos programas instalados. Por exemplo: `/etc/passwd`, `/etc/hostname`, `/etc/network/interfaces`. É uma das pastas mais importantes para administradores de sistema. 

Quando você instala uma aplicação como o Libre Office, por exemplo, os arquivos de configuração do Libre Office estarão dentro de "/etc/libreoffice" por exemplo. 

---

### `/home`

É onde ficam os diretórios dos **usuários comuns**. Cada usuário tem sua própria pasta, como `/home/junot`, com seus arquivos, configurações pessoais e documentos. Equivale à pasta "Documentos" no Windows para cada conta.

---

### `/lib` e `/lib64`

Contêm as **bibliotecas compartilhadas** (libraries) essenciais usadas pelos binários do `/bin` e `/sbin`. Bibliotecas são como "funções prontas" usadas por vários programas. No Linux, é comum várias aplicações compartilharem as mesmas bibliotecas, o que evita duplicações e economiza espaço.

- `/lib` → para sistemas 32 bits.
- `/lib64` → para bibliotecas de 64 bits.

---

### `/media`

Ponto de montagem automático para **dispositivos removíveis**, como pendrives, CDs/DVDs e HDs externos. Por exemplo: quando você conecta um pendrive, ele pode ser montado em `/media/junot/PENDRIVE`.

Quando você conecta um pendrive, lembre-se do diretório `\dev`, então ele cria uma abstração de objeto no `\dev` e ele monta no `\media`.

---

### `/mnt`

Usado tradicionalmente para **montagens temporárias** de sistemas de arquivos. Por exemplo, montar manualmente uma partição: `mount /dev/sdb1 /mnt`. Um CD por exemplo.

---

### `/opt`

Diretório usado para instalar **programas opcionais** ou de terceiros. Por exemplo, aplicativos que não fazem parte da distribuição padrão do sistema, como o Google Chrome ou softwares proprietários.

---

### `/proc`

Um sistema de arquivos virtual (não é um diretório de verdade no disco). Contém **informações do kernel e dos processos**. Exemplo: `/proc/cpuinfo`, `/proc/meminfo`, `/proc/1` (informações do processo PID 1).

---

### `/root`

É o **diretório home do usuário root**. Diferente de `/home`, que contém os usuários comuns, o superusuário tem sua pasta separada aqui: `/root`.

---

### `/run`

Diretório que armazena **informações voláteis** (temporárias) desde o boot até o momento atual. Substituiu diretórios antigos como `/var/run`.

---

### `/sbin`

Similar ao `/bin`, mas contém **binários essenciais para administração do sistema** (superusuário/root). Exemplos: `ifconfig`, `iptables`, `reboot`, `fdisk`.

---

### `/srv`

Significa "service". Contém arquivos para **serviços do sistema**, como páginas da web (`/srv/www`) ou arquivos de FTP (`/srv/ftp`).

---

### `/sys`

Outro sistema de arquivos virtual (como `/proc`). Usado para **interagir com o kernel e o hardware**. Ajuda a controlar e obter informações sobre dispositivos e drivers do sistema.

---

### `/tmp`

Armazena **arquivos temporários** criados por programas. Esses arquivos podem ser apagados automaticamente quando o sistema reinicia.

---

### `/usr`

Contém **aplicações e arquivos não essenciais** para o funcionamento do sistema. Inclui:

- `/usr/bin`: binários de usuário.
- `/usr/lib`: bibliotecas.
- `/usr/share`: arquivos compartilhados (documentação, ícones, traduções).
- `/usr/local`: instalação de programas feitos manualmente pelo administrador.

---

### `/var`

Contém **arquivos variáveis**, que mudam frequentemente. Exemplo: logs (`/var/log`), filas de impressão (`/var/spool`), arquivos de cache (`/var/cache`), e-mails, banco de dados, etc.

---

# Conclusão

Entender o FHS é essencial para navegar, configurar e administrar sistemas Linux. Cada diretório tem um propósito claro e definido, e esse padrão torna o sistema mais organizado e previsível.

---

**Dica Final**: Use o comando `ls -l /` para visualizar todos os diretórios na raiz do sistema e explorar o que há dentro de cada um.

Saber como o Linux organiza seus arquivos e entender o que é uma biblioteca são passos importantes para começar a dominar o sistema. O FHS nos ajuda a manter tudo padronizado, e o modelo de bibliotecas compartilhadas aumenta a eficiência.

Aos poucos, esses conceitos vão ficando mais claros com a prática. E conforme você for explorando os diretórios com comandos como `ls`, `cd` e `file`, vai começar a reconhecer onde cada coisa vive no sistema.
