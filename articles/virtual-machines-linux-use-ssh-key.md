<properties 
	pageTitle="Usar SSH para conectar-se a máquinas virtuais Linux no Azure" 
	description="Saiba como gerar e usar chaves SSH com uma máquina virtual Linux no Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="szark"/>

#Como usar SSH com Linux no Azure

A versão atual do Portal de Gerenciamento do Azure aceita apenas chaves públicas SSH encapsuladas em um certificado X509. Siga as etapas abaixo para gerar e usar chaves SSH com o Azure.

## Gerar chaves compatíveis do Azure no Linux ##

1. Instale o utilitário `openssl`, se necessário:

	**CentOS & Oracle Linux**

		# sudo yum install openssl

	**Ubuntu**

		# sudo apt-get install openssl

	**SLES e openSUSE**

		# sudo zypper install openssl


2. Use o `openssl` para gerar um certificado X509 com um par de chaves RSA de 2048 bits. Responda a algumas perguntas feitas pelo `openssl` (ou você pode deixá-las em branco). O conteúdo desses campos não é usado pela plataforma:

		# openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Altere as permissões na chave privada para protegê-la.

		# chmod 600 myPrivateKey.key

4.	Carregue `myCert.pem` ao criar a máquina virtual Linux. O processo de provisionamento instalará automaticamente a chave pública nesse certificado no arquivo `authorized_keys` para o usuário especificado na máquina virtual.

5.	Se você pretende usar a API diretamente e não usar o Portal de Gerenciamento, converta `myCert.pem` em `myCert.cer` (certificado X509 codificado em DER) usando o seguinte comando:

		# openssl  x509 -outform der -in myCert.pem -out myCert.cer


## Gerar uma chave com base em uma chave compatível com OpenSSH existente
O exemplo anterior descreve como criar uma nova chave para ser usada com o Azure. Em alguns casos, talvez você já tenha um par de chaves OpenSSH públicas e privadas compatíveis existentes e queira usar as mesmas chaves com o Azure.

As chaves privadas OpenSSH são diretamente legíveis pelo utilitário `openssl`. O seguinte comando usará uma chave privada SSH existente (id_rsa no exemplo abaixo) e criará a chave pública `.pem` necessária para o Azure:

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

O arquivo **myCert.pem** é a chave pública que pode ser usada para provisionar uma máquina virtual Linux no Azure. Durante o provisionamento, o arquivo `.pem` será convertido em uma chave pública compatível de `openssh` e colocada em `~/.ssh/authorized_keys`.


## Conectar-se a uma máquina Virtual do Azure a partir do Linux

1. Em alguns casos, o ponto de extremidade SSH para uma máquina virtual Linux pode ser configurado para uma porta que não a porta 22 padrão. Você pode encontrar o número da porta correto no Painel para a máquina virtual no Portal de gerenciamento (em “Detalhes de SSH").

2.	Conecte-se à máquina virtual Linux usando o `ssh`. Você será solicitado a aceitar a impressão digital da chave pública do host na primeira vez que você fizer o logon.

		# ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.	Opcional: você pode copiar `myPrivateKey.key` para `~/.ssh/id_rsa` de modo que o cliente OpenSSH possa selecionar isso automaticamente sem usar a opção `-i`.

## Obter o OpenSSL no Windows ##

Há vários utilitários que incluem um `openssl` para Windows. Alguns exemplos seguem listados abaixo -

### Usar msysgit ###

1.	Baixe e instale o msysgit do seguinte local: [http://msysgit.github.com/](http://msysgit.github.com/)
2.	Execute o `msys` no diretório instalado (exemplo: c:\msysgit\msys.exe)
3.	Mude para o diretório `bin` digitando `cd bin`


### Usar GitHub para Windows ###

1.	Baixe e instale o GitHub para Windows do seguinte local: [http://windows.github.com/](http://windows.github.com/)
2.	Execute o Git Shell pelo Menu Iniciar > Todos os Programas > GitHub, Inc

	**Observação:** É possível encontrar o seguinte erro ao executar os comandos `openssl` acima:

		Unable to load config info from /usr/local/ssl/openssl.cnf

	A maneira mais fácil de resolver esse problema é definir a variável de ambiente `OPENSSL_CONF`. O processo de configuração dessa variável irá variar dependendo do shell que você configurou no Github:

	**Powershell:**

		$Env:OPENSSL_CONF="$Env:GITHUB_GIT\ssl\openssl.cnf"

	**CMD:**

		set OPENSSL_CONF=%GITHUB_GIT%\ssl\openssl.cnf

	**Git Bash:**

		export OPENSSL_CONF=$GITHUB_GIT/ssl/openssl.cnf


###Usar Cygwin###

1.	Baixe e instale o Cygwin do seguinte local: [http://cygwin.com/](http://cygwin.com/)
2.	Certifique-se de que o pacote OpenSSL e todas as suas dependências estão instaladas.
3.	Execute o `cygwin`


## Criar uma chave privada no Windows ##

1.	Siga um dos conjuntos de instruções acima para poder executar o `openssl.exe`.
2.	Digite o seguinte comando:

		# openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Sua tela deverá parecer com o seguinte:

	![linuxwelcomegit](./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png)

4.	Responda às perguntas feitas.
5.	Ele criaria dois arquivos: `myPrivateKey.key` e `myCert.pem`.
6.	Se você pretende usar a API diretamente e não usar o Portal de Gerenciamento, converta `myCert.pem` em `myCert.cer` (certificado X509 codificado em DER) usando o seguinte comando:

		# openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Criar um PPK para Putty ##

1. Baixe e instale o Puttygen do seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Talvez o Puttygen não seja capaz de ler a chave privada criada anteriormente (`myPrivateKey.key`). Execute o comando a seguir para convertê-la em uma chave privada RSA legível para o Puttygen:

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	O comando acima deve produzir uma nova chave privada chamada myPrivateKey_rsa.

3. Execute o `puttygen.exe`

4. Clique no menu: Arquivo > Carregar uma chave privada

5. Encontre a chave privada, que nomeamos de `myPrivateKey_rsa` acima. Você precisará alterar o filtro de arquivo para mostrar **Todos os arquivos (*.*)**

6. Clique em **Abrir**. Você receberá um prompt que deverá ter esta aparência:

	![linuxgoodforeignkey](./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png)

7. Clique em **OK**

8. Clique em **Salvar Chave Privada**, que é destacado na captura de tela abaixo:

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png)

9. Salve o arquivo como um PPK


## Use Putty para se conectar a um computador Linux ##

1.	Baixe e instale o putty do seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Execute putty.exe
3.	Preencha o nome do host usando o IP do Portal de Gerenciamento:

	![linuxputtyconfig](./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png)

4.	Antes de selecionar **Abrir**, clique na guia Conexão > SSH > Autenticação para escolher a chave. Consulte a captura de tela abaixo para ver o campo a ser preenchido:

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png)

5.	Clique em **Abrir** para se conectar a sua máquina virtual

<!---HONumber=58-->