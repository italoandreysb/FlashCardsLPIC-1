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



Geralmente é comum alterar os parametros do kernel? qual a localização do arquivo de grub?

- Não, mas pode ser util para detectar e resolver problemas.
- Localizado em /etc/default/grub, para persistir, insira na linha GRUB_CMDLINE_LINUX


É necessário gerar um novo arquivo de Configuração para o carregador de inicialização a cada vez que /etc/default/grub é alterado.
Qual o comando?

- mkconfig -o /boot/grub/grub.cfg

Quando o sistema operacional estiver rodando, os
parâmetros do kernel usados para carregar a sessão ficam disponíveis para leitura no arquivo
/proc/cmdline.

--- 
# Adicionar ao anki:
---
## Inicialização do sistema 

Quando começa a iniclização do sistema operacional?
- Quando o o carregador de inicialização(bootloader) carrega o kernel na RAM, então o kernel assume o controle da CPU, carrega a parte fundamental (conf. básica de harware eo endereçamento de memória).

O que é e para que serve o initramfs?
- Intial RAM filesystem, é um arquivo que contém um sistema de arquivos raiz temporário usado na inicialização. O objetivo dele é fornecer os módulos para kernel acessar o arquivos raiz

Qual o primeiro programa executado pelo kenel? para que serve? existem outros "primeiros programas?"
- init, responsável por iniciar todos os scripts de inicialização e os daemons do sistema.

Existem outras implementações: systemd e o upstart



Resumo:
```
+------------------------+
| Bootloader             |
| (Carrega o kernel)     |
+-----------+------------+
            |
            v
+------------------------+
| Kernel na RAM          |
| - Assume CPU           |
| - Configura hardware   |
| - Endereçamento memória|
+-----------+------------+
            |
            v
+------------------------+
| Carregamento do        |
| initramfs              |
| - Sistema raiz temp.   |
| - Módulos necessários  |
+-----------+------------+
            |
            v
+------------------------+
| Acesso ao sistema      |
| de arquivos raiz real  |
+-----------+------------+
            |
            v
+------------------------+
| Montagem de sistemas   |
| de arquivos (/etc/fstab)|
+-----------+------------+
            |
            v
+------------------------+
| Execução do processo   |
| init (ou systemd, etc.)|
| - Scripts e daemons    |
+-----------+------------+
            |
            v
+------------------------+
| Remoção do initramfs   |
| da RAM                 |
+------------------------+
```
Fale um pouco sobre os sistemas de Inicialização (init systems): SysVinit, Upstart e systemd

## 🧱 SysVinit (System V Init) (1983)

- **Origem**: Baseado no Unix System V.
- **Funcionamento**: Utiliza scripts shell localizados em `/etc/init.d/` e links simbólicos em diretórios como `/etc/rc.d/` ou `/etc/rcX.d/`.
- **Sequência Linear**: Serviços iniciam em série, com base em uma ordem numérica.
- **Runlevels**: Define diferentes estados do sistema (modo multiusuário, gráfico, etc.).
- **Desvantagens**:
  - Não paraleliza a inicialização.
  - Gerenciamento de dependências é limitado.

---

## ⚙️ Upstart (2006)

- **Origem**: Desenvolvido pela Canonical para Ubuntu.
- **Funcionamento**: Baseado em eventos (ex: "start serviço X quando a rede estiver ativa").
- **Mais dinâmico que SysV**: Responde a eventos do sistema como adição de hardware.
- **Local dos scripts**: `/etc/init/`
- **Desvantagens**:
  - Melhor que SysV, mas ainda possui limitações em dependências complexas.
  - Substituído pelo systemd nas distros modernas.

---

## 🚀 systemd (2010)

- **Origem**: Desenvolvido pela Red Hat; padrão atual em muitas distribuições Linux.
- **Funcionamento**: Utiliza *unit files* (serviços, montagens, dispositivos, sockets etc.) em `/etc/systemd/system/`.
- **Paralelismo**: Inicia serviços em paralelo com base em dependências.
- **Substitui Runlevels**: Utiliza *targets* para representar estados do sistema.
- **Comando principal**: `systemctl` (ex: `systemctl start nginx.service`)
- **Recursos**:
  - Log integrado com `journald`
  - *Socket activation*
  - Uso de *cgroups* para controle de processos
  - Substitui cron com *timers*

- **Críticas**:
  - Considerado por alguns como complexo e "monolítico", contrariando o princípio Unix de simplicidade.


## Inspeção da inicialização

Como é chamado o espaço de memória que o kernel armazena suas mensagens, incluindo mensagens de inicialização? como visualizar? e quando e como as mensagens são apagadas?
- Buffer de anel do kernel.
- As mensagens são mantidas mesmo que não sejam exibidas na inicialização (quando trocada por uma animação)
- As mensagens podem ser lidas com o comando: dmesg
- As mensagens são apagadas quando o sistema é desligado ou quando executado o "dmesg --clear".


A linha abaixo contém a saída do comando "dmesg", o que significam os números no início?
```
[ 5.705468] parport0: PC-style at 0x378 (0x778), irq 7, dma 3
```
- É a quantidade de segundos desde o início do kernel.


Nos sistemas baseados no systemd, o comando journalctl mostra as mensagens de inicialização. Quais são os parâmetros que podemos usar junto? onde ficam salvos estes logs?
- -b, --boot, -k, --dmesg



Suponha que temos um problema com inicialização que não impediu a inicialização, e queiramos checar os logs do boot atual, mas também dos anteriores, como podemos fazer?

1. Listar todos os boots ()
- journalctl --list-boots

2. Checar os logs de inicialização anteriores:
- journalctl -b 0  ("-b 1" também pode ser usado)


Caso haja um problema sério e o servidor não inicialize, podemos acessar os logs de inicialização através de outras mídias? Se sim, em qual diretório?
- Sim, /var/log/journal
- Caso esteja em outro diretório diferente do parão, utilize o parâmetro -D ou --directory



Qual é o diretório padrão das mensagens de log do systemd, posso simplesmente ler o texto plano?
- /var/log/journal/
- Não, as mensgens de log do sistema não são armazenadas em texto puro, o comando journalctl é necessário para que fiquem legíveis.


Em uma máquina equipada com firmware BIOS, onde está localizado o binário do bootstrap?
- No MBR do primeiro dispositivo de armazenamento.

O firmware UEFI suporta recursos estendidos fornecidos por programas externos, chamados
aplicativos EFI. Esses aplicativos, no entanto, têm seu próprio local especial. Em que lugar do
sistema localizam-se os aplicativos?

- São armazenados na EFI System Partition (ESP), localizada em qualquer
bloco de armazenamento disponível com um sistema de arquivos compatível (geralmente um
sistema de arquivos FAT32).


## Parei na página 33 (comece a ler e coletar as respostas dos exercicios guiados)

