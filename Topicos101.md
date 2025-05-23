# Tpopico 101 Identificar e editar configurações de hardware
## 101.1 Lição 1
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
- Náo detecta no sistema operacional: provavelmente defeito na peça.
- Quando é detectada, mas não funciona: provável problema no lado do SO.

Portanto, uma das primeiras coisas é olhar se o SO está detectando o dispositivo corretamente. Existem 2 maneiras básicas de checar isso:

### lspci: (peripheral Componet Interconnect)
Mostra dispositivos conectados ao barramento PCI 

### lsusb (Universal Serial Bus)
Mostra os dispositivos exclusivamente USBs conectados.

### Qual a função do módulo do Kernel?
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
### Como descarrega e recarrega módulos que não estão em uso?

```
modprobe -r snd-hda-intel
```


# 101.2 - Inicialização do sistema

### O é o GRUB?

- O carregador de inicialização mais popular para linux na arquitetura x86, ele exibe uma lista de sistemas operacionais disponiveis para inicializar.


### Caso o GRUB não mostre a lista dos sistemas, como invoca-la em um sistema com BIOS e em um sistema com UEFI?

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



### Geralmente é comum alterar os parametros do kernel? qual a localização do arquivo de grub?

- Não, mas pode ser util para detectar e resolver problemas.
- Localizado em /etc/default/grub, para persistir, insira na linha GRUB_CMDLINE_LINUX


### É necessário gerar um novo arquivo de Configuração para o carregador de inicialização a cada vez que /etc/default/grub é alterado. Qual o comando?

- mkconfig -o /boot/grub/grub.cfg

### Quando o sistema operacional estiver rodando, os parâmetros do kernel usados para carregar a sessão ficam disponíveis para leitura no arquivo
/proc/cmdline.

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


Caso haja um problema e o servidor não inicialize, podemos acessar os logs de inicialização através de outras mídias? Se sim, em qual diretório?
- Sim, /var/log/journal
- Caso esteja em outro diretório diferente do parão, utilize o parâmetro -D ou --directory


Qual é o diretório padrão das mensagens de log do systemd, posso simplesmente ler o texto plano?
- /var/log/journal/
- Não, as mensgens de log do sistema não são armazenadas em texto puro, o comando journalctl é necessário para que fiquem legíveis.


Em uma máquina equipada com firmware BIOS, onde está o binário do bootstrap?
- No MBR do primeiro dispositivo de armazenamento.

O firmware UEFI suporta recursos estendidos fornecidos por programas externos, chamados aplicativos EFI. Esses aplicativos, no entanto, têm seu próprio local especial. Em que lugar do sistema localizam-se os aplicativos?

- São armazenados na EFI System Partition (ESP), localizada em qualquer bloco de armazenamento disponível com um sistema de arquivos compatível (geralmente um
sistema de arquivos FAT32).


Os gerenciadores de inicialização permitem a passagem de parâmetros personalizados do kernel antes de carregá-lo. Suponha que o sistema não possa inicializar devido a uma localização incorreta do sistema de arquivos raiz. Como o sistema de arquivos  raiz correto, localizado em /dev/sda3, seria fornecido como parâmetro para o kernel?
- O parâmetro root deve ser usado, como em root=/dev/sda3.


O carregador de inicialização apresenta uma lista de sistemas operacionais a escolher quando houver mais de um sistema operacional instalado na máquina. No entanto, um sistema operacional recém-instalado pode sobrescrever o MBR do disco rígido, apagando o primeiro estágio do gerenciador de inicialização e tornando o outro sistema operacional inacessível. Por que isso não aconteceria em uma máquina equipada com um firmware UEFI?

- As máquinas UEFI não usam o MBR do disco rígido para armazenar o primeiro estágio do gerenciador de inicialização.

Qual é uma consequência comum de se instalar um kernel personalizado sem fornecer uma imagem initramfs apropriada?
- O sistema de arquivos raiz pode ficar inacessível se seu tipo tiver sido compilado como um módulo externo do kernel.


O log de inicialização tem centenas de linhas, portanto a saída do comando dmesg é frequentemente canalizada para um comando de paginação — como o comando less — para facilitar a leitura. Qual opção do dmesg faz automaticamente a paginação da saída, eliminando a necessidade de usar explicitamente um comando de paginação?
- Os comandos dmesg -H ou dmesg --human habilitam a paginação por padrão.

