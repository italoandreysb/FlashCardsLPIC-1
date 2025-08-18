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