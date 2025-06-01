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
- Contém arquivos usados pelo Gerenciador de Inicialização (não são bootloaders (SysV, Upstart Sysd,)) 


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

### Como podemos checar se o disco possui uma partição ou um diretório /boot?
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

### Explique sobre os 2 métodos usados no processo de compilação de um executável a partir do método fonte:
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

### Quais o principais parametros do ldconf?

- sudo ldconfig -v
- sudo ldconfig -p  (ou --print-cache) # exibe armazenamento no cache atual

Note como a cache emprega o soname completo dos vínculos:
```
$ sudo ldconfig -p |grep libfuse
libfuse.so.2 (libc6,x86-64) => /lib/x86_64-linux-gnu/libfuse.so.2
```

### Além da estrutura do ldconf, como posso adicionar tempoarariamene os caminhos para bibliotecas compartilhadas? E como checar? Use /user/local/mylib como exemplo.

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
