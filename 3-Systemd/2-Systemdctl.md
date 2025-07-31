# Gerenciamento de Serviços com systemctl

No CentOS (e em várias outras distribuições Linux que utilizam o `systemd` como sistema de inicialização), o comando `systemctl` é utilizado para controlar os serviços do sistema — também chamados de "units". 

Com ele, é possível iniciar, parar, reiniciar, verificar o status de serviços e configurá-los para iniciar automaticamente durante o boot do sistema.

## Instalação de um Serviço

Para utilizar o `systemctl`, o serviço precisa estar instalado. Diferentemente do Ubuntu, por exemplo, onde muitos serviços já são configurados para iniciar automaticamente após a instalação, o CentOS adota uma abordagem mais minimalista: ele apenas instala o serviço, cabendo ao administrador configurá-lo manualmente depois, se necessário.

Então, ao instalar o servidor web Apache (httpd) com:

```bash
yum install httpd
```

O serviço `httpd` será registrado no `systemd`, e um arquivo de unidade (`httpd.service`) será criado, geralmente em `/usr/lib/systemd/system/`.

## Verificando o Status de um Serviço

Após a instalação, o serviço ainda não está em execução. Para verificar o estado atual dele:

```bash
systemctl status httpd
```

Esse comando exibe informações como se o serviço está ativo ou inativo, o PID do processo (se estiver rodando), quando foi iniciado, e os últimos logs relacionados a ele.

Aqui, ele mostra que o serviço não está habilitado. Quando você digita o comando:

```bash
systemctl enable httpd
```
Você está dizendo ao sistema que quer que o serviço httpd (que é o servidor Apache) seja iniciado automaticamente sempre que o sistema for ligado.

A saída:
```bash
Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.
```
Significa que o systemctl criou um link simbólico (ou atalho) entre dois arquivos:

O arquivo original do serviço httpd, que fica em /usr/lib/systemd/system/httpd.service. Esse arquivo tem as instruções de como iniciar e parar o Apache.

E o diretório de serviços ativos no boot, que nesse caso é /etc/systemd/system/multi-user.target.wants/.

Esse diretório representa o que o sistema deve iniciar quando atinge o nível de execução chamado multi-user.target, que é o modo texto completo com rede — típico de servidores.

Criar esse link é a forma que o *systemctl enable* usa para registrar que um serviço deve iniciar automaticamente no boot.

**Importante:** esse comando não inicia o serviço imediatamente, só configura para que ele inicie nos próximos boots.

## Iniciando e Parando Serviços Manualmente

Para iniciar o serviço manualmente:

```bash
systemctl start httpd
```

Para parar o serviço:

```bash
systemctl stop httpd
```

Também é possível reiniciá-lo:

```bash
systemctl restart httpd
```

Ou apenas recarregar a configuração do serviço (sem derrubar o processo):

```bash
systemctl reload httpd
```

## Verificando Portas em Escuta

Após iniciar um serviço como o Apache, que escuta conexões de rede, é possível confirmar que ele está escutando na porta 80 com:

```bash
ss -tuln | grep :80
```

## Ativando um Serviço para Iniciar com o Sistema

Um serviço iniciado manualmente com `systemctl start` não será iniciado automaticamente na próxima vez que o sistema for reiniciado. Para garantir que ele seja executado automaticamente no boot, é necessário ativá-lo com:

```bash
systemctl enable httpd
```

Esse comando cria um link simbólico (symlink) do arquivo `httpd.service` dentro do diretório `/etc/systemd/system/multi-user.target.wants/`, que está associado ao target padrão do sistema (equivalente ao antigo runlevel 3).

Para verificar esse link simbólico:

```bash
ls -l /etc/systemd/system/multi-user.target.wants/
```

Esse link é o que garante que o serviço seja carregado automaticamente sempre que o sistema atingir o estado multiusuário (multi-user target).

## Desativando um Serviço no Boot

Se não quiser mais que o serviço seja iniciado automaticamente, execute:

```bash
systemctl disable httpd
```

Isso remove o link simbólico anteriormente criado, mas não interfere no estado atual do serviço (ele continuará rodando até ser parado manualmente ou até o sistema ser reiniciado).

## Arquivo de Unidade (Unit File)

Os arquivos de unidade dos serviços (`*.service`) contêm instruções para o `systemd` saber como iniciar, parar e gerenciar o serviço. Para inspecionar o conteúdo do arquivo do Apache:

```bash
cat /usr/lib/systemd/system/httpd.service
```

Esse arquivo define se o serviço depende de outros, quais comandos usar para iniciar/parar, e em qual target o serviço deve rodar, entre outras informações.

## Alvo Padrão do Sistema (Default Target)

O `systemd` utiliza "targets" para definir estados do sistema (como os antigos runlevels). O target padrão geralmente é:

```bash
/etc/systemd/system/default.target -> /usr/lib/systemd/system/multi-user.target
```

É esse target que é atingido após o boot do sistema. Todos os serviços habilitados para esse target serão iniciados automaticamente.

## Verificando o Status Após Reboot

Depois de configurar um serviço para iniciar com o sistema e reiniciar o sistema com:

```bash
reboot
```

É possível confirmar se o serviço foi iniciado corretamente com:

```bash
systemctl status httpd
```

E também confirmar que a porta 80 ainda está sendo escutada:

```bash
ss -tuln | grep :80
```

## Conclusão

O `systemctl` é uma ferramenta poderosa e essencial para administração de sistemas Linux que utilizam o `systemd`. Ele permite controle completo sobre os serviços, desde ações manuais (start, stop, restart) até a configuração de quais serviços devem ou não iniciar automaticamente. Com ele, é possível administrar de forma clara e segura o comportamento dos serviços no ciclo de vida do sistema.
