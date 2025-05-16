# Tópico 102: Instalação do Linux e administração de Pacotes

### Onde ficam salvas as informações a respeito das partições?
- As informações a respeito delas são armazenadas em uma tabela de partições
-  Essa tabela inclui informações sobre o primeiro e o último setores da partição e seu tipo e detalhes


### É correto afirmar que no sistema de arquivos descreve como as informações estarão armazenadas? Como é a estrutura?
- Sim.
- Disco >> Partição >> Sistema de arquivos


### Qual o principal objetivo do LVM?
-  O LVM é útil em situações em que é necessário adicionar mais espaço a uma partição sem precisar migrar os dados para um dispositivo maior


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


### Quais são os principais GERENCIADORES DE INICIALIZAÇÃO?
- No linux, costuma ser o GRUB2, nos mais antigos, o GRUB Legacy


### A PARTIÇÃO /boot é necessária? Mas seria recomendado instalar separado?
- Tecnicamente não é necessária, pois na maioria dos casos o GRUB pode montar o /boot na partição raiz (/boot).
- Pode ser interessante, em caso de falha no sistema de arquivos raiz, ou se usar um método não suportado de criptografia ou compactação

### Porque deixar a partição de inicialização localizada no início do disco terminando antes do cilindro 1024 (528 MB)?
- Porque a IBM PC BIOS Original exigia um tamanho máximo de cilindros, caeças e setores resultando em um tamnho de 528MB. Deixar a partição locaizada no início faz com que independente do que aconteça a máquina consegue iniciar o kernel.


## A partição do sistema EFI (ESP)