Um disco rígido contendo todo o sistema de arquivos de uma máquina offline foi removido e conectado a uma máquina operacional como drive secundário. Supondo que seu ponto de montagem seja /mnt/hd, como o journalctl seria usado para inspecionar o conteúdo dos arquivos de diário localizados em /mnt/hd/var/log/journal/?

- Com os comandos **journalctl -D /mnt/hd/var/log/journal** ou  **journalctl --directory=/mnt/hd/var/log/journal**

# 101.3 - Alternar runlevels/boot targets, desligar e reiniciar o sistema

### O que são daemons?
- Também são chamados de serviços.
- Também são responsáveis pelos recursos estendido subjacentes (servidor http, compartilhamento de arquivos, email..)


### Qual o primeiro programa lançado pelo kenel durante a inicialização e que recebe sempre o PID 1?
- O gerenciador de serviços ou gerenciadores de inicialização (SysV, Upstart ou Systemd)


### Por muitos anos o método de gerenciamento de serviços implementado pelo SystemV foi o mais utilizado, ainda permanece assim?
- Não, atualmente os gerenciadores de serviços são baseados no systemd (implementado pelo systemd e upstart).


### Sobre o sysVinit, como funcionam os níveis de execução (runlevels)?
- São numerados de 0 a 6, geralmente atribuidos às finalidades:

- Nível de execução 0
Encerramento do sistema.
- Nível de execução 1, s ou single
Modo de usuário único, sem rede e outros recursos não-essenciais (modo de manutenção).
- Nível de execução 2, 3 ou 4
Modo multiusuário. Os usuários podem se logar via console ou pela rede. Os níveis de execução 2 e 4 não são usados com frequência.
- Nível de execução 5
Modo multiusuário. Equivale ao nível 3, mais o login em modo gráfico.
- Nível de execução 6
Reinicialização do sistema.


### Porque alterar o runlevel(níveis de execução)? Possibilita ativar/paralisar serviços sem precisar reiniciarIsso ativa/paralisa serviços dinamicamente, sem reiniciar
- Para depurar o sistema sem interface gráfica.
- Para inicializar um servidor sem serviços desnecessários.
- Para manter um ambiente enxuto e seguro.

usando o sysVinit:
sudo init 3    # Vai para o modo texto com rede
sudo init 5    # Volta para o modo gráfico


### [Sobre SystemV] Qual é o nome e onde fica o programa responsável pelo gerenciamento dos niveis de execução (runlevels) e daemons/serviços? E Também pode ser definido no kernel ou em um arquivo, onde fica localizado este arquivo?
- Programa: init
- Localização padrão: /sbin/init
- Pode ser ajustado no: /etc/inittab

### [Sobre SystemV]  Cada nível de execução(runlevel) pode ter diversos arquivos de serviço associados, geralmente scripts, onde ficam?
- /etc/init.d/

### [Sobre SysV] Podemos personalizar os runlevels através do arquivo: /etc/inittab, como é a sintaxe utilizada?
- id:runlevels:action:process
```
id: nome genérico de até 4 caracteres, identifica a entrada.
runlevel: lista de números do runlevel
action: define como o init executará o processo
process: Pode escolher entre (boot, bootwait, sysinit, wait, respawn e ctrlaltdel)
```

### [Sysv] Ao personalizarmos o runlevel através do arquivo /etc/inittab utilizamos a sintaxe: "id:runlevels:action:process" como podemos utilizar o campo action?
- boot: O processo será executado durante a inicialização do sistema. O campo runlevels é ignorado.
- bootwait: O processo será executado durante a inicialização do sistema e o init aguardará sua conclusão
- para continuar. O campo runlevels é ignorado.
- sysinit: O processo será executado após a inicialização do sistema, qualquer que seja o nível de execução. O campo runlevels é ignorado.
- wait: O processo será executado nos níveis de execução dados e init aguardará sua conclusão para continuar.
- respawn: O processo será reiniciado caso seja encerrado.
- ctrlaltdel: O processo será executado quando o processo init receber o sinal SIGINT, disparado quando o atalho de teclado Ctrl + Alt + Del for pressionado.

