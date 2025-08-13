# 103.1 Trabalhar na linha de comando

### Como podemos descobrir em que diretório estamos atualmente?
- ``` $ pwd```

### Qual comando pode ser utiliado para descobrir a versão do kernel do linux atualmente carregada?
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