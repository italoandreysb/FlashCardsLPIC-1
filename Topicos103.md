# 103.1 Trabalhar na linha de comando

### Como podemos descobrir em que diretório estamos atualmente?
- ``` $ pwd```

### Qual comando pode ser utilizado para descobrir a versão do kernel do linux atualmente carregada?
- ``` $ uname -a```

## Obtendo informações sobre comandos

### Qual comando no Linux exibe todas as informações do sistema, incluindo nome do kernel, versão, arquitetura e nome do host?
- ```$ uname -a```

### Os desenvolvedores Linux escrevem arquivos man e os distribuem junto com os utilitários que criam, onde podemos acessar manuais de alguma ferramenta, por exemplo "man uname". Mas como podemos pesquisar por um programa cujo nós não lembramos o nome?
- ```$ apropos kernel```
- Nesse caso, serve para procurar nas páginas de manual (man pages) por qualquer comando, função ou arquivo cuja descrição contenha a palavra “kernel”.
- Obs: caso retorne o erro "nothing appropriate", geralmente precisa-se atualizar o banco de dados: ```sudo mandb```


### Caso não precise da documentação completa de um comando, pode obter seus dados básicos rapidamente usando type, ex: ```$ type uname```, o que pode retornar "uname is hashed (/bin/uname)". O que significa o termo hashed?
- A razão para isso é que o usuário recentemente usou uname e, para aumentar a eficiência
do sistema, o comando foi adicionado a uma tabela de hash para ficar mais acessível na próxima vez que for executado.
- Pode ser usado ```hash -d <programa>``` para limpar a tabela específica ou rebootado o servidor.

### Como podemos encontrar a localização absoluta de um comando? 
- ```$ which <programa>```

### Como podemos exibir informações sobre comandos internos do shell (built-in)?
- Utlizando o comando help.
- Ex: ```help cd```

## Usando o histórico de comandos

### Como poderia pesquisar por um comando que já foi executado?
- history | grep {comando}

### O que é o arquivo .bash_history e o que tem nele?
- Arquivo oculto, localizado no diretório de cada usuário e contém os comandos utilizado pelo usuário. Porém não contém todos os comandos pois os arquivo só é gravado após a finalização da sessão atual.

### Use o sistema man para determinar qual licença de copyright é atribuída ao comando grep.
- Rode man grep e desça até a seção “Copyright” do documento. Note que o programa usa um copyright da Free Software Foundation.

### Imprima na tela as últimas vinte linhas do banco de dados dinâmico history e do arquivo .bash_history para compará-los.
```
$ history 20
$ tail -n 20 .bash_history
```

### Use a ferramenta apropos para identificar a página man na qual se encontra o comando necessário para mostrar o tamanho de um dispositivo de bloco físico conectado em bytes, ao invés de megabytes ou gigabytes.
- Uma maneira seria executar apropos com o string block, ler os resultados, notar que lsblk lista os dispositivos de bloco (e assim seria a ferramenta mais provável para dar a resposta que buscamos), rodar man lsblk, descer pela seção “Description” e notar que -b exibe o tamanho de um dispositivo em bytes. Finalmente, executamos lsblk -b para ver o que aparece.

```
$ apropos block
$ man lsblk
$ lsblk -b
```


## 103.1 Lição 2

### Então, como identificamos os valores atuais para cada uma de nossas variáveis de ambiente? 
```
$ env
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
XDG_RUNTIME_DIR=/run/user/1000
XAUTHORITY=/run/user/1000/gdm/Xauthority
XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/game
s:/snap/bin
GJS_DEBUG_TOPICS=JS ERROR;JS LOG
[...]
```

### Para que serve o comando "echo"?
- Exibe na tela o que mandar.
- Inclusive, valores de variáveis: echo $PATH
```
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/sn
ap/bin
```
- O comando "$ set" faz algo parecido, mas com algumas diferenças, a principal delas é que set exibe todas as variáveis e funções.

## Criando novas variáveis de ambiente

### Podemos criar uma variável local personalizada? Se sim, como?
- Sim, exemplo:  myvar=hello
- A string à esquerda será o nome da nova variável e a string à direita, seu valor. TUDO JUNTO, SEM ESPAÇO.
- OBS: Criando a variável desta forma só ficará disponível localmente (nesta sessão shell), se abrir outra sessão, ou bash filho, a variável não irá junto. Para exportar para outros shells digite: $ export myvar

## Removendo as variáveis de ambiente

