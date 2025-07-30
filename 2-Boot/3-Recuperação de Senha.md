# Recuperando acesso ao CentOS via modo de recuperação (reset de senha)

Este documento descreve dois métodos para redefinir a senha de um usuário no CentOS quando o acesso ao sistema foi perdido, e explica por que apenas o segundo método funciona corretamente.

---

## Método 1 — Não funcional (init=/bin/bash)

Durante a inicialização do sistema:

1. No menu do GRUB, pressione a tecla `e` para editar a entrada de boot.
2. Localize a linha que começa com `linux16`.
3. Substitua `ro` por `rw` e adicione `init=/bin/bash` ao final da linha.
4. Pressione `Ctrl + X` para iniciar.

O sistema vai abrir um shell de linha de comando.

5. Execute o comando `passwd nome do usuário` para alterar a senha do usuário.
6. Execute `sync` para garantir que as alterações sejam gravadas no disco.
7. Reinicie com `reboot -f`.

Este método **não funciona corretamente** porque o ambiente carregado não é o sistema real (é o initrd). Portanto:

- O comando `passwd` pode alterar arquivos fora do local correto.
- O SELinux não reconhece as alterações e pode bloquear o login.
- O login falha mesmo após a senha aparentemente ter sido alterada com sucesso.

---

## Método 2 — Funcional (init=/sysroot/bin/sh + chroot)

Durante a inicialização do sistema:

1. No menu do GRUB, pressione a tecla `e` para editar a entrada de boot.
2. Localize a linha que começa com `linux16`.
3. Substitua `ro` por `rw` e adicione `init=/sysroot/bin/sh` ao final da linha.
4. Pressione `Ctrl + X` para iniciar.

O sistema vai carregar o shell dentro do diretório `/sysroot`.

5. Execute o comando `chroot /sysroot` para trocar o diretório raiz para o sistema real.
6. Execute `passwd nome do usuário` para alterar a senha do usuário.
7. Execute `touch /.autorelabel` para forçar o SELinux a reaplicar os rótulos de segurança.
8. Execute `exit` para sair do chroot.
9. Execute `reboot` para reiniciar o sistema.

Após o reboot, o sistema relabela os arquivos automaticamente e o login com a nova senha funciona corretamente.

---

## Diferença técnica entre os métodos

No método 1 (`init=/bin/bash`), o sistema não monta o ambiente real, e você está dentro de um shell do initrd, que é um sistema de arquivos temporário.

No método 2 (`init=/sysroot/bin/sh`), o sistema monta o sistema de arquivos real do CentOS em `/sysroot`. O comando `chroot /sysroot` muda o diretório raiz, fazendo com que comandos como `passwd` afetem corretamente os arquivos do sistema real.

Além disso, o uso de `touch /.autorelabel` garante que o SELinux não bloqueie o acesso após a alteração de senha, corrigindo rótulos de segurança.

---

## Estado do sistema após o procedimento

- A senha do usuário foi redefinida corretamente.
- Os rótulos de segurança do SELinux foram atualizados.
- Nenhum arquivo do sistema foi corrompido.
- O CentOS continua íntegro e funcional.