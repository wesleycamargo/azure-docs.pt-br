<properties linkid="article" urlDisplayName="Usar SSH" pageTitle="Use o SSH para se conectar a máquinas virtuais Linux no Azure" metaKeywords="Chaves SSH Azure Linux, Linux VM SSH" description="Aprenda a gerar e usar chaves SSH com uma máquina virtual Linux no Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Como usar SSH com Linux no Azure" authors="" solutions="" manager="" editor="" />





#Como usar SSH com o Linux no Azure

Este tópico descreve as etapas para gerar chaves SSH compatíveis com o Azure.

A versão atual do Portal de Gerenciamento do Azure aceita apenas chaves públicas SSH encapsuladas em um certificado X509. Siga as etapas abaixo para gerar e usar chaves SSH com o Azure.

## Gerar chaves compatíveis do Windows Azure no Linux ##

1. Instale o utilitário `openssl`, se necessário:

	**CentOS/Oracle Linux**

		`sudo yum install openssl`

	**Ubuntu**

		`sudo apt-get install openssl`

	**SLES e openSUSE**

		`sudo zypper install openssl`


2. Use `openssl` para gerar um certificado X509 com um par de chaves RSA de 2.048 bits. Responda a algumas perguntas feitas pelo `openssl` (ou você pode deixá-las em branco). O conteúdo desses campos não é usado pela plataforma:

			openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Altere as permissões na chave privada para protegê-la.

			chmod 600 myPrivateKey.key
4.	Carregue `myCert.pem` ao criar a máquina virtual Linux. O processo de provisionamento instalará automaticamente a chave pública nesse certificado no arquivo `authorized_keys` para o usuário especificado na máquina virtual.

5.	Se você for usar a API diretamente, e não usar o Portal de Gerenciamento, converta `myCert.pem` em `myCert.cer` (certificado X509 codificado em DER) usando o seguinte comando:

			openssl  x509 -outform der -in myCert.pem -out myCert.cer


## Gerar uma chave com base em uma chave compatível com OpenSSH existente
O exemplo anterior descreve como criar uma nova chave a ser usada com o Windows Azure. Em alguns casos, os usuários talvez já tenham um par de chaves OpenSSH pública e privada compatíveis existente e queira usar as mesmas chaves com o Windows Azure.

As chaves privadas OpenSSH são diretamente legíveis pelo utilitário `openssl`. O seguinte comando utilizará uma chave privada SSH existente (id_rsa no exemplo abaixo) e criará a chave pública `.pem` necessária para o Windows Azure:

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

O arquivo **myCert.pem** é a chave pública que pode ser usada para provisionar uma máquina virtual Linux no Windows Azure. Durante o provisionamento, o arquivo `.pem` será convertido em uma chave pública `openssh` compatível e colocado em `~/.ssh/authorized_keys`.


## Conectar-se a uma máquina virtual do Windows Azure no Linux
Cada máquina virtual Linux é provisionada com o SSH em uma porta específica que pode ser diferente da porta padrão usada. 

1.	Localize a porta que você usará para se conectar à máquina virtual Linux pelo Portal de Gerenciamento.
2.	Conecte a máquina virtual Linux usando `ssh`. Você será solicitado a aceitar a impressão digital da chave pública do host na primeira vez que você efetuar o login.

		ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net
3.	(Opcional) Você pode copiar `myPrivateKey.key` para `~/.ssh/id_rsa` de forma que o cliente openssh possa selecioná-lo automaticamente sem o uso da opção `-i`.

## Obter o OpenSSL no Windows ##
### Usar msysgit ###

1.	Baixe e instale o msysgit do seguinte local: [http://msysgit.github.com/](http://msysgit.github.com/)
2.	Execute `msys` no diretório instalado (exemplo: c:\msysgit\msys.exe)
3.	Altere para o diretório `bin` digitando `cd bin`

###Usar GitHub para Windows###

1.	Baixe e instale o GitHub para Windows do seguinte local: [http://windows.github.com/](http://windows.github.com/)
2.	Execute o Git Shell pelo Menu Iniciar > Todos os Programas > GitHub, Inc

###Usar cygwin###

1.	Baixe e instale o Cygwin do seguinte local: [http://cygwin.com/](http://cygwin.com/)
2.	Certifique-se de que o pacote OpenSSL e todas as suas dependências estão instaladas.
3.	Executar `cygwin`

## Criar uma chave privada no Windows ##

1.	Seguir um dos conjuntos de instruções acima para executar `openssl.exe`
2.	Digite o seguinte comando:

		openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Sua tela deverá parecer com o seguinte:

	![linuxwelcomegit](./media/linux-use-ssh-key/linuxwelcomegit.png)

4.	Responda às perguntas feitas.
5.	Ele criaria dois arquivos: `myPrivateKey.key` e `myCert.pem`.
6.	Se você for usar a API diretamente, e não usar o Portal de Gerenciamento, converta `myCert.pem` em `myCert.cer` (certificado X509 codificado em DER) usando o seguinte comando:

		openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Criar um PPK para Putty ##

1.	Baixe e instale o puttygen do seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Executar `puttygen.exe`
3.	Clique no menu: Arquivo > Carregar uma chave privada
4.	Localize a chave privada, que chamamos de `myPrivateKey.key`. Você precisará alterar o filtro de arquivo para mostrar **Todos os arquivos (\*.*)**
5.	Clique em **Abrir**. Você receberá um prompt que deverá ter esta aparência:

	![linuxgoodforeignkey](./media/linux-use-ssh-key/linuxgoodforeignkey.png)

6.	Clique em **OK**.
7.	Clique em **Salvar Chave Privada**, que é destacado na captura de tela abaixo:

	![linuxputtyprivatekey](./media/linux-use-ssh-key/linuxputtygenprivatekey.png)

8.	Salve o arquivo como um PPK.

## Use Putty para se conectar a um computador Linux ##

1.	Baixe e instale o putty do seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Execute putty.exe
3.	Preencha o nome do host usando o IP do Portal de Gerenciamento.

	![linuxputtyconfig](./media/linux-use-ssh-key/linuxputtyconfig.png)

4.	Antes de selecionar **Abrir**, clique na guia Conexão > SSH > Autenticação para escolher a chave. Consulte a captura de tela abaixo do campo para preencher.

	![linuxputtyprivatekey](./media/linux-use-ssh-key/linuxputtyprivatekey.png)

5.	Clique em **Abrir** para se conectar a sua máquina virtual.