### Como podemos limpar uma variável chamada myvar?
- Se não foi propagada (com o export), pode apenas reinicar o PC ou fechar o shell pai.
- Uma outra forma é $ unset myvar


## Usando aspas para escapar dos caracteres especiais

### Como podemos criar um arquivo com o nome "my big file"?
- touch "my big file"
- Touch 'my big file'
- touch my\ big\ file\

### Ao executar comandos utilizando strings, qual a diferença entre " (áspas duplas) e ' (áspas simples) ?
- O escape com " preserva os valores especiais do cifrão, da crase e da barra invertida. Já o escape com o ' faz com que todos os caracteres sejam interpretados literalmente.

--- 

## 103.2 Processar fluxos de texto usando filtros


### O que significa cat e o que acontece se não designarmos nada para o cat ler, apenas executando "cat"?
- Vem do "concatenar"
- se você não especificar de onde cat deve ler, ele lerá a entrada padrão (o que você digitar) e produzirá o que for lido na janela do terminal (a saída padrão).

### O que acontece se digitarmos "cat mytextfile > mynewtextfile", depois  "cat mynewtextfile"
- Vai  copiar mytextfile para mynewtextfile, depois ler o arquivo mynewtextfile

### Como posso comparar 2 arquivos?
- Utilize o comando diff file1 file2

### Qual a diferença dos operadores ">" e ">>"?
- O ">" sobrescreve o conteúdo do arquivo, se o arquivo já existir, ele é apagado e substituído pela nova saída, Se não existir, ele é criado.
- o ">>" adiciona o texto ao fim do arquivo.


### Como poderia canalizar uma saída de um cat para um grep não case sensitive?
- cat mytextfile | grep -i this

### Existem alguns comandos para lidar com arquivos compactados, como por exemplo, arquivos bzip e xz e gzip. Como podemos visualizar estes arquivos sem descompacta-los?
- bzcat para arquivos compactados bzip, 
- xzcat para arquivos xz,
- zcat para arquivos gzip,
- Exemplo: zcat arquivo.txt.gz, zless arquivo.txt.gz e zgrep "palavra" arquivo.txt.gz.

### Quais os programas utilizados para ler apenas o início do texto ou apenas o fim do texto?
- head e tail.
- ex: ```tail -n 10 <file> ou head -n 10 <file>```

### o quefaz o comando "less" em um arquivo e o que diferencia do "more"?
- Enquanto o "more" mostra o arquivo página por página, mas só avança para frente, o less é mais moderno e poderoso, permite rolar pra frente e para trás e também carrega o arquivo por partes, sendo mais rápido.

### o que faria o comando "grep -v closed /var/log/auth.log"?
- listará todas as correspondências que NÃO CONTIVEREM a palavra "closed" para o arquivo auth.log.
- o "v" é de inverted-match

### o que faz o comando "sed" como ele se diferencia do "grep"?
- O sed significa Stream Editor (editor de fluxo), ele lê linha por linha e pode modificar o fluxo. O grep não altera o texto, apenas filtra as informações.

### Qual a diferença dos comandos "sed -n /cat/p < ftu.txt" e "sed -i.backup s/cat/dog/ ftu.txt"?
- O primeiro comando mostra no terminal apenas as linhas do arquivo que têm a palavra cat.
Não altera o arquivo.
- O segundo comando modifica o arquivo onde houver "cat" e passa a se "dog" e cria uma arquivo com a extensão .backup. O -i significa in-place.

## Garantindo a integridade dos dados

### O que é um checksum?
- Uma soma de verificação (checksum) é um valor derivado de um cálculo matemático, baseado em uma função hash criptográfica, em relação a um arquivo. Existem diferentes tipos de funções hash criptográficas com diferentes intensidades. 

- Para o exame, você deverá estar familiarizado com o uso de md5sum,
sha256sum e sha512sum

### Utilizando o sha256, como podemos gerar e verificar um checksum do arquivo file.txt? (Creio que também funcione com o md5sum e sha256sum, valide!)
- Gere o checksum e direcione para um arquivo: ```sha256sum file.txt > sha256.txt```
- Verifique a soma com o "-c": ```sha256sum -c sha256.txt```
- Deve retornar como: ```file.txt: ok```, se retornar " WARNING: 1 computed checksum did NOT match" não corresponde.

## Um olhar mais aprofundado nos arquivos

