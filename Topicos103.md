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
- history | grep <comando>

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

### Podemos criar uma variável de ambiente personalizada? Se sim, como?
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
- Touch 'my big file"
- touch my\ big\ file\

### Ao executar comandos utilizando strings, qual a diferença entre " (áspas duplas) e ' (áspas simples) ?
- O escape com " preserva os valores especiais do cifrão, da crase e da barra invertida. Já o escape com o ' faz com que todos os caracteres sejam interpretados literalmente.

--- 

## 103.2 Processar fluxos de texto usando filtros


### o que significa cat e o que acontece se não designarmos nada para o cat ler, apenas executando "cat"?
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

### Cite um exemplo útil para o uso do comando od.
- Depurar scripts, ele mostra caracteres que não são normalmente visíveis e que existem em um arquivo
```
$ od -c ftu.txt 
```
- Caso não qeuira ver o o deslocamento de bytes (primeira coluna), utilize:  
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
- -d: → usa os dois-pontos (:) como separador de campos (padrão do arquivo /etc/passwd)
- -f3 → pega apenas o terceiro campo de cada linha
```
O primeiro campo de qualquer linha do arquivo /etc/passwd é o nome do usuário, o segundo é tipicamente um x indicando que a senha do usuário não está armazenada aqui (ela é criptografada no arquivo /etc/shadow). O terceiro é o id do usuário (UID) e o quarto é o id do grupo (GID). Portanto, isso deve nos fornecer o número de usuários.
```

### Liste apenas a primeira, a última e a décima linha do arquivo /etc/passwd.
- ```$ sed -n -e '1'p -e '10'p -e '$'p /etc/passwd```
```
Explicação:
sed  : Editor de fluxo (stream editor) muito usado para filtrar e transformar texto
-n  : Silencia a saída automática. Sem o -n, o sed imprimiria todas as linhas
-e : Permite especificar múltiplos comandos (cada -e é uma instrução separada)
'1'p : Quando chegar na linha 1, execute o comando p → imprima essa linha
'10'p : Quando chegar na linha 10, imprima essa linha
'$'p : Quando chegar na última linha do arquivo ($ significa última linha), imprima
/etc/passwd: Arquivo de entrada
```

### Liste todos os usuários do arquivo /etc/passwd no grupo 1000 (use sed para selecionar apenas o campo apropriado) do arquivo mypasswd. sabendo que após o GID o campo seguinte começa com uma caractere maiúsculo. ex: ```carol:x:1000:2000:Carol Smith,Finance,,,Main Office:/home/carol:/bin/bash```

- ```sed -n /:1000:[A-Z]/p /etc/passwd```


---
# Parei no final da página 222