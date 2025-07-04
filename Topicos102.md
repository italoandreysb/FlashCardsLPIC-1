# Tópico 102: Instalação do Linux e administração de Pacotes

### Onde ficam salvas as informações a respeito das partições?
- As informações a respeito delas são armazenadas em uma tabela de partições
-  Essa tabela inclui informações sobre o primeiro e o último setores da partição e seu tipo e detalhes


### É correto afirmar que no sistema de arquivos descreve como as informações estarão armazenadas? Como é a estrutura?
- Sim.
- Disco >> Partição >> Sistema de arquivos


### Qual o principal objetivo do LVM?
- O LVM é útil em situações em que é necessário adicionar mais espaço a uma partição sem precisar migrar os dados para um dispositivo maior


## Pontos de montagem

### O que é um ponto de montagem?
- É quando eu vinculo um sistema de arquivos a um ponto especídico da árvore de diretórios do sistema.

### O que aconeceria se tentássemos montar um sistema de arquivos em um ponto de montagem inexistente?
- Não seria possível montar um sistema de arquivos em um ponto de montagem inexistente.

### O que aconteceria se tentássemos montar um sistema de arquivos em um ponto de montagem cujo o diretório já possui arquivos?
- Estes arquivos ficariam indisponíveis até você montar o sistema de arquivos.

### Onde são montados de forma automática os dispositivos externos conectados à maquina? e onde devo montar manual?
- Antigamente todos eram montados em **/mnt** (CD/DVD, Disquete...), mas atualmente o ponto de montagem padrão para qualquer mídia é o **/media** (como discos externos, drives flash USB, leitores de cartão de memória, discos óticos etc.) conectada ao sistema. 
- Caso a montagem seja manual, recomenda-se montar no /mnt


### Nos padrões novos de montagem, onde seriam montados, por exemplo, um drive flash USB com o nome FlashDrive conectado pelo usuário john?
- Em /media/jhon/FlashDrive, mas a maneira que isso é feito varia de acordo com o ambiente desktop.

## Mantendo as coisas separadas

### Quais os principais motivos para manter alguns os diretórios em partições separadas?
- /boot separado: garante que o sistema ainda vai inicializar em caso de flha no sistema de arquivos raiz
- /home ou /var separados: Mais fácil de reinstalar o sistema, visto que os dados dos usuários ou servidor web(por exemplo) estarão em outra partição.
- Para questões de desempenho, pode querer manter uma partição "/" em um SSD e um "/home" ou "/var" em um disco mais lento.

### O que há na partição /boot?
- Contém arquivos usados pelo bootloader/Gerenciador de Inicialização (GRUB).

Também possui arquivos como:
| config-6.8.0-60-generic       | Arquivo de configuração do kernel com as opções usadas na compilação. 
| grub/                         | Diretório do GRUB, o carregador de boot do sistema.                   
| initrd.img -> initrd.img-6.8.0-60-generic | Link simbólico para a imagem initramfs usada na inicialização.       
| initrd.img-6.8.0-60-generic   | Imagem initramfs que contém drivers e scripts usados no boot.        
| lost+found/                   | Diretório usado pelo sistema de arquivos para recuperação de dados.  
| System.map-6.8.0-60-generic   | Mapeamento de endereços de funções e símbolos do kernel.             
| vmlinuz -> vmlinuz-6.8.0-60-generic | Link simbólico para o kernel Linux.                              
| vmlinuz-6.8.0-60-generic      | Kernel Linux propriamente dito.        


### Quais são os principais gerenciadores de inicialização (bootloader)?
- No linux, costuma ser o GRUB2, nos mais antigos, o GRUB Legacy
- Gerenciadores de inicialização são diferentes de sistema de inicialização (init sistem: SysVinit, Upstart, Systemd)

### Uma partição para o /boot é necessária? Mas seria recomendado instalar separado?
- Tecnicamente não é necessária, pois na maioria dos casos o GRUB pode montar o /boot na partição raiz (/boot).
- Pode ser interessante, em caso de falha no sistema de arquivos raiz, ou se usar um método não suportado de criptografia ou compactação


## A partição do sistema EFI (ESP)

### O que você sabe sobre a partição do sistema EFI (Partição ESP)? Qual o sistem de arquivos utilizado? é obrigatório ser no início?
- A partição do sistema EFI (ESP) é utilizada por máquinas baseadas na UEFI.
- Armazena gerenciadores de inicialização e imagens do kernel para SO.
- Utiliza sistema de arquivos baseado em FAT.
- Em disco particionado com tabela GUID, ela possui o identificador global único  C12A7328-F81F-11D2-BA4B-00A0C93EC93B, se for formatado no esquema de particionamento do MBR, o Id seria 0xEF.
- Em máquinas windows a costuma sera primeira partição do disco, embora isso não seja obrigatório

### Após a instalação do sistema operacional, onde os arquivos da partição ESP são criados ou preenchidos em um sistema Linux?
- /boot/efi

## /home

### O que se sabe sobre a partição /home?
- Principal local de criação de usuários padrões
- O diretório do usuário root não é esse, é o /root.
- Alguns serviços do sistema por der usuários associados com diretórios pessoais em outros lugares.

## /var

### O que se tem no diretório /var?
Contém "dados variáveis", arquivos e diretórios que o sitema deve poder escrever durante a opraçao: Ex:
- /var/log
- /var/temp
- /var/cache
- /var/www/html (padrão apache web server) e 
- /var/lib/mysql (padrão banco de dados)

### Qual seria uma boa razão para colocar o /var em uma partição separada?
- Estabilidade, um processo defeituoso poderia encher os logs /var/log.
- Se /var estiver em /, poderia disparar um kernel panic e corromper o sistema de arquivos, o que seria muito ruim de resolver.
- Em um sistema crítico é interessante colocar o /var em um disco diferente adicionando uma camada extra de proteção.

## SWAP

### O que sabe sobre a partição swap ou partição de troca? Qual o utilitário utilizado?
- É usada para passar as páginas da memória RAM para o disco confome o necessário.
- Pode ser usado tanto partição SWAP, quanto ARQUIVOS SWAPs (pode ser 1 ou vários)
- No caso de partição, é de um tipo específico, configurada com o utilitário "mkswap"
- No caso de arquivos, serve para aumentar rapidamento o espaço  útil quando necessário.

### Qual a "Regra da memória SWAP"?
- Controversa. A regra antiga nem sempre se aplica (R. Antiga: 2x a quantidade de RAM)
- A quantidade, depende da carga de trabaho, O ideal, é que se o serviço for crítico, verificar na documentação do serviço.
- Mas A Red Hat tem um modelo:
(anexei ao anki)

## LVM

### O que seria o LVM? e qual sua estrutura base?
É uma forma de virtualizar o armazenamento, mais flexível que o particionamento tradicional.

- LV = Logical Volumes: Similar às partições, mas com flexibilidade.
    - LE: Logical Extensions
- VG = Volumes Groups: Vistos como um único dispositivo lógico.    
- PV = Phisical Volumes: Dispositivo de bloco.
    - PE = Phisical Extensions

Cada extensão lógica (LE) de forma geral é mapeada para uma extensão física (PE), mas pode ser alterado quando usado espelhamento de disco.


### Como podemos aumentar o LV (volume lógico) em uma estrutura de LVM?
- Basta adicionar mais extensões do pool disponível no Grupo de Volumes. Da mesmaforma, as extensões podem ser removidas para reduzir o LV.
- Volume lógico = PE (4MB por padrão) * LE