### O que faz o comando od (octal dump)?
- Apenas lista os arquivos em formato octal. A primeira coluna sempre é o deslocamento de bytes para cada linha de saída.
```
$ od ftu.txt
0000000 075142 060543 005164 060543 005164 072543 005164 062550
0000020 062141 066012 071545 005163 062155 071465 066565 067012
0000040 005154 062157 070012 071541 062564 071412 062145 071412
0000060 060550 032462 071466 066565 071412 060550 030465 071462
0000100 066565 071412 071157 005164 070163 064554 005164 060564
0000120 066151 072012 005162 067165 070551 073412 005143 075170
0000140 060543 005164 061572 072141 000012
0000151
```
- Também podemos vizualizar um conteúdo de um arquivo em formato hexadecimal com a opção -x, onde cada uma das oito colunas após o deslocamento de bytes é representada por seus equivalentes hexadecimais.
```
$ od -x ftu.txt
0000000 7a62 6163 0a74 6163 0a74 7563 0a74 6568
0000020 6461 6c0a 7365 0a73 646d 7335 6d75 6e0a
0000040 0a6c 646f 700a 7361 6574 730a 6465 730a
0000060 6168 3532 7336 6d75 730a 6168 3135 7332
0000100 6d75 730a 726f 0a74 7073 696c 0a74 6174
0000120 6c69 740a 0a72 6e75 7169 770a 0a63 7a78
0000140 6163 0a74 637a 7461 000a
0000151
```

### Para que serve o comando od? Cite um exemplo útil para o uso.
- Octal dump, Ele mostra o conteúdo bruto de um arquivo, o "-c" manda o od exibir os bytes como caracteres ASCII, na prática mostra o arquivo byte por byte
```
$ od -c ftu.txt 
```
- Caso não queira ver o o deslocamento de bytes (primeira coluna), utilize:  
```
$ od -An -c ftu.txt
```

### Alguém acaba de doar um laptop para sua escola e você deseja instalar Linux nele. Ele veio sem manual e você é obrigado a inicializá-lo a partir de um pen drive USB sem nenhuma interface gráfica. Aparece um terminal shell e você sabe que, para cada processador presente, haverá uma linha dedicada no arquivo /proc/cpuinfo abaixo.  Usando os comandos grep e wc, exiba o número de processadores presentes.
```
processor : 0
vendor_id : GenuineIntel
cpu family : 6
model : 158

(linhas puladas)

processor : 1
vendor_id : GenuineIntel
cpu family : 6
model : 158

(more lines skipped)
```

Comando: 
```
cat /proc/cpuinfo | grep processor | wc -l
```
ou
```
grep processor /proc/cpuinfo | wc -l
```
Com o sed: ```$ sed -n /processor/p /proc/cpuinfo | wc -l```  (-n para não imprir nada exceto o que corresponder a "processor")

### Qual a diferença entre o comando ```sed -n /processor/p /proc/cpuinfo | wc -l``` e ``` $ sed -n /processor/p /proc/cpuinfo | sed -n '$=' ```?
```
Esta sequência de comando fornece resultados idênticos aos do exemplo anterior, no qual a saída de sed foi canalizada para um comando wc. A diferença, aqui, é que em vez de usar wc -l para contar o número de linhas, sed é novamente invocado para fornecer uma funcionalidade equivalente. Mais uma vez, estamos suprimindo a saída de sed com a opção -n, exceto para a expressão que  estamos chamando explicitamente, que é '$='. Essa expressão diz ao sed para encontrar uma correspondência para a última linha ($) e então imprimir o número dessa linha (=).
```

### O que faz o comando ```$ grep -v ":/bin/bash$" /etc/passwd | wc -l```?
- Filtra todos as correspondencias para a quantidade de contas de usuário no sistema que NÃO usam o Bash (/bin/bash) como shell padrão.

### O que faz o comando ```$ cut -d: -f3 /etc/passwd | wc -l```?
- cut:  → extrai parts especificas de cada de texto, geralmente colunas ou campos.
- -d: → (delimiter) usa os dois-pontos (:) como separador de campos (padrão do arquivo /etc/passwd)
- -f3 → (Fields) pega apenas o terceiro campo de cada linha
```
O primeiro campo de qualquer linha do arquivo /etc/passwd é o nome do usuário, o segundo é tipicamente um x indicando que a senha do usuário não está armazenada aqui (ela é criptografada no arquivo /etc/shadow). O terceiro é o id do usuário (UID) e o quarto é o id do grupo (GID). Portanto, isso deve nos fornecer o número de usuários.
```

