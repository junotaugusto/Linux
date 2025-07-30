# Systemd vs SysV — Runlevel vs Target

Durante muito tempo, o gerenciamento do processo de inicialização (boot) do sistema Linux era feito através do SysVinit (System V Init), um sistema tradicional herdado do UNIX. 

Com o passar do tempo, esse modelo foi sendo substituído por soluções mais modernas, sendo o Systemd a principal delas atualmente. Nesta explicação, veremos o que é o SysV, o que é o Systemd, o conceito de Runlevel e Target, e como tudo isso se encaixa no contexto da administração de sistemas Linux.

---

## O que é o SysV

SysV (System V Init) foi o primeiro sistema de inicialização amplamente utilizado no Linux. 

Ele era responsável por gerenciar a sequência de inicialização dos serviços e processos essenciais do sistema. Após o carregamento do kernel, o sistema passava o controle para o processo `init`, que então lia arquivos como `/etc/inittab` para determinar qual runlevel iniciar e, com isso, quais serviços carregar.

Cada runlevel representava um estado operacional do sistema, como modo de usuário único, modo multiusuário, modo gráfico etc. 

O SysV utilizava diretórios como `/etc/rc.d/rcN.d` (onde N é o número do runlevel) contendo links simbólicos para scripts de inicialização de serviços. 

Esses scripts eram executados em ordem alfabética, usando uma convenção de nomes com `S` (start) e `K` (kill), o que determinava a ordem de inicialização e encerramento.

Apesar de funcional, o SysV tinha várias limitações. Não era capaz de tratar dependências entre serviços, não suportava paralelismo (os serviços eram iniciados sequencialmente), e tinha gerenciamento limitado de estado dos serviços. Isso tornava o boot mais lento e menos confiável em sistemas modernos e complexos.

---

## O que é o Systemd

Systemd é o substituto moderno do SysVinit. Ele surgiu para resolver justamente os problemas estruturais do modelo antigo, oferecendo uma arquitetura mais eficiente e robusta. 

O Systemd não é apenas um gerenciador de inicialização, mas um sistema completo de gerenciamento de serviços, processos, estados e recursos do sistema.

Ao contrário do SysV, o Systemd é baseado no conceito de unidades (units), que podem representar serviços, dispositivos, montagens, sockets, entre outros. 

Essas unidades são configuradas por arquivos do tipo `.service`, `.target`, `.mount`, etc., localizados geralmente em `/etc/systemd/system/` e `/lib/systemd/system/`.

O Systemd tem suporte nativo a dependências entre serviços, garantindo que os processos sejam iniciados na ordem correta. Também suporta paralelismo, iniciando múltiplos serviços simultaneamente sempre que possível, acelerando o processo de boot.

Além disso, o Systemd oferece uma série de funcionalidades extras:

- Gerenciamento de logs centralizado com o `journald`, acessível pelo comando `journalctl`.
- Controle de rede com integração ao `networkd`.
- Controle de fuso horário (`timedatectl`).
- Monitoramento do status de serviços (`systemctl status`), permitindo saber se um serviço está ativo, falhou, ou foi encerrado.

O Systemd passou a ser adotado pela maioria das distribuições Linux modernas, incluindo CentOS, Fedora, Ubuntu, Debian, entre outras.

---

## O que é Runlevel

Runlevel é um conceito associado ao SysVinit que define o estado operacional do sistema. Cada runlevel corresponde a um conjunto específico de serviços que devem estar ativos. Os principais runlevels são:

- **0**: Halt (desligar o sistema)
- **1**: Single-user mode (modo de manutenção, sem rede)
- **2**: Multi-user mode (sem rede, usado por algumas distros)
- **3**: Multi-user mode com rede (modo padrão em servidores)
- **4**: Não utilizado (reservado para uso do usuário)
- **5**: Multi-user mode com rede e interface gráfica (modo padrão em desktops)
- **6**: Reboot (reinicializar o sistema)

---

## Systemd Targets

No Systemd, o conceito de runlevel é substituído pelos chamados *targets*. Cada target representa um estado do sistema, de forma semelhante aos runlevels, mas com muito mais flexibilidade. Targets podem depender uns dos outros e podem ser personalizados.

A equivalência entre runlevels e targets no Systemd é a seguinte:

| Runlevel | Target               | Descrição                                           |
|----------|----------------------|-----------------------------------------------------|
| 0        | `poweroff.target`    | Desliga o sistema                                   |
| 1        | `rescue.target`      | Modo de recuperação (modo único, root apenas)       |
| 2        | `multi-user.target`  | Modo multiusuário (sem interface gráfica)           |
| 3        | `multi-user.target`  | Idêntico ao runlevel 2                              |
| 4        | `multi-user.target`  | Pode ser usado como customização                    |
| 5        | `graphical.target`   | Modo gráfico (multiusuário com interface gráfica)   |
| 6        | `reboot.target`      | Reinicia o sistema                                  |

O Systemd permite mudar o target com o comando `systemctl isolate nome.target`, e definir o target padrão com `systemctl set-default nome.target`.