### [Sobre o SysV] O que aconteceria se não escolhermos um nível de execução/runlevel no arquivo /etc/inittab, linha "id:x:initdefault" e alterarmos o "x" para 0 ou 6?
- O nível de execução/runlevel padrão seria escolhido e após iniciar o sistema ele desligaria ou reiniciaria.
- Exemplo de um /etc/inittab:
```
# Default runlevel
id:3:initdefault:
# Configuration script executed during boot
si::sysinit:/etc/init.d/rcS
# Action taken on runlevel S (single user)
~:S:wait:/sbin/sulogin
# Configuration for each execution level
l0:0:wait:/etc/init.d/rc 0
l1:1:wait:/etc/init.d/rc 1
l2:2:wait:/etc/init.d/rc 2
l3:3:wait:/etc/init.d/rc 3
l4:4:wait:/etc/init.d/rc 4
l5:5:wait:/etc/init.d/rc 5
l6:6:wait:/etc/init.d/rc 6
# Action taken upon ctrl+alt+del keystroke
ca::ctrlaltdel:/sbin/shutdown -r now
# Enable consoles for runlevels 2 and 3
1:23:respawn:/sbin/getty tty1 VC linux
2:23:respawn:/sbin/getty tty2 VC linux
3:23:respawn:/sbin/getty tty3 VC linux
4:23:respawn:/sbin/getty tty4 VC linux
# For runlevel 3, also enable serial
# terminals ttyS0 and ttyS1 (modem) consoles
S0:3:respawn:/sbin/getty -L 9600 ttyS0 vt320
S1:3:respawn:/sbin/mgetty -x0 -D ttyS1
```


## [Sobre SystemV] Depois de alterar o arquivo /etc/inittab, o que precisa ser feito?
- Execute o comando "telinit q" ou "telinit Q" (mesma coisa) para checar sintaxe do arquivo e aplicar as configurações sem reiniciar. Evita falha na inicialização por erro de sintaxe.


## [Sobre SystemV]Os scripts utilizados pelo init, localizados em /etc/rc0.d/, /etc/rc1.d/... são links simbólicos para o diretório /etc/init.d/? [S/N]
- Sim, Pois o mesmo script (Ex: /etc/rc0.d/) pode ser usado em diferentes níveis de execução

## [SystemV] Um nome de arquivo iniciado com a letra K determina que o serviço será encerrado ao entrar no nível de execução (kill). Se começar com a letra S, o serviço será iniciado ao ingressar no nível de execução (start). O diretório /etc/rc1.d/, por exemplo, terá muitos links para scripts de rede iniciados com a letra K, considerando que o nível de execução 1 é o nível do usuário único, sem conectividade de rede.[Verdadeiro ou falso]?
- Verdadeiro.

## [SystemV] Como posso, ver o runlevel atual? como posso alterar o runlevel sem precisar reiniciar?
runlevel
Retorna: "N 3"
- O "N" significa que o runlevel não foi alterado
- Alterando o runlevel sem precisar reiniciar: os comandos "telinit 1", "telinit s" ou "telinit S" mudarão o sistema para o nível de execução 1.

### [Sysv] No sysv como impedir que os usuários reiniciem a máquina através do ctrl+alt+del? 
Acesse o arquivo /etc/inittab e adicione o "-a" a linha:
```
ca::ctrlaltdel:/sbin/shutdown -a -t3 -r now
```
Os usuários no arquivo /etc/shutdown.allow estarão habilitados


## Systemd 

### [systemd] Qual o nome do conjunto de ferramentas mais usado para gerenciar os recursos dos serviços dos sistemas? e como são chamados eses serviços (ex: httd.service)?
- Systemd
- São chamados de unitis

### [Systemd] Quantos e quais são os tipos de unidades do systemd?

- Service: tipo mais comum, recursos do SO que podem ser iniciados, parados, reiniciados..
- Socket: Pode ser socket de sistem a ou socket de rede.
- device: uma unidade de dispositivo associada ao hardware, precisa ter uma regra udev!
- mount: unidade de montagem, semelhante a entrada no /etc/fstab.
- automount: unidade de montagem automatica.
- target: agrupamento de unidades gerenciadas como uma única unidade.
- snapshot: é um stado salvo do gerenciador do systemd (não disponível em todas as distros).