### Liste apenas a primeira, a última e a décima linha do arquivo /etc/passwd.
- ```$ sed -n -e '1'p -e '10'p -e '$'p /etc/passwd```
```
Explicação:
sed  : Editor de fluxo (stream editor) muito usado para filtrar e transformar texto
-n  : desativa a impressão automática
-e : Permite especificar múltiplos comandos (cada -e é uma instrução separada)
'1'p : Quando chegar na linha 1, execute o comando p → imprima essa linha
'10'p : Quando chegar na linha 10, imprima essa linha
'$'p : Quando chegar na última linha do arquivo ($ significa última linha), imprima
/etc/passwd: Arquivo de entrada
```

### Liste todos os usuários do arquivo /etc/passwd no grupo 1000 (use sed para selecionar apenas o campo apropriado) do arquivo mypasswd. sabendo que após o GID o campo seguinte começa com uma caractere maiúsculo. ex: ```carol:x:1000:2000:Carol Smith,Finance,,,Main Office:/home/carol:/bin/bash```

- ```sed -n /:1000:[A-Z]/p /etc/passwd```


```
Explicação
sed : processa e edita texto, linha a linha.
-n : desatia a impressão automática.
/.../ : expressão regular
-p : print linha

A expressão regular: :1000:[A-Z] significa: um caracter ":" número "1000", outro ":" e [A-Z] uma letra maiúscula de A a Z.
```

## Exercícios exploratórios:

### Usando novamente o arquivo mypasswd dos exercícios anteriores, imagine um comando Bash que selecione um indivíduo do Main Office para ganhar uma rifa. Use o comando sed para imprimir apenas as linhas do Main Office e, em seguida, uma sequência de comando cut para recuperar o primeiro nome de cada usuário a partir dessas linhas. Depois, classifique esses nomes aleatoriamente e imprima apenas o nome principal da lista.Primeiro, explore como o parâmetro -R manipula a saída do comando sort. Repita esse comando algumas vezes em sua máquina (note que será preciso colocare 'Main Office' entre aspas simples para que o sed o trate como uma única string):

```
$ sed -n /'Main Office'/p mypasswd | cut -d: -f5 | cut -d, -f1 | sort -R

Eis uma solução:

$ sed -n /'Main Office'/p mypasswd | cut -d: -f5 | cut -d, -f1 | sort -R | head -1
```
Explicação:

```
sed → processa texto linha a linha
-n → não imprime tudo automaticamente
/'Main Office'/p → imprime apenas as linhas que contêm Main Office
mypasswd → arquivo de entrada

cut  → extrai parts especificas de cada de texto, geralmente colunas ou campos.
-d: → delimitador é :
-f5 → pega o 5º campo

Com isso temos: "Nome,Local" direcionamos outro cut (cut -d, -f1)

sort: Comando para ordenar linhas
-R: (random sort) embaralha as linhas

head: mostra apenas as primeiras linhas
-1 : mostra apenas a primeira linha

Ps: esse procedimento também pode ser feito com o AWK

```

### O que faz o comando uniq -c? ex: $ uniq -c dados.txt
```
uniq: mostra apenas as linhas unicas ADJASCENTES (uma abaixo da outra)
-c: (count) conta as linhas iguais
```
Arquivo dados.txt:
```
apple
apple
banana
banana
banana
orange
```
Retornaria:
```
2 apple
3 banana
1 orange
```


### O que faz o comando "$  paste firstname lastname department | tr '\t' , > names.csv"? considerando que os arquivos "firstname" possuem o primeiro nome, "lastname" o sobrenome e o "department" o setor. 

```
paste : junta arquivos linha a linha (por padrão utiliza o TAB como separador (\t))
| : direciona a saída para o próximo comando
tr '\t' ,  : substitui o tab (\t) por (significa translate or delete characters) ,
> names.csv : direciona para o arquivo names.csv
```
- para o tr, existe uma opção ```-s``` (squeeze) que reduz as repetições consecutivas, ex:"isso   tem     muitos   espaços", com o ```tr -s ' ' ```, seria: "isso tem muitos espaços".

### Suponha que a planilha names.csv criada no exercício anterior seja um arquivo importante e queremos ter certeza de que ninguém vai adulterá-lo desde o momento do envio até a recepção pelo destinatário. Como podemos garantir a integridade desse arquivo usando md5sum?

