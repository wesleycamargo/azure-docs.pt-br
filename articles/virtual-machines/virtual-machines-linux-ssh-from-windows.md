<properties 
	pageTitle="Usar SSH no WIndows para conectar-se a máquinas virtuais Linux | Microsoft Azure" 
description="Saiba como gerar e usar chaves SSH em um computador Windows para se conectar a uma máquina virtual Linux no Azure." 
	services="virtual-machines-linux" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2016" 
	ms.author="rasquill"/>

#Como usar SSH com Windows no Azure

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux/Mac](virtual-machines-linux-ssh-from-linux.md)

Este tópico descreve como criar e usar arquivos de chave pública e privada com formato **ssh-rsa** e **.pem** no Windows, que você pode usar para se conectar a suas VMs Linux no Azure com o comando **ssh**. Se já tiver arquivos **.pem** criados, você pode usá-los para criar VMs Linux às quais pode se conectar usando **ssh**. Vários outros comandos usam o protocolo **SSH** e arquivos de chave para realizar o trabalho com segurança, especialmente **scp** ou [Secure Copy](https://en.wikipedia.org/wiki/Secure_copy), que podem copiar com segurança os arquivos entre computadores que dão suporte a conexões **SSH**.


## De quais programas de criação de chave e SSH você precisa?

O **SSH** &#8212, ou [secure shell](https://en.wikipedia.org/wiki/Secure_Shell) &#8212, é um protocolo de conexão criptografada que permite logons seguros através de conexões não seguras. É o protocolo de conexão padrão para VMs Linux hospedadas no Azure, a menos que você configure suas VMs Linux para habilitar algum outro mecanismo de conexão. Usuários do Windows também podem conectar e gerenciar máquinas virtuais Linux no Azure usando uma implementação de cliente **ssh**, mas os computadores Windows normalmente não vêm com um cliente **ssh**, portanto, será necessário escolher um.

Clientes comuns que você pode instalar incluem:

- [puTTY e puTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)
- [Git para Windows](https://git-for-windows.github.io/), que é fornecido com o ambiente e as ferramentas

Se estiver se sentindo especialmente nerd, você também pode testar a [nova porta do conjunto de ferramentas **OpenSSH** para Windows](http://blogs.msdn.com/b/powershell/archive/2015/10/19/openssh-for-windows-update.aspx). Lembre-se, no entanto, de que este é um código que está em desenvolvimento e você deve analisar a base de código antes de usá-lo para sistemas de produção.

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Quais arquivos de chave você precisa criar?

Uma configuração SSH básica para o Azure inclui um par de chaves **ssh-rsa** pública e privada de 2048 bits (por padrão, **ssh-keygen** armazena esses arquivos como **~/.ssh/id\_rsa** e **~/.ssh/id-rsa.pub**, a menos que você mude os padrões) e também um arquivo `.pem` gerado com a chave privada **id\_rsa** para uso com o modelo de implantação clássico do portal clássico.

Estes são os cenários de implantação e os tipos de arquivo que você usa em cada um deles:

1. As chaves **ssh-rsa** são necessárias para qualquer implantação que usa o [Portal do Azure](https://portal.azure.com), independentemente do modelo de implantação.
2. O arquivo .pem é necessário para criar VMs usando o [portal clássico](https://manage.windowsazure.com). Arquivos .pem também recebem suporte em implantações clássicas que usam a [CLI do Azure](../xplat-cli-install.md).

> [AZURE.NOTE] Se você planeja gerenciar o serviço implantado com o modelo de implantação clássico, convém criar também um arquivo no formato **.cer** para carregar no portal, embora isso não envolva **ssh** ou a conexão com VMS do Linux, que é o assunto deste artigo. Para criar esses arquivos no Windows, digite: <br /> openssl.exe x509 -outform der -in myCert.pem -out myCert.cer

## Obter ssh-keygen e openssl no Windows ##

[A seção](#What-SSH-and-key-creation-programs-do-you-need) acima lista vários utilitários que incluem um `ssh-keygen` e `openssl` para Windows. Alguns exemplos são listados abaixo:

###Usar Git para Windows###

1.	Baixe e instale o Git para Windows no seguinte local: [https://git-for-windows.github.io/](https://git-for-windows.github.io/)
2.	Execute o Git Bash do Menu Iniciar > Todos os Aplicativos > Git Shell

> [AZURE.NOTE] É possível encontrar o seguinte erro ao executar os comandos `openssl` acima:

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

## Criar uma chave privada##

1.	Siga um dos conjuntos de instruções acima para poder executar o `openssl.exe`.
2.	Digite o seguinte comando:

  ```
  openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem
  ```
3.	Sua tela deverá parecer com o seguinte:

  ```
  $ openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem
  Generating a 2048 bit RSA private key
  .......................................+++
  .......................+++
  writing new private key to 'myPrivateKey.key'
  -----
  You are about to be asked to enter information that will be incorporated
  into your certificate request.
  What you are about to enter is what is called a Distinguished Name or a DN.
  There are quite a few fields but you can leave some blank
  For some fields there will be a default value,
  If you enter '.', the field will be left blank.
  -----
  Country Name (2 letter code) [AU]:
  ```

4.	Responda às perguntas feitas.
5.	Ele criaria dois arquivos: `myPrivateKey.key` e `myCert.pem`.
6.	Se você pretende usar a API diretamente e não usar o Portal de Gerenciamento, converta `myCert.pem` em `myCert.cer` (certificado X509 codificado em DER) usando o seguinte comando:

  ```
  openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
  ```

## Criar um PPK para Putty ##

1. Baixe e instale o Puttygen do seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Talvez o Puttygen não seja capaz de ler a chave privada criada anteriormente (`myPrivateKey.key`). Execute o comando a seguir para convertê-la em uma chave privada RSA legível para o Puttygen:

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	O comando acima deve produzir uma nova chave privada chamada myPrivateKey\_rsa.

3. Execute o `puttygen.exe`

4. Clique no menu: Arquivo > Carregar uma chave privada

5. Encontre a chave privada, que nomeamos de `myPrivateKey_rsa` acima. Você precisará alterar o filtro de arquivo para mostrar **Todos os arquivos (*.*)**

6. Clique em **Abrir**. Você receberá um prompt que deverá ter esta aparência:

	![linuxgoodforeignkey](./media/virtual-machines-linux-ssh-from-linux/linuxgoodforeignkey.png)

7. Clique em **OK**

8. Clique em **Salvar Chave Privada**, que é destacado na captura de tela abaixo:

	![linuxputtyprivatekey](./media/virtual-machines-linux-ssh-from-linux/linuxputtygenprivatekey.png)

9. Salve o arquivo como um PPK


## Use Putty para se conectar a um computador Linux ##

1.	Baixe e instale o putty do seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Execute putty.exe
3.	Preencha o nome do host usando o IP do Portal de Gerenciamento:

	![linuxputtyconfig](./media/virtual-machines-linux-ssh-from-linux/linuxputtyconfig.png)

4.	Antes de selecionar **Abrir**, clique na guia Conexão > SSH > Autenticação para escolher a chave. Consulte a captura de tela abaixo para ver o campo a ser preenchido:

	![linuxputtyprivatekey](./media/virtual-machines-linux-ssh-from-linux/linuxputtyprivatekey.png)

5.	Clique em **Abrir** para se conectar a sua máquina virtual
 

<!---HONumber=AcomDC_0629_2016-->