[Systemd] Qual o principal comando do systemd para executar relacionadas a ativação, desativação, execução, interrupção e monitoramento da unidade, para uma unidade chamada unit.service?
- systemctl start unit.service
- systemctl stop unit.service
- systemctl restart unit.service 
- systemctl status unit.service (Exibe status do unit)
- systemctl is-active unit.service (Exibe se o unit estiver rodando)
- systemctl enable unit.service (habilita o uniti na inicialização)
- systemctl disable unit.service (desabilita o unit na inicialização)
- systemctl is-enabled unit.service (verifica se o unit está habilitado 0: inicia, 1; Não, as mais recentes mostram "enabled") 

### O comando systemctl também controla os destinos do sistema, a unidade multiuser.target, por exemplo, combina as unidades do ambiente multiusuário, semelhante ao nível de execução runlevel 3 do sysV (V/F)?
- Verdadeiro

### O que faz o comando "systemctl isolate multi-user.target"?
- Faz com que o sitema mude imediatamente para o TARGET "multi-user.target" (sem interface gráfica), encerrando os serviços que não fazem parte desse TARGET e iniciando os que fazem. É o equivalente a alteração no runlevel 3 do SysV.
- Para retornar, use: **systemctl isolate graphical.target**.
- OBS: Esse ajuste não fica salvo persistentemente, retorna no reinicio.

### O systemd utiliza o /etc/initab para alterar o destino padrão do sistema? [S/N]
- Não, quem utiliza é o SysV

### [systemd] Existem 2 formas de alterar o destino padrão no systemd, fazendo com que os targets persistam mesmo após o reinício, quais são? (use multi-user.target como exemplo)

- 1ª forma: Adicionanfo o parâmetro abaixo à lista de parâmetros do kernel (em /etc/default/grub), porém esse arquivo é um modelo, sendo necessário atualizar a configuração real do GRUB executando "sudo update-grub" que é um wrapper para "grub-mkconfig -o /boot/grub/grub.cfg"
```
GRUB_CMDLINE_LINUX="rhgb quiet systemd.unit=multi-user.target"
```
wraper = invólucro, um programa que embrulha outro programa para facilitar o uso.

- 2ª forma: Modificando o link simbólico /etc/systemd/system/default.target para que ele aponte o destino desejado:
```
Systemctl set-default multi-user.target
```

### [Systemd] Onde ficam os arquivos associados a cada unidade(unit)?
- /lib/systemd/system/


### [Systemd] Como posso listar todas as unidades, somente ativas  e filtrar por tipo service ou tipo target?
- Exibe todas as Unidades:
```systemctl list-unit-files```

- Filtra por tipo service, altere para "target" se preferir: ```systemctl list-unit-files --type=service```.


### [Systemd] Qual o comandos mais usados para gerenciar eventos relacionados a consumo de energia (suspender e hibernar) e qual o local do arquivo? posso usar junto do de outro gerenciador de energia?

- Coloca o sistema em baixo consumo de energia mantendo os dados atuais na memória:
```systemctl suspend```

- Copia os dados da memória para o disco, para que o estado atual do sistema possa ser recuperado após desligamento. ```systemctl hibernate```
- Local do arquivo: ```/etc/systemd/logind.conf```. Arquivos separados ficam em ```/etc/systemd/logind.conf.d/```.
- Não pode ser usado junto de outro gerenciador de energia (acpid)

### Qual o principal gerenciador de energia no Linux e o que ele faz?

- o daemon acpid.
- Permite ajustes mais refinados das ações após eventos relacionados ao consumo de energia, como fechar a tampa do laptop, bateria fraca ou níveis de carga da bateria.


### [systemd] O que aconteceria se executasse o comando "systemctl set-default shutdown.target"?

Configuraria o sistema para tentar iniciar no modo shutdown.target POR PADRÃO, **o que é um grande problema**. Na próxima vez que tentar iniciar o sistema, ele vai travar e tentar desligar. Corresponde ao runlevel 0 do SysV

## Upstart

