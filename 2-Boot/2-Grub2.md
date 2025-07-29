# GRUB2 – Configuração e Funcionamento

## O que é o GRUB2

GRUB2 (GRand Unified Bootloader v2) é o carregador de boot padrão na maioria das distribuições Linux modernas. Ele é responsável por carregar o kernel do sistema operacional e iniciar o processo de boot. 

O GRUB2 é altamente configurável, suporta múltiplos sistemas operacionais e pode ser personalizado para executar comandos, scripts, alterar parâmetros de boot e muito mais.

## Configuração do GRUB2

- A configuração do GRUB2 é importante porque ele controla o boot do sistema. Manutenções como recuperação e customizações passam por ele.

- O arquivo de configuração principal do GRUB2 é:
  `/boot/grub2/grub.cfg`
  Nunca se deve editar este arquivo diretamente. Ele é gerado automaticamente. Ele é o arquivo central. Se você remover este arquivo, o sistema não "boota" mais.

  **De acordo com as boas práticas do grub o que precisamos fazer é editar outros arquivos de configuração que vão impactar neste arquivo. Nunca diretamente ele.**

  Ou seja, editamos outros arquivos e, quando rodar o update no grub, ele vai automaticamente atualizar o arquivo de configuração acima.

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

## Entendendo o Diretório `/etc/grub.d` e a Geração do Arquivo `grub.cfg`

### Visão Geral

O diretório `/etc/grub.d` contém uma série de scripts executáveis que são **concatenados** para formar o arquivo final de configuração do GRUB2:  
`/boot/grub2/grub.cfg`

Esses scripts representam **blocos de configuração** do GRUB, como o cabeçalho, entradas de menu, detecção automática de sistemas operacionais, opções de recuperação, entre outros.

---

### Como funciona a concatenação

Quando você executa o comando:

```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```

O sistema:

1. **Lê o arquivo** `/etc/default/grub`, que define variáveis globais (como GRUB_TIMEOUT, GRUB_CMDLINE_LINUX etc.).
2. **Executa os scripts** localizados em `/etc/grub.d`, **em ordem numérica** (10_linux, 20_linux_xen, 30_os-prober, etc.).
3. Cada script **gera uma parte da configuração** em texto.
4. O conteúdo de cada script executado é **concatenado** (unido, somado) em sequência.
5. O resultado final dessa concatenação é escrito no arquivo `/boot/grub2/grub.cfg`.

---

### Estrutura típica do diretório `/etc/grub.d`

| Script               | Função                                                                 |
|----------------------|------------------------------------------------------------------------|
| `00_header`          | Define configurações básicas como fundo de tela, fontes e variáveis.   |
| `10_linux`           | Gera entradas para o kernel Linux instalado no sistema.                |
| `20_linux_xen`       | Se você usa o Xen Hypervisor, gera entradas para ele.                  |
| `30_os-prober`       | Detecta outros sistemas operacionais (como Windows) e cria entradas.   |
| `40_custom`          | Script vazio para você adicionar entradas personalizadas.              |
| `41_custom`          | Igual ao `40_custom`, mas executado após o `os-prober`.                |

Esses scripts devem ser **executáveis** (`chmod +x`), caso contrário serão ignorados.

---

### Exemplo: como o sistema monta o `grub.cfg`

Suponha que você tenha os seguintes scripts em `/etc/grub.d`:

- `00_header` → imprime cabeçalho.
- `10_linux` → imprime entradas para o CentOS.
- `30_os-prober` → imprime entrada para Windows.
- `40_custom` → imprime uma entrada personalizada sua.

Quando você roda `grub2-mkconfig`, o sistema vai executar cada script e juntar suas saídas em um único arquivo:

```bash
cat /etc/grub.d/00_header > grub.cfg
cat /etc/grub.d/10_linux >> grub.cfg
cat /etc/grub.d/30_os-prober >> grub.cfg
cat /etc/grub.d/40_custom >> grub.cfg
```

(O processo real é feito via shell script de forma dinâmica, mas essa é a ideia.)

---

### Por que não editar o `/boot/grub2/grub.cfg` diretamente?

Porque ele é **gerado automaticamente**. Se você editar manualmente, suas mudanças serão **sobrescritas** da próxima vez que você executar `grub2-mkconfig`.

Se quiser personalizar o GRUB:

- Use `/etc/default/grub` para configurações gerais.
- Edite ou crie scripts em `/etc/grub.d/` para alterar o comportamento das entradas.
- Ou adicione entradas personalizadas em `/etc/grub.d/40_custom`.

________________________________________________________________________________

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