```
$ md5sum names.csv

Que retorna:
61f0251fcab61d9575b1d0cbf0195e25 names.csv
```
- Após isso, pode enviar o arquivo por uma fonte segura, ex: FTP. E o resumo de mensagens geradas, envie por outra via também segura. Se o arquivo tiver sido ligeiramente alterado, o resumo será totalmente diferente.
- Sempre que você disponibilizar arquivos para download, é aconselhável distribuir também um resumo de mensagens correspondente para que as pessoas que baixarem aquele arquivo possam produzir um novo resumo de mensagens e comparar com o original.


### Considere que temos um arquivo de um livro, contendo apenas texto. Utilizando o "split" Como poderíamos separar em vários arquivos de 100 linhas e definir um prefixo para os nomes dos arquivos? 

```split -l 100 -d livro.txt parte```

Explicação:

```
split: separa arquivos em pedaços
-l 100: (lines) coloca um número no nome do arquivo a cada saída de 100 linhas;
-d :(utiliza sufixos numéricos)
livro.txt: nome do arquivo
parte: prefixo que trará o resultado "parte01, parte02..."
```

### O que faz a função squeeze do tr? ex: cat file.txt | tr -s ' '?

```
Para o tr, existe uma opção ```-s``` (squeeze) que reduz as repetições consecutivas, ex:"isso   tem     muitos   espaços", com o ```tr -s ' ' ```, seria: "isso tem muitos espaços".
```

### [Prática] Como pode listar apenas o nome do proprietário e dos diretórios do /etc?

```
 ls -l /etc | grep ^d | tr -s ' ' | cut -d" " -f9,3
```
Explicação:
- `ls -l /etc`  
  Lista o conteúdo do diretório `/etc` em formato longo (permissões, dono, grupo, tamanho, data, nome).

- `grep ^d`  
  Filtra apenas as linhas que começam com `d`, ou seja, **somente diretórios**.

- `tr -s ' '`  
  Substitui múltiplos espaços consecutivos por um único espaço, facilitando o corte por campos.

- `cut -d" " -f9,3`  
  Usa o espaço como delimitador e exibe:
  - campo 9 → nome do diretório
  - campo 3 → grupo proprietário

### Alguém plugou um pendrive no servidor linux, acompanhe a saída em tempo real do arquivo /var/log/syslog e obtenha o produto (product) o fabricante (manufacturer) e a quantidade total de memória (Blocks) do seu pendrive.

```
$ tail -f /var/log/syslog | grep -i 'product\:\|blocks\|manufacturer'

Nov 8 06:01:35 brod-avell kernel: [124954.369361] usb 1-4.3: Product: Cruzer Blade
Nov 8 06:01:35 brod-avell kernel: [124954.369364] usb 1-4.3: Manufacturer: SanDisk
Nov 8 06:01:37 brod-avell kernel: [124955.419267] sd 2:0:0:0: [sdc] 61056064 512-byte
logical blocks: (31.3 GB/29.1 GiB)
```
Explicação:
```
tail -f :(follow) acompanha os logs em tempo real
grep -i : (ignore case) Não se sabe se é maiúscula ou minúscula.
\|  : É o operador lógico "OU/OR"
```

# 103.3 Gerenciamento básico de arquivos (Página 230)

### Podemos dizer que no linux tudo é arquivo?
- sim, mas o material não diz que são arquivos de texto.

### De que é composto um arquivo?
- Normalmente de conteúdo e metadados (tamanho do arquivo, proprietário, data de criação, permissões). Os arquivos são organizados em diretórios. Um diretório é um arquivo que armazena outros arquivos.

### Utilizando o ls como podemos incluir a listagem longa, de arquivos ocultos e legível por humanos?
- ls -lha

### Para que serve o comando touch? como utilizar?
- Para criar arquivos: touch file1 file2 file3 (Cria 3 arquivos)
- Para mudar os carimbos de tempo (timestamp):  touch -am file3 (altera a hora de acesso e hora de modificação para a atual)

Explicação
```
-a : access time
-m : modification time
```

### Como copiar o arquivo file2 de "/home/frank/Documents/" para "/home/frank/Documents/Backup"? e como mover?
- Copiar: cp /home/frank/Documents/file2 /home/frank/Documents/Backup
- Mover: mv /home/frank/Documents/file2 /home/frank/Documents/Backup

### Como diferencia um caminho absoluto de um caminho relativo?
- Os caminhos absolutos iniciam com /, caso contrário, é relativo.

### Como posso renomear um arquivo utilizando o mv e o que faz o -i e o -f?

- $  mv old_file_name new_file_name