### Após a criação do LV (volume lógico), é correto afirmar que será visto pelo sistema operacional como um dispositivo de bloco normal. Um dispositivo será criado em /dev, com o nome /dev/VGNAME/LVNAME, em que VGNAME é o nome do Grupo de Volumes e LVNAME o nome do Volume Lógico?
- Verdadeiro

### Como os discos LV podem ser montados? (Resposta teórica)
- Podem ser formatados com o sistema de arquivos desejado usando utilitários padrão (como o mkfs.ext4, por exemplo) e montados com os métodos usuais, seja manualmente, com o comando mount, ou automaticamente, adicionando-os ao arquivo /etc/fstab.

### Onde deve estar a partição de inicialização para garantir que um PC seja capaz de carregar o kernel?
- Antes do cilindro 1024.

### Onde a partição EFI costuma ser montada?
- Em /boot/efi

### Qual a menor unidade dentro de um Grupo de Volumes?
- Os Grupos de Volumes são subdivididos em extensões

### Como se define o tamanho de um Volume Lógico?
- Pelo tamanho das extensões físicas multiplicado pelo número de extensões no volume.


### Em um disco formatado com o esquema de particionamento MBR, qual a ID da Partição do Sistema EFI?
- A ID é 0xEF

---
---
---
---
---
---

# 102.2 Instalar o gerenciador de inicialização

## Qual o gerenciador de inicialização na maioria das distribuições linux?
- GRUB, que carrega um kernel de um sistema operacional.

## O que é o grub 2? e quais as principais vantagens relacionadas ao seu antecessor?
- Uma remodelagem completa do grub legacy (antigo), se tornou mais robusto e poderoso. Parece uma linguagem Script;
- Pode iniciar imagens ISOs LiveCDs direto do disco rígido;
- Suporte à temas;
- Melhor suporte à arquitetura não-x86;
- suporte universal para UUIDs (ajuda a identificar discos e partições);

## Quando o GRUB (legacy) parou o seu desenvolvimento?
- Em 2005, desde então a maioria das principais distros linux vem com o GRUB 2. Mas ainda podemos encontrar sistemas usando o GRUB legacy

## Em discos com esquemas de particionamento MBR (Master Boot Record) e GPT (GUID Partition Table) onde fica o bootloader (gerenciador de inicialização)?
- MBR: No setor MBR (primeiros 512 bytes)
- GPT: No arquivo .EFI na partição ESP (geralmente FAT32)

# Quais as limitações do esquema de particionamento MBR?
- Até 2TB
- Originalmente máximo de 4 partições primárias, mais tarde, 3 partições primárias e 1 estendida subdividida em várias partições lógicas.


## Os discos particionados em GPT podem ser usados em computadores com BIOS tradicional ou também com UEFI? [V/F]
- Verdadeiro. Em máquinas com BIOS, a segunda parte do GRUB é armazenada em uma partição especial de inicialização da BIOS.


## Em sistemas com firmware UEFI, o grub é carregado pelo firmware a partir dos arquivos grubia32.efi (para sistemas de 32 bits) ou grubx64.efi (para sistemas de 64 bits) em qual partição?
- ESP (EFI System Partition)


# A Partição /boot
### Por que, apesar de não ser imprescindível, é aconselhável utilizar uma partição /boot separada em sistemas Linux modernos?
- Porque a partição /boot separa os arquivos necessários para o processo de inicialização do restante do sistema de arquivos, o que pode aumentar a confiabilidade e facilitar o gerenciamento do sistema, além de garantir acesso mais fácil ao kernel e ao carregador de boot.


### Quais são as situações específicas em que uma partição /boot separada se torna necessária para garantir o funcionamento do GRUB 2?
- Quando a partição raiz do sistema está criptografada, compactada, ou utiliza um sistema de arquivos que o GRUB 2 não suporta, é necessário ter uma partição /boot separada, pois o GRUB precisa acessar diretamente os arquivos de inicialização.

### O que sabe sobre o conteúdo da partição /boot?

-  Arquivo de configuração (config-VERSION): Arquivo de configuração do kernel, este arquivo é gerado automaticamente quando um novo kernel é compilado, não deve ser editado manualmente. Ex: config-4.15.0-65-generic

- Mapa do sistema (System.map-VERSION): Tabela de consulta de nomes e símbolos com sua posição correspondente na memória. Pode ser últil para resolver problemas de kernel panic. EX: System.map-4.15.0-65-generic

- Kernel do Linux (vmlinux-VERSION): É o kernel propriamente dito. EX: vmlinux-4.15.0-65-generic. Também pode vir com um "z" no final (vmlinuz), que indica que o arquivo foi compactado.

- Arquivos relacionados ao Gerenciador de inicialização ficam em /boot/grub/:
 - /boot/grub/grub.cfg para o GRUB2  **ou** /boot/grub/menu.lst para o GRUB Legacy
 - /boot/grub/i386-pc: Módulos
 - /boot/grub/locale: arquivos de tradução
 - /boot/grub/fonts: fontes 

 # GRUB 2
 ### Qual a diferença entre "sudo su" e "sudo su -"?
 - Enquanto o "sudo su" eleva o usuário atual para root mantendo o ambiente atual (como variáveis de ambiente e diretório de trabalho) o "sudo su -" carrega o ambiente completo do root como se tivesse feito login no shell.

 ### O que fazer caso seu sistema se recuse a iniciar? (checar melhor)
 - Será necessário iniciar a partir de um Live-CD ou um disco de recuperação, descobrir qual a partição de inicialização, monta-la, executar o utilitário grub-install para reinstalar o grub2.

 ```
fdisk -l /dev/sda     # Liste os diretórios
mkdir /mnt/tmp     # Crie o diretório temporário
mount /dev/sda1 /mnt/tmp    # Monte o diretório temporário
grub-install --boot-directory=/mnt/tmp /dev/sda    # Aponte o utilitário para o dispositivo de inicialização. 
```
Caso não tenha uma partição de inicialização (Coluna boot com asterisco) mas possua o diretório /boot, substitua o último comando por:
```
grub-install --boot-directory=/boot /dev/sda
```

### Como podemos checar com o fdisk se o disco possui uma partição ou um diretório /boot?
Execute o comando: "fdisk -l /dev/sda". 
A partição de inicialização é identificada com o * na coluna boot:
```
Device    Boot   Start    End      Sectors    Size    Id   Type
/dev/sda1  *     2048   2000895    1998848    976M    83   Linux
/dev/sda2       2002942 234440703 232437762   110,9G   5   Extended
/dev/sda5       2002944 18008063   16005120   7,6G     82  Linux swap / Solaris
/dev/sda6       18010112 234440703 216430592 103,2G 83 Linux
```
Caso esteja instalando em um sistema que não possui uma partição de inicialização, possivelmente não terá um "*", mas sim uma partição chamada "BIOS Boot":
```
Device       Start       End   Sectors Size Type
/dev/sda1     2048      4095      2048   1M BIOS boot
/dev/sda2     4096   2101247   2097152   1G Linux filesystem
/dev/sda3  2101248 157284351 155183104  74G Linux filesystem
```

## Configurando o grub2:

### Qual a localização do arquivo de configuração (.cfg) do GRUB 2? recomenda-se edita-lo manualmente? Onde fica o que podemos editar?

- Localização:  "/boot/grub/grub.cfg", 
- Não recomenda-se edita-lo manualmente pois é gerado automaticamente.
- O que podemos editar é o "/etc/default/grub"

