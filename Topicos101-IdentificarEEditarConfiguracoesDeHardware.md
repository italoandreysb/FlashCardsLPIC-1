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
Mostra os dispositivos USBs conectados.

Qual a função do módulo do Kernel?
Controlar o dispositivo existente. O módulo do Kernel pode fazer parte do kernel ou ser adicionado depois.

- Os modulos de kernel do linux relacionados a hardware também são chamados de drivers.
- Geralmente precisam do sudo

Execute o lspci
```
$ lspci

04:02.0 Network controller: Ralink corp. RT2561/RT61 802.11g PCI
```
Para **mais detalhes**, copie o endereço do dispositivo PCI (Hexadecimais no início) e adicione as flags -s -v:

```
$ lspci -s 04:02.0 -v

04:02.0 Network controller: Ralink corp. RT2561/RT61 802.11g PCI
Subsystem: Linksys WMP54G v4.1
Flags: bus master, slow devsel, latency 32, IRQ 21
Memory at e3100000 (32-bit, non-prefetchable) [size=32K]
Capabilities: [40] Power Management version 2
kernel driver in use: rt61pci
```
O módulo do kernel pode ser localizado na linha "kernel driver in use"

Para identificar os drivers do kernel, use o **-k**:

```
lspci -s 01:00.0 -k
```