Utilizando o -f o arquivo é sobrescrito forçadamente, já o -i ele pede a interação do cliente.

### Como podemos remover um arquivo solicitando a confirmação ao usuário antes de remover?

- rm -i file.txt

### Como criar um diretório junto aos seus subdiretórios(parents)? E como deletar?
- mkdir -p nome_diretorio/nome_subdiretorio

Deletando:
- rmdir -p nome_diretorio/nome_subdiretorio

Se o diretório não estiver vazio, não será removido.

## Manipulação recursiva de arquivos e diretórios

### O que vem a ser uma manipulação recursiva?
- Manipulação de diretórios ou conteúdos que se propagam por toda a árvore de diretórios.

### Ao fazer uma cópia recursiva de um arquivo, qual a diferença de utilizar -r ou -R ou --recursive?
- Nenhuma

### O que acontece se tentarmos excluir um diretório sem usar -r?

```
$ rm newcopy/
rm: cannot remove 'newcopy/': Is a directory
$ rm -r newcopy/
```
Precisa do -r


### Com o intuito de apagar os diretórios, qual a diferença dos comandos rmdir e rm -r?
- o rmdir serve apenas para diretórios

## Globbing de arquivos e caracteres curinga (pag 242)

### O que seria o globbig de arquivos? cite exemplos
- O globbing de arquivos é um recurso fornecido pelo shell do Unix/Linux para representar múltiplos nomes de arquivo usando caracteres especiais chamados caracteres curinga.

- ``` * ```  Remove todos os arquivos do diretório atual.
- ``` ? ```  representa uma única ocorrência de qualquer caractere
- ```[ ]```  Remover todos os diretórios cujo o nome começa com uma letra.

Exemplo:

``` rm * ```   >> Remove todos os arquivos no diretório de trabalho atual.  
``` ls l?st ```  >> Lista todos os arquivos cujo nome começa com l, seguido por qualquer caractere único e
terminando com st.  
``` rmdir [a-z]* ```  >> Remove todos os diretórios cujo nome começa com uma letra.

### Sobre globbing de arquivos, o que significa: ``` ls [plf]?st* ```?
- Lista qualquer caractere que inicie com p,l,f + um outro caractere + "st" e finalize com quaisquer outros caracteres.

# 103.3 Gerenciamento básico de arquivos PARTE 2

### Como podemos utilizar o find para buscar arquivos de extensão PDF no diretorio atual?
- find . -name "*.pdf"

### Para acelerar uma busca de arquivo no find, podemos definir um tipo a ser buscado, quais os parametros são utilizados para buscar por arquivos, diretórios e links simbólicos?

```
-type f: busca por arquivos.
-type d: busca por diretórios.
-type l: busca por links simbólicos.
```

Ex:  ``` find . -type d -name "example" ```
- Este comando procura por todos os diretórios, no diretório atual e abaixo dele, que tenham o
nome example


### Utilizando o find para buscar aquivos, cite alguns dos outros paramentros que podemos utilizar.

```
-name: pesquisa com base no nome fornecido.
-iname: pesquisa com base no nome, desconsiderando maiúsculas e minúsculas (ou seja, o caso de teste
myFile é semelhante a MYFILE).
-not: retorna os resultados que não correspondem ao caso de teste.
-maxdepth N: pesquisa no diretório atual, além dos subdiretórios até N níveis de profundidade.
```

### Utilizando o find, como podemos fazer uma busca na raiz de um arquivo .conf baseado em quando o arquivo foi modificado, ex: nos últimos 7 dias?

```
$ sudo find / -name "*.conf" -mtime 7
/etc/logrotate.conf
```

### Utilizando o find, como podemos localizar arquivos maiores de 2GB?
```
$ sudo find /var -size +2G
/var/lib/libvirt/images/debian10.qcow2
/var/lib/libvirt/images/rhel8.qcow2
```
outras opções:
```
-size 100c  -> arquivos com exatamente 100 bytes.
-size +100k  -> arquivos maiores que 100 kilobytes.
-size -20M  -> arquivos menores que 20 megabytes.
-size +2G -> arquivos maiores que 2 gigabytes.
Para encontrar arquivos vazios, podemos usar: find . -size 0c ou find . -empty.
```

### Quando utilizamos o find, é possível executar uma ação ao resultado, como podemos fazer isso?
- Com o -exec
```
$ find . -name "*.conf" -exec chmod 644 '{}' \;
```

# Parei na pagina 258