Obs: lembre-se de sempre gerar o arquivo .cfg novo após a edição do grub com o comando: update-grub (ou "grub-mkconfig -o /boot/grub/grub.cfg).


### Quais os principais parâmetros que podemos utilizar no /etc/default/grub para controlar o comportamento do GRUB 2 como o kernel padrão de inicialização, tempo limite, parâmetros extras da linha de comando, etc?

- GRUB_DEFAULT= Define a entrada de menu padrão, pode ser numerico (0,1..) ou o nome do item, ex: debian. Tabém pode ser "saved", explicado abaixo:

- GRUB_SAVEDEFAULT= se marcada como "true" e a "GRUB_DEFAULT=" como "saved" a opção padrão de inicialização será sempre a última utilizada.

- GRUB_TIMEOUT= O tempo limite, em segundos, para que a entrada do menu padrão seja selecionada. Se "0", o sistema entra sem exibir o menu, se "-1" o sistema aguardará até que o usuário selecione uma opção, independente do tempo.

- GRUB_CMDLINE_LINUX= Lista as opções de linha de comando que serão adicionadas às entradas do kernel do Linux.

- GRUB_CMDLINE_LINUX_DEFAULT= Por padrão, duas entradas de menu são geradas para cada kernel do Linux, uma com as opções padrão e uma entrada para recuperação. Com esta opção, você pode incluir parâmetros extras que serão adicionados apenas à entrada padrão.

- GRUB_ENABLE_CRYPTODISK= Se definido como "y", comandos como "grub-mkconfig", "update-grub" e "grub-install" procuram por discos criptografados e adicionam os comandos necessários para acessá-los (Habilita o suporte para discos criptografados) e desabilita o a inicialização automática (GRUB_TIMEOUT= com valor diferente de -1), o GRUB precisa ser capaz de pedir a senha para descriptografá-lo antes de carregar o kernel do sistema.

### O que acontece quando rodamos o comando "update-grub"?
- busca por kernels e sistemas operacionais na máquina e gera as entradas de menu correspondentes no arquivo /boot/grub/grub.cfg

 É possível adicionar novas entradas manualmente aos arquivos de script dentro do diretório /etc/grub.d

 ### Caso queiramos adicionar manualmete novas entradas ao grub, como faremos?
 - Adicionando scripts de entradas no diretório "/etc/grub.d", geralmente no arquivo "40_custom".
 - Padrão: Estes arquivos são executados em ordem numérica, 05_debian_theme executado antes de  10_linux e assim sucessivamente.
 - Sintaxe básica:
```
menuentry "Default OS" {
set root=(hd0,1)
linux /vmlinuz root=/dev/sda1 ro quiet splash
initrd /initrd.img
}
```
- No exemplo acima, o sistema de arquivos raiz está localizado no primeiro disco (hd0), primeira partição (,1) ou sda1.

### Caso queiramos adicionar entradas manualmente ao grub 2, podemos editar os arquivos em /etc/grub.d, geralmente o arquivo "40_custom", e inseri-lo baseado em um modelo. Mas caso queiramos inserir baseado no UUID  (Universally Unique Identifier) do disco, qual o comando para checar esses identificadores? 

- Comando: "ls -l /dev/disk/by-uuid/"
- Inserindo no arquivo /etc/grub.d/40_custom:
```
menuentry 'Meu Linux com UUID' {
    insmod ext2
    search --no-floppy --fs-uuid --set=root f3a5e2b0-5c4f-4b9e-94e7-9c749f412345
    linux /boot/vmlinuz-linux root=UUID=f3a5e2b0-5c4f-4b9e-94e7-9c749f412345 ro quiet
    initrd /boot/initramfs-linux.img
}
```
linha linux: aponta para o kernel do sistema (vmlinuz);  
linha initrd aponta para o disco de RAM inicial;  
--no-floppy = ignore disquetes;  
--set=root

## Interagindo com o GRUB 2

### Como podemos interagir com o GRUB 2, quando não mostra o menu de opções de ações ao ligar o servidor?
- Pressione shift (se for UEFI).
- Para editar uma opção selecione com as setas e pressione "E"
- Será aberto o conteúdo da menuentry, como definido na /boot/grub/grub.cfg. Ao finalizar salve com F10.

# Inicialização a partir do shell do GRUB 2 (pag 76)

### Como podemos acessar o shell do grub 2?
- Pressione C na tela do menu (ou Ctrl + C ) na janela de edição.
Aparecerá um prompt de comando como este: grub >

- Lembre-se de que esse menu não aparecerá se GRUB_TIMEOUT estiver definido com 0 em /etc/default/grub

### Caso exista uma configuração incorreta em uma entrada de menu que cause falha na inicialização, como podemos resolve? (!Não consegui validar este procedimento em disco em gpt! Página 76)
1. Acesse o shell do grub pressionando "C" no menu, ou "control + c" na janela de edição. Verá um "grub >". 
2. Descubra onde está a partição de inicialização digitando "ls". O exemplo abaixo retorna somente um disco (hd0), com apenas uma partição. A primeira partição do hd0 é chamada msdos1 porque o disco foi particionado usando o esquema de particionamento MBR. Se ele fosse particionado usando GPT, o nome seria gpt1.
```
grub> ls
(proc) (hd0) (hd0,msdos1)
```
3. Defina a partição de inicialização:  
```grub> set root=(hd0,msdos1)```
4. Carregue o kernel do Linux com o comando linux:  
```grub> linux /vmlinuz root=/dev/sda1```
5. Carregue o disco RAM inicial com initrd, seguido pelo caminho completo para o arquivo initrd.img:  
```grub> initrd /initrd.img```

- Obs: Os discos e partições listados serão diferentes no seu sistema. Em nosso exemplo, a primeira partição do hd0 é chamada msdos1 porque o disco foi particionado usando o esquema de particionamento MBR. Se ele fosse particionado usando GPT, o nome seria gpt1.

### O que é o shell mínimo de recuperação grub rescue> e em quais situações ele aparece? Como localizo as partições?
O "grub rescue>" é um ambiente de recuperação minimalista disponibilizado pelo GRUB (Grand Unified Bootloader) quando este não consegue localizar ou carregar sua configuração principal (grub.cfg) ou módulos necessários para a inicialização do sistema. Ele aparece geralmente após problemas como:

- Exclusão ou corrupção de arquivos importantes do GRUB;
- Alterações no esquema de partições do disco;
- Instalação ou remoção incorreta de sistemas operacionais;
- Falhas no disco.

Esse shell oferece um conjunto muito limitado de comandos, permitindo ao usuário realizar diagnósticos básicos, localizar manualmente as partições corretas e, em alguns casos, iniciar o carregamento do sistema operacional.

```
1. Listar partições e sistemas de arquivos detectados:
grub rescue> ls

2.Definir a partição correta onde está o /boot/grub:
grub rescue> set prefix=(hd0,msdos1)/boot/grub

3.carregue os módulos normal e linux com o comando insmod:
grub rescue> insmod normal
grub rescue> insmod linux

Após isso, se for bem-sucedido, o GRUB completo será carregado.
```

### Quando instalar o grub legacy? E como fazer isso a partir do shell do GRUB?
- Compatibilidade com hardware antigo
- Sistemas embarcados
- Dualboot com sistemas antigos


1. No shell do GRUB (digite c no menu de inicialização para acessar o prompt grub>)
```
grub> root (hd0,0)
```

2. Se você não souber qual dispositivo contém o diretório /boot, peça ao GRUB para procurá-lo com
o comando find, como abaixo:
```
grub> find /boot/grub/stage1
(hd0,0)
```
3. Defina a partição de inicialização conforme as instruções acima e use o comando
setup para instalar o GRUB Legacy no MBR e copiar os arquivos necessários no disco:
```
grub> setup (hd0) 
```
5. Reinicie o sistema normalmente.

Obs: Pode ser necessário especificar o local da imagem inicial do disco RAM para o sistema operacional
com o parâmetro initrd: 

```
# This line is a comment
title My Linux Distribution
root (hd0,0)
kernel /vmlinuz root=/dev/hda1
initrd /initrd.img
```

### Onde ficam localizadas as entradas e configurações do menu do grub legacy? posso editar diretamente?
- /boot/grub/menu.lst
- Pode editar diretamente

```
# This line is a comment
title My Linux Distribution     # Título do sistema na tela de menu
root (hd0,0)                    # Informa o dispositivo ou partição de inicialização
kernel /vmlinuz root=/dev/hda1  # Caminho completo para a imagem do kernel (relativo ao dispositivo em "root")
```
A configuração abaixo resume a configuração acima em uma linha:
```
kernel (hd0,0)/vmlinuz root=dev/hda1
```
Ambos carregam o arquivo vmlinuz a partir do diretório root (/) da primeira partição do primeiro disco (hd0,0).

### Ao contrário do GRUB 2, no GRUB Legacy tanto as partições quanto os discos são numerados a partir de zero. [verdadeiro ou falso]?
- Verdadeiro
- Grub: (hd0,0)
- Grub2: (hd0,1)

### O parâmetro "root=/dev/hda1" após o comando kernel informa ao kernel do Linux qual partição deve ser usada como sistema de arquivos raiz. Esse comando pertence ao GRUB Legacy ou ao Kernel Linux?
- Pertence ao Kernel Linux.

### O GRUB Legacy tem um design modular, no qual módulos (geralmente armazenados como arquivos .mod em /boot/grub/i386-pc) podem ser carregados para adicionar recursos extras, como suporte a hardware incomum, sistemas de arquivos ou novos algoritmos de compactação. Qual a extensão dos arquivos de módulo? e como carregar um módulo específico?
- Extensão .mod
- Utilize o comando module seguido do caminho completo do módulo. Ex: module /boot/grub/i386-pc/915resolution.mod

### O GRUB Legacy pode ser usado para carregar sistemas operacionais não suportado? 
- Sim, chama-se chainloading (carregamento em cadeia)

### Como seria uma entrada típica para o chainloading (carregamento em cadeia) no Grub Legacy do windows?

```
# Load Windows
title Windows XP  
root (hd0,1)    # Especifica a partição (segunda do primeiro disco)
makeactive      # Indica partição ativa, funciona apenas com particoes primarias DOS.
chainload +1    # Diz para o grub para carregar o primeiro setor, geralmente ficam os gerenciadores de inicializaçao.
boot            # Executa o gerenciador de inicialização e carrega o sistema.
```

### Quais são as etapas necessárias para alterar as configurações do GRUB 2?
- Efetuar as alterações no arquivo /etc/default/grub, depois atualizar a configuração com update-grub.

### Em qual arquivo devem ser adicionadas as entradas de menu personalizadas do GRUB 2?
- /etc/grub.d/40_custom

###  Onde são armazenadas as entradas de menu do GRUB Legacy?
- /boot/grub/menu.lst

### Como podemos entrar no shell do GRUB a partir de um menu do GRUB 2 ou GRUB Legacy?
- Pressionando C na tela de menu

### Imagine que você tenha um disco identificado como /dev/sda com diversas partições. Que comando pode ser usado para descobrir qual a partição de inicialização em um sistema?
- Use fdisk -l /dev/sda. A partição de inicialização estará marcada na lista com um asterisco (*).

### Qual o comando para descobrir o UUID de uma partição?
- Use ls -la /dev/disk/by-uuid/ e procure pelo UUID que aponta para a partição


### Considere a entrada abaixo para o GRUB 2 e altere-a para que o sistema inicialize a partir de um disco com o UUID 5dda0af3-c995-481a-a6f3-46dcd3b6998d:
```
menuentry "Default OS" {
set root=(hd0,1)
linux /vmlinuz root=/dev/sda1 ro quiet splash
initrd /initrd.img
}
```

Altere a linha: ```search --set=root --fs-uuid 5dda0af3-c995-481a-a6f3-46dcd3b6998d```

### Como configurar o GRUB 2 para aguardar 10 segundos antes de inicializar a entrada de menu padrão?
- Adicionando o parâmetro GRUB_TIMEOUT=10 a /etc/default/grub.

### Em um shell do GRUB Legacy, quais são os comandos para instalar o GRUB na primeira partição do segundo disco?
```
grub> root (hd1,0)
grub> setup (hd1)
```
---
---
---
---
---
---
# 102.3 Controle de bibliotecas compartilhadas

### O que são bibliotecas compartilhadas (ou objetos compartilhados)?
- Assim como as bibliotecas físicas permitem que os livros sejam compartilhados por várias pessoas, as bibliotecas de software são coleções de código que podem ser usadas pro vários programas diferentes.

### Cite as 2 etapas importantes na criação de um arquivo executável a partir do código fonte e os 2 métodos possíveis de serem usados:
1. Compilador transforma o código fonte em código de máquina (armazenado no chamados "arquivos-objeto")
2. O Vinculador (linker) combina os arquivo-objeto e os vinculas às bibliotecas para gerar o arquivo final.

- Métodos Estáticos ou dinâmicos.

### Explique os 2 métodos usados no processo de compilação de um executável a partir do método fonte:
- Método estático ou dinâmico.

- Bibliotecas Estáticas: Uma biblioteca é mesclada com o programa no momento do vínculo, uma cópia do código é incorporada no programa. Torna o programa independente, porém mais pesado.

- Bibliotecas Compartilhadas (ou dinâmicas): O programa faz a referência às bibliotecas, no entanto, nenhum código da biblioteca permanece no programa, logo, a bibliteca deve estar disponível em tempo de execução. É uma abordagem mais econômica, apenas uma cópia da biblioteca é carregada na RAM, mesmo se usada por vários programas.

### Como é o padrão de nomenclatura de uma biblioteca estática e compartilhada?

- Bibliotecas estáticas: Terminam em ".a", Ex: libthread.a

- Bibliotecas Compartilhadas: Nome da biblioteca (normalmente com prefixo "lib"), seguido de "so", de "shared object", seguido da versão da biblioteca. Ex: libpthread.so.0

### É verdade que a maioria dos sistemas linux já possuem todas as bibliotecas estáticas e dinâmicas?
- A maioria dos sistemas linux já contém as bibliotecas compartilhadas/dinâmicas, porém, as bibliotecas estáticas podem não estar presentes pois são usadas em arquivos dedicados.

### A biblioteca compartilhada glibc (GNU C) de arquivo libc.so.6 fica onde? é o nome real?


```
$ ls -l /lib/x86_64-linux-gnu/libc.so.6
lrwxrwxrwx 1 root root 12 feb
 6 22:17 /lib/x86_64-linux-gnu/libc.so.6 -> libc-2.24.so
 ```

- Usar nomes de arquivos mais gerais para fazer referência a arquivos de bibliotecas compartilhadas nomeados por uma versão específica é uma prática comum.

### Onde geralmente estão localizadas as bibliotecas compartilhadas em um sistema Linux?

- /lib
- /lib32
- /lib64
- /usr/lib
- /usr/local/lib

### O arquivo ld.so.conf é um arquivo de configuração do linker dinâmico no Linux, ele define caminhos adicionais de bibliotecas para o carregador dinâmico (ld.so ou ld-linux.so). Qual a localização dele?

```
$ cat /etc/ld.so.conf
include /etc/ld.so.conf.d/*.conf
```

### O que faz o /etc/ld.so.conf e o que tem dentro dele?
- Ele define caminhos adicionais de bibliotecas para o carregador dinâmico.
- Esse arquivo invoca todos os .conf do /etc/ld.so.conf.d/.

### Qual comando deve ser executado sempre que alteramos ou adicionamos arquivos de configuração das bibliotecas compartilhadas, para que ele leia esses arquivos, crie os links simbólicos necessários, atualize o cache em /etc/ld.so.cache e facilite a localização das bibliotecas pelo sistema?

- $ sudo ldconfig

- Sempre rodar com sudo, pois exige aceso de escrita ao /etc/ld.so.cache

### O comando ldconfig é utilizado em sistemas Linux para configurar a ligação dinâmica de bibliotecas compartilhadas. Ele atualiza o cache das bibliotecas compartilhadas localizadas em diretórios específicos. Quais o principais parametros do ldconfig?

- sudo ldconfig -v
- sudo ldconfig -p  (ou --print-cache) # exibe armazenamento no cache atual

Note como a cache emprega o soname completo dos vínculos:
```
$ sudo ldconfig -p |grep libfuse
libfuse.so.2 (libc6,x86-64) => /lib/x86_64-linux-gnu/libfuse.so.2
```

### Além da estrutura do ldconf, como posso adicionar tempoarariamente os caminhos para bibliotecas compartilhadas? E como checar? Use /user/local/mylib como exemplo.

- Através da variável LD_LIBRARY_PATH;
- É composto de um conjunto de diretórios serparados por ":" onde as bibliotecas são buscadas.
- Adicionando: LD_LIBRARY_PATH=/user/local/mylib

- Checando: echo $LD_LIBRARY_PATH. Deve retornar o valor.

### 1. Como posso adicionar /usr/local/mylib ao caminho da biblioteca na sessão atual do shell e exportá-lo para todos os processos secundários originados desse shell? 2. Como remover a variável de ambiente? 3. Como posso torna-las permanentes?

1. $ export LD_LIBRARY_PATH=/usr/local/mylib
2. $ unset LD_LIBRARY_PATH
3. Adicione a seguinte linha ao /etc/bash.bashrc ou ~/.bashrc: export LD_LIBRARY_PATH=/usr/local/mylib

### É correto dizer que: LD_LIBRARY_PATH está para as bibliotecas compartilhadas como PATH está para os executáveis[Sim / Não]? 
- Sim

### 1. Qual o comando usado para buscar as bibliotecas compartilhadas requeridas por um programa específico? 2.Posso procurar dependência de objetos compartilhados?

1. ldd <caminho_absoluto>
- Ex: ldd /usr/bin/git

2. Sim, $ ldd /lib/x86_64-linux-gnu/libc.so.6
- Com a opção -u (ou --unused), o ldd imprime as dependências diretas não utilizadas (se existirem)

### Porque que existem dependências não utilizadas? 
- Isso está relacionado ao vinculador no momento da criação do binário.
- Embora o programa não precise de uma biblioteca não utilizada ela fica vinculada e rotulada como "NEEDED" nas informações sobre arquivo-objeto.
- Podemos checar isso utilizando o readelf ou objdump.

###  Você desenvolveu um programa e deseja adicionar um novo diretório de biblioteca compartilhada em seu sistema (/opt/lib/mylib). Você escreve o caminho absoluto em um arquivo chamado mylib.conf. 
1. Em que diretório deve ser armazenado esse arquivo?
2. Qual comando deve ser executado para que as alterações sejam totalmente efetivas?
3. Qual comando você usaria para listar as bibliotecas compartilhadas exigidas por kill?

1. /etc/ld.so.conf.d
2. ldconfig
3. ldd /bin/kill

### Qual o comando que exibe informações sobre o objeto? e como usar?
- objdump
- objdump -p /lib/x86_64-linux-gnu/libc.so.6 | grep NEEDED
- objdump -p /lib/x86_64-linux-gnu/libc.so.6 | grep SONAME     # Filtrando para exibir o soname 
- objdump -p /bin/bash | grep NEEDED


---
---
---
---
---

# 102.4 Utilização do sistema de pacotes Debian

### Antes da existência dos gerenciadores de pacotes, como eram distribuidos os softwares?
- Em um arquivo comprimido (geralmente um .tar.gz) com um código fonte que o usuário precisava descomprimir e compilar.

### Quais as principais ferramentas de gerenciamento de pacotes presentes no debian e alguns derivados?

- dpkg: Debian package 
- apt: Advanced package tool

### Quais os comandos para instalar e remover um pacote .deb baixado?
```# dpkg -i PACKAGENAME```  
```# dpkg -r PACKAGENAME```

### Quando utilizo o comando "dpkg -r PACKAGENAME.deb" para remover o pacote também removo os arquivos de configuração? se não, como removo?
- Não, se a ideia for remover tudo que esteja associado ao pacote, use a opção -P (purge) em vez de -r.

### Podemos forçar o dpkg a instalar ou remover um pacote, mesmo que as dependências não sejam atendidas, adicionando o parâmetro --force como em "dpkg -i --force PACKAGENAME". Mas quais as consequências?
- Isso provavelmente deixará o pacote, ou mesmo o sistema, em um estado de falha. Não use --force, a menos que tenha certeza absoluta do que está fazendo.

### Como posso inspecionar um pacote com o dpkg para ver informações de arquitetura, mantenedor, dependências...?
- Utilize o -I (maiúsculo)
```dpkg -I google-chrome-stable_current_amd64.deb```

### (dpkg) Como posso obter uma lista de todos os programas instalados? e uma lista de todos os arquivos instalados por um pacote específico:
```dpkg --get-selections```  
```dpkg -L PACKAGENAME```

- Exemplo prático: gerar lista via dpkg --get-selections > packages.list e depois restaurar em outra máquina com dpkg --set-selections.

### Como podemos descobrir qual pacote possui um arquivo específico no sistema?
- Utilizando o dpkg-query
```# dpkg-query -S /usr/bin/unrar-nonfree```

Sempre utilizar o caminho completo do arquivo.

## Reconfigurando os pacotes instalados

### Caso necessite restaurar as configurações iniciais de um programa, como se fosse "novo", instalado do zero, como fazer com o dpkg?

```dpkg-reconfigure PACKAGENAME```

- Isso faz um backup dos arquivos antigos, descompacta os novos nos diretórios corretos e executa o script de pós instalação fornecido pelo pacote.

## Ferramenta de pacotes avançada (apt)

### O apt (Advanced Package Tool) é um substituto para o dpkg? S/N
- Não, o apt está mais para um "front end", simplificando as operaçoes.

### É verdade que além de repositórios remotos, o apt permite instalação de softwares em servidores locais ou remotos e até de disco CD/ROM?
- Verdadeiro.
- As distribuições Linux, como Debian e Ubuntu, mantêm seus próprios repositórios. Outros repositórios podem ser  mantidos por desenvolvedores ou grupos de usuários para fornecer software que não está disponível nos principais repositórios de distribuição.

### Além do utilitário amigável apt, Quais são os outros utilitários que interagem com o apt?

- apt-get: usado para baixar, instalar, atualizar ou remover pacotes do sistema.
- apt-cache: usado para executar operações, como pesquisas, no índice do pacote.
- apt-file: usado para buscar arquivos dentro de pacotes.

Muitos dos comandos de apt são os mesmos de apt-get, de forma que eles são intercambiáveis em diversos casos. Porém, como o apt pode não estar instalado em um sistema, é recomendável aprender a usar o apt-get e o apt-cache.

### Qual o comando utilizado para atualizar o índice dos pacotes para recuperar informações sobre pacotes novos e atualizados?
``` apt-get update``` ou ```apt update```

- Posteriormente pode-se instalar o pacote com: ```apt-get install <PACKAGE>```

### Ao instalar ou remover pacotes, o APT executará a resolução automática de dependências. Assim, todos os pacotes adicionais necessários para o pacote que você está instalando também serão instalados, e os pacotes que dependem do pacote que você está removendo também serão removidos [V/F]?
- Verdadeiro

### Como posso remover os pacotes instalados com apt e dpkg junto com seus arquivos de configuração?

 - ```# apt-get remove --purge p7zip``` ou ```# apt-get purge p7zip```
 - ```# dpkg --purge p7zip``` ou ```# sudo dpkg -P nome-do-pacote```

### O que são dependências quebradas em um sistema? Como corrigir?
-  significa que um ou mais dos pacotes instalados dependem de outros pacotes que não foram instalados ou não estão mais presentes. Isso pode ocorrer devido a um erro do APT ou a um pacote instalado manualmente.
- Utilize ```apt-get install -f``` ou ```apt install -f``` para corrigir, o argumento significa --fix-broken

### Quais comandos para atualizar o índice do pacote e depois atualizar automaticamente todos os pacotes?
- ```# apt-get update```
- ```# apt-get upgrade```

### Quando instalamos ou atualizamos um pacote, o arquivo .deb correspondente é baixado em um diretório de cache local antes do pacote ser instalado. Por padrão, esse diretório é /var/cache/apt/archives. Onde ficam os arquivos parcialmente baixados? e como recuperar o espaço em cache?
- Os parciais ficam em "/var/cache/apt/archives/partial/"
- Utilize o comando ```apt clean``` ou ```apt-get clean```

## Buscando pacotes

### Como podemos executar operações no índice dos pacotes, como por exemplo, buscar um pacote específico ou listar quais pacotes tem um arquivo determinado? Como podemos mostrar informações completas do pacote?
- Buscar um pacote específico: # apt-cache search p7zip  ou (apt search)
- Mostrar informações completas do pacote: # apt-cache show liblzma5 ou (apt show)

É possível usar expressões regulares com o padrão de pesquisa (não abordaremos aqui).

### O APT usa uma lista de fontes para saber de onde obter pacotes. Esta lista é armazenada no arquivo sources.list, onde está localizado?
- /etc/apt

###  O APT usa uma lista de fontes para saber de onde obter pacotes. Esta lista é armazenada no arquivo sources.list, que exibe: "deb http://us.archive.ubuntu.com/ubuntu/ disco main restricted universe multiverse" o que significam os componentes após o a versão do ubuntu 19 (disco dingo)?

- Cada componente representa um conjunto de pacotes. Esses componentes podem ser diferentes nas diversas distribuições Linux. Por exemplo, no Ubuntu e derivados, eles são:

- main: Contém pacotes de código aberto oficialmente suportados.
- restricted: contém software de código fechado oficialmente suportado, como drivers de dispositivo
para placas gráficas, por exemplo.
- universe: contém software de código aberto mantido pela comunidade.

- multiverse: contém software não suportado, de código fechado ou protegido por patente.
No Debian, os principais componentes são:
- main: consiste em pacotes compatíveis com as Debian Free Software Guidelines (DFSG), que não
dependem de software externos a essa área para operar. Os pacotes incluídos aqui são
considerados parte da distribuição Debian.
- contrib: contém pacotes compatíveis com DFSG, mas que dependem de outros pacotes que não estão
no main.
- non-free: contém pacotes que não são compatíveis com o DFSG.
- security: contém atualizações de segurança.
- backports: contém versões mais recentes dos pacotes que estão em main. O ciclo de desenvolvimento
das versões estáveis do Debian é bastante longo (cerca de dois anos), garantindo que os usuários possam obter os pacotes mais atualizados sem precisar modificar o repositório principal main.

### Como faço para adicionar um novo repositório para obter novos pacotes?
1. Altere o arquivo /etc/apt/sources.list
2. Recarregue o índice: apt-get update ou apt update.
3. Estarão disponíveis para instalar com o: apt-get install ou apt install.

## o que faz a ferramenta apt-file?
- Pode ser usado para executar mais operações no índice do pacote, como listar o conteúdo de um pacote ou localizar um pacote que contenha um arquivo específico. Provavelmente precise reinstala-lo.

```
# apt-get install apt-file
# apt-file update    # Atualizando cache do pacote usado para o apt-file
# apt-file list unrar   # Listando conteudo de um pacote
```

### Qual a diferença entre apt-file search e  dpkg-query?
- O "# apt-file search" também busca por pacotes não instalados.

### Qual seria o comando para instalar um pacote chamado package.deb usando dpkg?
```# dpkg -i package.deb```

### Usando dpkg-query, descubra qual pacote contém um arquivo chamado 7zr.1.gz.
```# dpkg-query -S 7zr.1.gz```

### É possível remover um pacote chamado unzip do sistema usando dpkg -r unzip se o pacote file-roller depender dele? Se não, qual seria o jeito correto de fazer isso?
- Não. O dpkg não resolve dependências e não permite remover um pacote se outro pacote instalado depender dele. neste exemplo, podemos primeiro remover file-roller (pressupondo que nada depende dele) e em seguida remover unzip, ou remover os dois ao mesmo tempo com:

```dpkg -r unzip file-roller```

### Usando o utilitário apt-file, como podemos descobrir qual pacote contém o arquivo /usr/bin/unrar?
- Use o parâmetro search seguido pelo caminho (ou nome de arquivo):
```apt-file search /usr/bin/unrar```

### Usando o apt-cache, qual seria o comando para exibir informações para o pacote gimp?
- Use o parâmetro show seguido pelo nome do pacote:
``` # apt-cache show gimp```

### Considere um repositório com pacotes de fontes Debian para a distribuição xenial, hospedado em http://us.archive.ubuntu.com/ubuntu/ e com pacotes para o componente universe. Qual seria a linha correspondente a adicionar a /etc/apt/sources.list?
- Os pacotes fonte são do tipo deb-src, então a linha deve ser:
``` deb-src http://us.archive.ubuntu.com/ubuntu/ xenial universe```

- Essa linha também poderia ser adicionada a um arquivo.list fem /etc/apt/sources.list.d/. Ela pode ter qualquer nome, mas é melhor que seja descritivo, algo como xenial_sources.list.

###  Ao compilar um programa, aparece uma mensagem de erro reclamando que o arquivo de cabeçalho zzip-io.h não está presente no seu sistema. Como você pode descobrir qual pacote fornece esse arquivo?
- Use apt-file search para descobrir qual pacote contém um arquivo que não está presente
no sistema:
```# apt-file search zzip-io.h```

###  Como podemos ignorar um aviso de dependência e remover um pacote usando dpkg, mesmo que haja pacotes que dependam dele no sistema?
- O parâmetro --force poderia ser usado, mas isso jamais deve ser feito a menos que se saiba exatamente o que se está fazendo, já que existe um risco enorme de que o sistema seja deixado em um estado inconsistente ou “quebrado”.


###  Como é possível pode obter mais informações sobre um pacote chamado midori usando apt?
- Use apt-cache show seguido pelo nome do pacote:
```# apt-cache show midori```

### Antes de instalar ou atualizar pacotes com o apt, qual comando deve ser usado para garantir que o índice do pacote esteja atualizado?
- apt-get update deve ser usado. Ele baixa os índices mais recentes do pacote dos repositórios descritos no arquivo /etc/apt/sources.list ou no diretório /etc/apt/sources.list.d/.

---
---
--- 
---
---

## 102.5 Utilização do sistema de pacotes RPM e YUM

### É verdade que o fato de várias distribuições trabalharem com os formatos de pacotes (rpm, o yum, o dnf e o zypper) faz com que não precisemos procurar uma versão específica para a distribuição desejada?
- Não. Apesar do formato .rpm ser comum, isso não significa que qualquer pacote .rpm funcionará em qualquer distro .rpm-based. Um pacote compilado para RHEL 8 pode não funcionar corretamente no RHEL 9 (ou CentOS 7), devido a diferenças nas bibliotecas e APIs, Um pacote .rpm para x86_64 não funcionará em sistemas aarch64 (ARM), mesmo na mesma distro, O openSUSE/SUSE usa .rpm, mas seu sistema de gerenciamento (zypper) e estrutura de pacotes são diferentes, então Um .rpm feito para RHEL quase nunca será compatível com o openSUSE.

### Qual a ferramenta essencial para gerenciar pacotes de software em sistemas baseados/derivados no Red Hat?
- RPM Package Manager (rpm) 

### Como instalar pacotes .rpm em sistemas derivados/baseados no Radhat?
- rpm -i <pacote>

### Nos sistemas derivados/baseados no RadHat, como posso atualizar um pacote já existente, mas baseados em uma outra versão?
- rpm -U <package>

- Obs: se não houver uma versão do pacote instalada, uma versão será instalada. Caso não queira instalar, apenas utilizar, utilize o parâmetro "-F".

### Os comandos "rpm -i -v -h" são o mesmo que "rpm -ivh"?
- Sim

### Como podemos apagar um pacote? 
-  rpm -e wget    (erase)

- Se tiver alguma dependência, poderá gerar um erro:
```
# rpm -e unzip
error: Failed dependencies:
/usr/bin/unzip is needed by (installed) file-roller-3.28.1-2.el7.x86_64
```
- Podemos passar vários nomes de pacotes para o rpm -e para remover vários pacotes de uma só vez.

### Muitas vezes, um pacote pode depender de outros para funcionar como pretendido. O que acontece se um pacote rpm necessitar de uma dependência inexistente no seu computador/servidor?
- O rpm verifica se essas dependências estão instaladas em seu sistema; se não estiverem, ele não conseguirá instalar o pacote. Nesse caso, o rpm lista os pacotes faltantes. No entanto, ele não pode resolver dependências sozinho, Cabe ao usuário encontrar os pacotes .rpm com as dependências correspondentes e instalá-los.

- Mostrará algo como:
```
# rpm -i gimp-2.8.22-1.el7.x86_64.rpm
error: Failed dependencies:
    babl(x86-64) >= 0.1.10 is needed by gimp-2:2.8.22-1.el7.x86_64
    gegl(x86-64) >= 0.2.0 is needed by gimp-2:2.8.22-1.el7.x86_64
    gimp-libs(x86-64) = 2:2.8.22-1.el7 is needed by gimp-2:2.8.22-1.el7.x86_64
```

### Utilizando o rpm, como podemos listar todos os pacotes?
- ```# rpm -qa```
- "-qa" vem de query all

### Utilizando o rpm, como podemos obter informações sobre um pacote?
- ```# rpm -qi unzip```
- "-qi" vem de query info. Se quiser a lista de arquivos de um programa que não for instalado, insira a opção "-p": ```# rpm -qip PACKAGENAME```

### Utilizando o rpm, como podemos ver uma lista dos arquivos que estão dentro de um pacote instalado?
- ```# rpm -ql unzip```
- "ql" de query list. Se quiser a lista de arquivos de um programa que não for instalado, insira a opção "-p": ```# rpm -qlp atom.x86_64.rpm```

### Utilizando o rpm, como podemos saber qual pacote possui um arquivo em específico?
```
# rpm -qf /usr/bin/unzip
    unzip-6.0-19.el7.x86_64
```
- No exemplo acima, o arquivo /usr/bin/unzip pertence ao pacote unzip-6.0-19.el7.x86_64.



## YellowDog Updater Modified (YUM)

### O que é o YellowDog Updater Modified (YUM)?
O yum foi originalmente desenvolvido como Yellow Dog Updater (YUP), uma ferramenta para gerenciamento de pacotes na distribuição Linux Yellow Dog. Com o tempo, evoluiu para gerenciar pacotes em outros sistemas baseados em RPM, como Fedora, CentOS, Red Hat Enterprise Linux e Oracle Linux. Em termos de funcionalidade, ele é semelhante ao utilitário apt dos sistemas baseados em Debian, sendo capaz de buscar, instalar, atualizar e remover pacotes, além de gerir automaticamente as dependências. O yum pode ser usado para instalar um único pacote ou para atualizar um sistema inteiro de uma vez só. 

### Utilizando o YellowDog Updater Modified (YUM), como poesquisar por um pacote? atualizar um pacote?
- Pesqusando: ```# yum search 7zip```
- Instalando: ```# yum update wget```

### Utilizando o YUM, como podemos  verificar se há uma atualização disponível para um pacote específico?
- ```# yum check-update PACKAGENAME```

### Utilizando o YUM, como posso atualizar todos os pacotes no sistema para os quais existam atualizações?
- ```# yum update```

### Utilizando o YUM, se uma tentativa de instalação de um pacote falha por conta de dependência de uma biblioteca, o rpm mostra quais arquivos estão faltando, mas como checar os pacotes que o fornecem estes arquivos? Utilize a lib "libgimpui-2.0.so.0" como exemplo.
```
# yum whatprovides libgimpui-2.0.so.0
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
* base: mirror.ufscar.br
* epel: mirror.globo.com
* extras: mirror.ufscar.br
* updates: mirror.ufscar.br
2:gimp-libs-2.8.22-1.el7.i686 : GIMP libraries
Repo
 : base
Matched from:
Provides
 : libgimpui-2.0.so.0
```
- A resposta é gimp-libs-2.8.22-1.el7.i686. Em seguida podemos instalar o pacote com o comando yum install gimp-libs

- Isso também funciona para arquivos já existentes no seu sistema. Por exemplo, para saber de
onde o arquivo /etc/hosts veio, você pode usar: yum whatprovides /etc/hosts

#### Utilizando o YUM, como fazer para saber quem provê arquivo /etc/hosts?
- ```# yum whatprovides /etc/hosts```


### Utilizando o YUM, como fazemos para obter informações sobre um pacote, como versão, arquitetura, descrição, tamanho e mais? utilize o firefox como exemplo.
- ```# yum info firefox```



## Administrando os repositórios de software



### Para o Yum, em qual diretório ficam listados os "repos"?
- Para o yum, os “repos” estão listados no diretório /etc/yum.repos.d/. Cada repositório é representado por um arquivo .repo, como CentOS-Base.repo.
- Repositórios adicionais podem ser incluídos pelo usuário acrescentando um arquivo .repo no diretório mencionado acima, ou no final de /etc/yum.conf. No entanto, a maneira recomendada de adicionar ou gerenciar repositórios é usar a ferramenta yum-config-manager. Para adicionar um repositório, use o parâmetro --add-repo, seguido da URL para um arquivo .repo. 
- Ex: ```# yum-config-manager --add-repo https://rpms.remirepo.net/enterprise/remi.repo```


### Utilizando o YUM, como obter uma lista de todos os repositórios disponíveis?
- ```# yum repolist all```

- Os repositórios disabled (desabilitados) serão ignorados ao instalar ou atualizar o software. Para habilitar ou desabilitar um repositório, use o utilitário yum-config-manager, seguido pelo ID do repositório.


### Utilizando o YUM, como desabilitar e habilitar os updates?
- Desabilitando: ```# yum-config-manager --disable updates```
- Habilitadno: ```# yum-config-manager --enable updates```

Depois de desabilitado, nenhum pacote desse repositório será listado nem instalado em operações como yum install, yum update ou yum upgrade.
Útil quando você quer evitar que o sistema busque atualizações daquela fonte (por exemplo, para manter versões congeladas, usar só mirror alternativo, ou depurar conflitos).

### O Yum armazena os pacotes baixados e os metadados associados em um diretório de cache (geralmente /var/cache/yum). À medida que o sistema é atualizado e novos pacotes são instalados, essa cache pode ficar bem grande, qual comando utilizamos para limpar o cache e recuperar o espaço em disco?
- ```# yum clean packages``` exclui os pacotes baixados e metadata (yum clean metadata)


## DNF - pg 137

### O que é o dnf?
- É uma ferramenta de gerenciamento de pacotes usada no Fedora, é um fork do yum, então muitos comandos são semelhantes.

### Cite alguns dos principais comandos do dnf.
- Busca de pacotes:  
$ dnf search PATTERN, onde PATTERN é aquilo que você está buscando. Por exemplo, dnf search unzip mostra todos os pacotes que contêm a palavra unzip no nome ou descrição.

- Obter informações sobre um pacote:  
$ dnf info PACKAGENAME

- Instalar pacotes:  
sudo dnf install PACKAGENAME, onde PACKAGENAME é o nome do pacote que se deseja instalar.
Para encontrar o nome, faça uma busca.

- Remover pacotes:  
sudo dnf remove PACKAGENAME

- Atualizar pacotes:  
sudo dnf upgrade PACKAGENAME para atualizar um só pacote. Omita o nome do pacote para
atualizar todos os pacotes do sistema.

- Descobrir qual pacote fornece um arquivo específico:  
$ dnf provides FILENAME

- Obter uma lista de todos os pacotes instalados no sistema:  
$ dnf list --installed

- Listar o conteúdo de um pacote:  
$ dnf repoquery -l PACKAGENAME

## Zypper - PG 139

### O que é o Zyper?
- Ferramenta de gerenciamento de pacotes usada no SUSE Linux e OpenSUSE. Emtermos de recursos, é semelhante ao apt e ao yum, sendo capaz de instalar, atualizar e remover pacotes de um sistema, com resolução automática de dependências.

### Utilizando o zyper, como podemos fazer a atualização dos pacotes e matadados contidos nos repositórios dele?
```
# zypper refresh
Repository 'Non-OSS Repository' is up to date.
Repository 'Main Repository' is up to date.
Repository 'Main Update Repository' is up to date.
Repository 'Update Repository (Non-Oss)' is up to date.
All repositories have been refreshed.
```
### É verdade que utilizando o Zypper, podemos habilitar a função de atualização automática caso a caso? ou seja, atualizando alguns repositórios automaticamente antes da consulta ou da instalação de um pacote, e outros atualizados manualmente?
- Verdadeiro

### Utilizando o Zypper, como podemos procurar um pacote (no repositório, não em instalados) pelo nome ou obter uma lista de todos os pacotes instalados no sistema?
- Procurando pelo nome: ```# zyper se gnumeric```. Pode-se utilizar o "se" ou search.
- listando instalados: ```# zyper se -i```.


### Utilizando o Zypper, como checar se um pacote específico está instalado?
- ```# zypper se -i firefox```
- Para pesquisar apenas nos pacotes não-instalados, adicione o parâmetro "-u" ao operador "se"


### O Zypper também pode instalar pacotes RPM, como podemos fazer isso?
- ```# zypper in /home/jhon/newpackage.rpm```

### Utilizando o Zypper, como podemos atualizar os pacotes instalados no sistema?
- ```# zypper update```

- Obs: Se quiser listar apenas as atualizações disponíveis, sem instalar nada, use zypper list-updates.

### Utilizando o Zypper, como podemos remover um pacote?
- ```# zypper rm <unrar>``` Ou utilize "remove" no lugar de "rm".

- Lembre-se de que a remoção de um pacote também remove outros pacotes que dependem dele.
- Obs: Para ver quais pacotes contêm um arquivo específico, use o operador de pesquisa seguido pelo parâmetro --provides e o nome do arquivo (ou o caminho completo para ele)

### Utilizando o Zypper, Como podemos obter informações sobre metadados associados a um pacote?

- ```# zypper info gimp```

### O zypper também pode ser usado para gerenciar repositórios de software, como podemos ver uma lista de todos os repositórios atualmente registrados no seu sistema?
- ```# zypper repos```

- Na coluna Enabled vemos alguns repositorios ativados ou não, para alterar utilizer o modifyrepo com -e (enable) ou -d (disable).
- ```# zypper modifyrepo -d repo-non-oss```

### É verdade que o zypper possui um recurso de atualização automática que pode ser ativado caso a caso nos repositórios. Quando ativado, esse sinalizador faz com que o zypper execute uma operação de atualização (como se executássemos zypper refresh) antes de trabalhar com o repositório especificado?

- Sim, O processo pode ser controlado com os parâmetros -f e -F do operador modifyrepo:
- ```# zypper modifyrepo -F repo-non-oss```
- ```# zypper modifyrepo -f repo-non-oss```

### Como podemos adicionar ou remover um repositprio utilizando o zypper?
- ```# zypper addrepo http://packman.inode.at/suse/openSUSE_Leap_15.1/ packman```
- ``` # zypper removerepo packman```

## Respostas aos exercicios

### Usando o rpm em um sistema Red Hat Enterprise Linux, como você instalaria o pacote file-roller-3.28.1-2.el7.x86_64.rpm de maneira a exibir uma barra de progresso durante a instalação?
- Use o parâmetro -i para instalar um pacote e a opção -h para habilitar os “sinais de hash” mostrando o progresso da instalação. 
- R: ```# rpm -ih file-roller-3.28.1-2.el7.x86_64.rpm.```

### Usando o rpm, descubra qual pacote contém o arquivo /etc/redhat-release.
- Estamos solicitando informações sobre um arquivo, por isso usamos o parâmetro -qf: rpm -qf /etc/redhat-release

### Como você usaria o yum para procurar atualizações para todos os pacotes do sistema?
- Use a operação check-update sem um nome de pacote: yum check-update.

### Usando o zypper, como desabilitaríamos um repositório chamado repo-extras?
- Use a operação modifyrepo para alterar os parâmetros de um repositório, e o parâmetro -d para desabilitá-lo: zypper modifyrepo -d repo-extras.

### Se tivermos um arquivo .repo descrevendo um novo repositório, onde esse arquivo deve ser colocado para poder ser reconhecido pelo DNF?
- Os arquivos .repo para o DNF devem ser postos no mesmo local usado pelo YUM, dentro de /etc/yum.repos.d/.

### Como usar o zypper para descobrir qual pacote possui o arquivo /usr/sbin/swapon?
- zypper se --provides /usr/sbin/swapon.

###  Como obter uma lista de todos os pacotes instalados no sistema usando o dnf?
- ```#  dnf list --installed```

### Usando o dnf, qual o comando para adicionar um repositório localizado  em https://www.example.url/home:reponame.repo ao sistema?
- O trabalho com repositórios é uma “mudança de configurações”, por isso use o config-manager e o parâmetro --add_repo: dnf config-manager --add_repo https://www.example.url/home:reponame.repo.

### Como podemos usar o zypper para conferir se o pacote unzip está instalado?
- ```# Precisamos fazer uma busca (se) nos pacotes instalados (-i): zypper se -i unzip.```

### Usando o yum, descubra qual pacote fornece o arquivo /bin/wget.
- Para descobrir quem fornece um arquivo, use whatprovides e o nome do arquivo: yum whatprovides /bin/wget.

---
---
--- 
---
---