### [Upstart] Onde ficam os scripts de inicialização utilizados pelo Upstart? e como lista-los (mesmo com detalhes)?

-  /etc/init/
-  initctl list

### [Upstart] Como gerenciar serviços no Upstart?
```
start tty6   # Inicia 6º terminal
status tty5   # Coleta status
stop tty6   # para o serviço
```

### [Upstart] O Upstart reconhece os comandos "runlevel" e "telinit", então utiliza os arquivos /etc/inittab?

Apesar desses comandos poderem ser utilizados para verificar e alternar os nívels de execução (runlevel), o upstart não utiliza o inittab, quem utiliza é o SysV.

### [Upstart] Quem criou o Upstart, e quando parou?
O Upstart foi desenvolvido para a distribuição Ubuntu Linux para facilitar a inicialização paralela dos processos. O Ubuntu parou de usar o Upstart em 2015, quando migrou do Upstart para o systemd.

### [Upstart] Como funciona o comando shutdown?
É um comando intermediário nos procedimentos do SysV ou Systemd. Após o comando ser executado todos os programas recebem o sinal SIGTERM, seguido pelo sinal SIGKILL e o sistema é encerrado ou muda seu nível de execução.

### [Upstart] o comando shutdown possui uma sintaxe e alguns parametros, quais são?


Sintaxe:
```
shutdown [option] time [message]
```

Somente o "time" é parametro obrigatório.

```
hh:mm   # Especifica quantos minutos esperar
+m   # Aguardar x minutos antes de desigar
now ou +0   # Execução imediata
```
Parametros message: é o texto de aviso enviado a todas as sessões de terminal dos usuários logados.


### [Sysd] Com o systemd como reiniciar e desligar a máquina?
```
systemctl poweroff 
#ou 
systemctl shutdown
```
Algumas distros vinculam poweroff e reboot ao systemctl como comandos individuais:
```
$ sudo which poweroff
/usr/sbin/poweroff
$ sudo ls -l /usr/sbin/poweroff
lrwxrwxrwx 1 root root 14 Aug 20 07:50 /usr/sbin/poweroff -> /bin/systemctl
```

### [Systemd] Caso precisemos enviar um comando para todos os usuários logados, como seria?
Útil antes de reiniciara máquina.

```
wall <mensagem>

Ex:
wall Desligarei o servidor em breve
```

### [SystemV] Como o comando telinit (que altera o runlevel) pode ser usado para reiniciar o sistema? Só funciona no SystemV?
- O comando telinit 6 alterna para o nível de execução 6, ou seja, reinicia o sistema.
- Obs: O telinit também é suportado pelo Systemd apenas por compatibilidade, internamente ele traduz chamadas para comandos equivalentes no systemctl.

### O que acontece com os serviços relacionados ao arquivo /etc/rc1.d/K90network quando o sistema entra no nível de execução 1?
- Como mostra a letra K no início do nome do arquivo, os serviços relacionados serão
interrompidos.


### Usando o comando systemctl, como um usuário pode verificar se a unidade sshd.service está em execução?
-  systemctl status sshd.service
 ou
- systemctl is-active sshd.service


### Em um sistema baseado em systemd, qual comando deve ser executado para ativar a unidade sshd.service durante a inicialização do sistema?
- O comando systemctl enable sshd.service, executado pelo root.


### Em um sistema baseado em SysV, suponha que o nível de execução padrão definido em/etc/inittab seja 3, mas o sistema sempre inicia no nível de execução 1. Qual é a causa provável disso?
- Os parâmetros 1 ou S podem estar presentes na lista de parâmetros do kernel (em /etc/default/grub).


### Embora o arquivo /sbin/init possa ser encontrado nos sistemas baseados em systemd, ele é apenas um link simbólico para outro arquivo executável. Nesses sistemas, qual o arquivo apontado por /sbin/init?
- O binário principal do systemd: /lib/systemd/systemd.


### Como se verifica o destino padrão do sistema em um sistema baseado em systemd?
- O link simbólico /etc/systemd/system/default.target aponta para o arquivo da unidade definido como destino padrão. Também é possível usar o comando systemctl get-default.


### Como poderíamos cancelar uma reinicialização do sistema programada com o comando shutdown?
- Usaríamos o comando shutdown -c.