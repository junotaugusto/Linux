# GRUB2 – Configuração e Funcionamento

## O que é o GRUB2

GRUB2 (GRand Unified Bootloader v2) é o carregador de boot padrão na maioria das distribuições Linux modernas. Ele é responsável por carregar o kernel do sistema operacional e iniciar o processo de boot. 

O GRUB2 é altamente configurável, suporta múltiplos sistemas operacionais e pode ser personalizado para executar comandos, scripts, alterar parâmetros de boot e muito mais.

## Resumo da Aula: Configuração do GRUB2

- A configuração do GRUB2 é importante porque ele controla o boot do sistema. Manutenções como recuperação e customizações passam por ele.

- O arquivo de configuração principal do GRUB2 é:
  `/boot/grub2/grub.cfg`
  Nunca se deve editar este arquivo diretamente. Ele é gerado automaticamente.

- A edição correta deve ser feita no arquivo:
  `/etc/default/grub`
  Esse arquivo possui variáveis como:
  - GRUB_TIMEOUT: tempo de espera no menu (ex: 5 segundos).
  - GRUB_DEFAULT: qual entrada será selecionada por padrão (ex: saved).
  - GRUB_DISABLE_RECOVERY: controla se o modo recovery aparecerá ou não.
  - GRUB_CMDLINE_LINUX: parâmetros passados ao kernel na inicialização.

- Outros arquivos auxiliares estão em:
  `/etc/grub.d/`
  Scripts nesse diretório são concatenados ao gerar o `grub.cfg`.

- Após alterar arquivos de configuração, é necessário rodar:
  `grub2-mkconfig -o /boot/grub2/grub.cfg`
  Esse comando recompila o arquivo principal com base nos arquivos de `/etc/default/grub` e `/etc/grub.d/`.

- É possível customizar o GRUB para, por exemplo, abrir um terminal como root diretamente no boot (para recuperação do sistema). Isso é feito adicionando parâmetros como:
  - `rw` (read-write no sistema de arquivos)
  - `init=/bin/bash` (define o bash como primeiro processo ao invés do init normal)

- Exemplo de uso avançado:
  - No menu do GRUB, pressionar `e` para editar a entrada.
  - Modificar a linha do kernel (`linux ...`) adicionando `rw init=/bin/bash`.
  - Pressionar `Ctrl + X` ou `F10` para iniciar com essas opções.
  - Isso pode permitir acesso root ao sistema mesmo sem autenticação, o que pode representar uma falha de segurança.

- Por segurança, pode-se configurar senha no GRUB para impedir alterações de parâmetros de boot sem autorização.

- Dica: o professor usou o `vi` e sugeriu que quem não estiver confortável pode instalar e usar o `nano` como editor de texto.

- Após alterações, confirme a nova data de modificação do `/boot/grub2/grub.cfg` para garantir que o comando de atualização foi bem-sucedido.

- Problemas comuns (como sistema não iniciar) podem ser contornados editando o GRUB no boot, usando o terminal root e corrigindo os arquivos problemáticos.

---

Esse é o comportamento padrão e recomendado para lidar com o GRUB2 nas distribuições Linux modernas como o CentOS.

