# 101.1 Lição 1
- BIOS (Basic Input Output System)
- UEFI (Unified Extensible Firmware Interface) - Recursos melhores que o anterior.

É possível realizar ajustes mesmo sem o sistema operaciona instalado.

É comum que chamem ambas de "BIOS" pois tanto a BIOS e a UEFI cumprem a mesma função.

## Ativação do dispositivo

Podem ser ativadas ao iniciar o computador e pressionar Del ou F2 ou F12. dependendo do fabricante.

Na bios podemos alterar configurações de Hardware como por exemplo:
- IRQ (Solicitação de interrupição)
- DMA (Acesso direto a memória)

Exemplo de ajuses que podem ser feitos:
- Ajuste de frequência de memória RAM para melhorar desempenho, segundo o fabricante.
- Desabilitar recursos desnecessários da CPU para economizar energia

Mas geralmente para computadores mais antigos.

Se a máquina estiver com vários dispositivos de armazenamento é importante definir que o que tiver o carregador de inicialização correto deva ser o primeiro da ordem.

## Inspeção de dispositivos no Linux
Quando um recurso de harware não está funcionando, é importante identificar onde está o problema.
- Náo deteca no sistema operacional: provavelmente defeito na peça.
- Quando é detectada, mas não funciona: provável problema no lado do SO.

Portanto, uma das primeiras coisas é olhar se o SO está detectando o dispositivo corretamente. Existem 2 maneiras básicas de checar isso:

### lspci: (peripheral Componet Interconnect)
Mostra dispositivos conectados ao barramento PCI 

### lsusb (Universal Serial Bus)
Mostra os dispositivos exclusivamente USBs conectados.

Qual a função do módulo do Kernel?
Controlar o dispositivo existente. O módulo do Kernel pode fazer parte do kernel ou ser adicionado depois.

- Os modulos de kernel do linux relacionados a hardware também são chamados de drivers.
- Geralmente precisam do sudo


Resumo de atributos lspci, que podem ser combinadas:
```
lspci                    >> listagem crua das lspcis
lspci -s 01:00.0         >> Listando com um source/origem diferente
lspci -s 01:00.0 -v      >> Verbose + detalhes
lspci -s 01:00.0 -k      >> Kernel - Verifica qual o módulo do kernel está sendo utilizado (versões mais recentes)
lspci -s 01:00.0 -t      >> Tree - Árvore
```
Resumos lsusb
```
lsusb
lsusb -s 01:20
lsusb -v -d 1781:0c9f   >> Verbose + DeviceID
lsusb -v -t             >> Mostra os mapeamentos de forma hierarquica. Quando existe um módulo correspondente, seu nome aparece no final da linha do dispositivo, como em Driver=btusb
```

## Pacote kmod

Um conjunto de ferramentas para realizar tarefas comuns com os módulos do kernel Linux, como inserir, remover, listar, perificar propriedades, resolver dependências e aliases.

### lsmod
Mostra todos os módulos carregados no momento

```
$ lsmod

root@bktools:/home/bktadmin# lsmod
Module                  Size  Used by
ufs                    81920  0
qnx4                   16384  0
hfsplus               110592  0
hfs                    61440  0
minix                  36864  0
...(resumido)
```
Coluna size: mostra a RAM utilizada pelos módulos em bytes.
Coluna Used by: Mostra módulos dependentes para que o módulo principal (na coluna nome) funcione.

### modprobe 
Descarrega e recarrega módulos que não estão em uso

```
modprobe -r snd-hda-intel
```


# 101.2 - Inicialização do sistema

O é o GRUB?

- O carregador de inicialização mais popular para linux na arquitetura x86, ele exibe uma lista de sistemas operacionais disponiveis para inicializar.


Caso o GRUB não mostre a lista dos sistemas, como invoca-la em um sistema com BIOS e em um sistema com UEFI?

- Na bios: tecla shift
- Nos sistemas UEFI: Tecla ESC

No menu do grub é possível escolhqer qual dos kernels instalados deve ser carregado e passar novos parâmetros para ele, cite alguns dos principais.

- acpi (Advanced Configuration and Power Interface): Habilita suporte ao gerenciamento de energia. Ex: "acpi=off".

- init: define um iniciador de sistema alternativo. EX: "init=/bin/bash" que inicializará uma sessão do shell após o kernel.

- systemd.unit: define o destino do systemd a ser ativado, ex: "systemd.unit=graphical.target" ou "5" (runlevel do SysV).

- mem: Define a quantidade de ram disponível para o sistema, por exemplo de um convidado. Ex: "mem=512M" que limita a 512MB.

- maxcpus: Limita o numero de processadores visíveis ao sistema de máquinas, ex: "maxcpus=2".

- quiet: Oculta a maior parte das mensagens das mensagens de inicialização.

- vga: seleciona o modo de vídeo, ex: "vga=ask" que mostra uma lista dos modos disponiveis.

- root: define a partição raiz diferente da que está no boot loader, ex: "root=/dev/sda3/".

- rootflags: Opção de montagem.

- ro: permite leitura montagem inicial do arquivo raiz.

- rw: permite escrever montagem inicial do arquivo raiz.