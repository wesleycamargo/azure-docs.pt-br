<properties linkid="article" urlDisplayName="Use SSH" pageTitle="Use SSH to connect to Linux virtual machines in Azure" metaKeywords="Azure SSH keys Linux, Linux vm SSH" description="Learn how to generate and use SSH keys with a Linux virtual machine on Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Use SSH with Linux on Azure" authors="" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# Como usar SSH com Linux no Azure

Este tópico descreve as etapas para gerar chaves SSH compatíveis com o Azure.

A versão atual do Portal de Gerenciamento do Azure aceita apenas chaves públicas SSH encapsuladas em um certificado X509. Siga as etapas abaixo para gerar e usar chaves SSH com o Azure.

## Gerar chaves compatíveis do Windows Azure no Linux

1.  Instale o utilitário `openssl`, se necessário:

    **CentOS/Oracle Linux**

        `sudo yum install openssl`

    **Ubuntu**

        `sudo apt-get install openssl`

    **SLES e openSUSE**

        `sudo zypper install openssl`

2.  Use `openssl` para gerar um certificado X509 com um par de chaves RSA de 2048 bits. Responda a algumas perguntas feitas pelo `openssl` (ou você pode deixá-las em branco). O conteúdo desses campos não é usado pela plataforma:

            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  Altere as permissões na chave privada para protegê-la.

            chmod 600 myPrivateKey.key

4.  Carregue o `myCert.pem` ao criar a máquina virtual Linux. O processo de provisionamento instalará automaticamente a chave pública nesse certificado no arquivo `authorized_keys` para o usuário especificado na máquina virtual.

5.  Se você pretende usar a API diretamente e não usar o Portal de Gerenciamento, converta `myCert.pem` em `myCert.cer` (certificado X509 codificado em DER) usando o seguinte comando:

            openssl  x509 -outform der -in myCert.pem -out myCert.cer

## Gerar uma chave com base em uma chave compatível com OpenSSH existente

O exemplo anterior descreve como criar uma nova chave a ser usada com o Windows Azure. Em alguns casos, os usuários talvez já tenham um par de chaves OpenSSH pública e privada compatíveis existente e queira usar as mesmas chaves com o Windows Azure.

As chaves privadas OpenSSH são diretamente legíveis pelo utilitário `openssl`. O seguinte comando usará uma chave privada SSH existente (id\_rsa no exemplo abaixo) e criará a chave pública `.pem` necessária ao Microsoft Azure:

    # openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

O arquivo **myCert.pem** é a chave pública que pode ser usada para provisionar uma máquina virtual Linux no Windows Azure. Durante o provisionamento, o arquivo `.pem` será convertido em uma chave pública `openssh` compatível e colocada em `~/.ssh/authorized_keys`.

## Conectar a uma máquina virtual do Windows Azure do Linux

Cada máquina virtual Linux é provisionada com o SSH em uma porta específica que pode ser diferente da porta padrão usada.

1.  Localize a porta que você usará para se conectar à máquina virtual Linux pelo Portal de Gerenciamento.
2.  Conecte-se à máquina virtual Linux usando o `ssh`. Você será solicitado a aceitar a impressão digital da chave pública do host na primeira vez que você efetuar o login.

        ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.  (Opcional) Você pode copiar `myPrivateKey.key` para `~/.ssh/id_rsa` para que o cliente openssh possa separá-lo automaticamente sem o uso da opção `-i`.

## Obter o OpenSSL no Windows

### Usar msysgit

1.  Baixe e instale msysgit do seguinte local: [][]<http://msysgit.github.com/></a>
2.  Execute `msys` no diretório instalado (exemplo: c:\\msysgit\\msys.exe)
3.  Mude para o diretório `bin` digitando `cd bin`

### Usar GitHub para Windows

1.  Baixe e instale GitHub para Windows no seguinte local: [][1]<http://windows.github.com/></a>
2.  Execute o Git Shell pelo Menu Iniciar \> Todos os Programas \> GitHub, Inc

### Usar cygwin

1.  Baixe e instale Cygwin no seguinte local: [][2]<http://cygwin.com/></a>
2.  Certifique-se de que o pacote OpenSSL e todas as suas dependências estão instaladas.
3.  Execute `cygwin`

## Criar uma chave privada no Windows

1.  Siga um dos conjuntos de instruções acima para executar `openssl.exe`
2.  Digite o seguinte comando:

        openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  Sua tela deverá parecer com o seguinte:

    ![linuxwelcomegit][linuxwelcomegit]

4.  Responda às perguntas feitas.
5.  Ele criaria dois arquivos: `myPrivateKey.key` e `myCert.pem`.
6.  Se você pretende usar a API diretamente e não usar o Portal de Gerenciamento, converta `myCert.pem` em `myCert.cer` (certificado X509 codificado em DER) usando o seguinte comando:

        openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Criar um PPK para Putty

1.  Baixe e instale puttygen no seguinte local: [][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>
2.  Execute `puttygen.exe`
3.  Clique no menu: Arquivo \> Carregar uma Chave Privada
4.  Encontre a chave privada, que chamamos de `myPrivateKey.key`. Você precisará alterar o filtro de arquivo para mostrar **Todos os Arquivos (\*.\*)**
5.  Clique em **Abrir**. Você receberá um prompt que deverá ter esta aparência:

    ![linuxgoodforeignkey][linuxgoodforeignkey]

6.  Clique em **OK**.
7.  Clique em **Salvar Chave Privada**, que é destacado na captura de tela abaixo:

    ![linuxputtyprivatekey][linuxputtyprivatekey]

8.  Salve o arquivo como um PPK.

## Use Putty para se conectar a um computador Linux

1.  Baixe e instale putty no seguinte local: [][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>
2.  Execute putty.exe
3.  Preencha o nome do host usando o IP do Portal de Gerenciamento.

    ![linuxputtyconfig][linuxputtyconfig]

4.  Antes de selecionar **Abrir**, clique na guia Conexão \> SSH \> Autenticação para escolher a chave. Consulte a captura de tela abaixo do campo para preencher.

    ![linuxputtyprivatekey][4]

5.  Clique em **Abrir** para se conectar a sua máquina virtual.

  []: http://msysgit.github.com/
  [1]: http://windows.github.com/
  [2]: http://cygwin.com/
  [linuxwelcomegit]: ./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png
  [3]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [linuxgoodforeignkey]: ./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png
  [linuxputtyprivatekey]: ./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png
  [linuxputtyconfig]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png
  [4]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png
