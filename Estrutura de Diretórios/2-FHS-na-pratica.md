# Kernel vmlinuz e initramfs no Linux

## vmlinuz – O Kernel do Linux

No Linux, o arquivo chamado `vmlinuz` representa o **núcleo (kernel)** do sistema operacional. Ele é um **arquivo compactado** que contém o código do kernel que será carregado na memória quando o sistema inicia.

- **vmlinuz** significa "Virtual Memory LINUx gZip".
- Ele é responsável por inicializar o sistema, reconhecendo o hardware, montando o sistema de arquivos raiz e iniciando o processo `init` ou `systemd`, que dá sequência ao boot.

**Local comum:** `/boot/vmlinuz-<versão>`

O kernel é o "coração" do Linux — ele gerencia os processos, a memória, os dispositivos e todo o sistema de arquivos.

---

## initramfs (ou initrd) – O Sistema de Arquivos Temporário

Junto com o `vmlinuz`, também é carregado um outro arquivo chamado `initramfs` (ou antigamente `initrd`). Esse arquivo representa um **sistema de arquivos temporário em memória** que é usado **durante o boot**.

- Ele contém drivers e utilitários necessários para **preparar o sistema para montar o sistema de arquivos real**.
- É muito útil para **montar sistemas de arquivos complexos**, como LVM, RAID, ou discos criptografados, antes que o Linux consiga montar a partição raiz real.

**Local comum:** `/boot/initramfs-<versão>.img`

---

## Como eles funcionam juntos

1. O bootloader (como o GRUB) carrega o `vmlinuz` e o `initramfs` na memória.
2. O kernel (`vmlinuz`) começa a rodar.
3. O kernel monta o `initramfs` como seu sistema de arquivos temporário.
4. A partir do `initramfs`, ele localiza e monta o verdadeiro sistema de arquivos raiz.
5. Após isso, o controle é passado ao sistema de inicialização (como o `systemd`), que continua o processo de boot normalmente.

---

## Resumo

| Arquivo       | Função                                                  |
|---------------|----------------------------------------------------------|
| `vmlinuz`     | Kernel do Linux, gerencia todo o sistema operacional     |
| `initramfs`   | Sistema de arquivos temporário, auxilia na montagem da partição raiz durante o boot |

Esses dois arquivos são essenciais para que o sistema Linux inicie corretamente.
