<properties
	pageTitle="Configurar Apache Tomcat em uma VM do Linux | Microsoft Azure"
	description="Saiba como configurar o Apache Tomcat7 usando uma máquina virtual (VM) do Azure que executa o Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/21/2015"
	ms.author="ningk"/>

#Como configurar o Tomcat7 em uma máquina virtual Linux com o Microsoft Azure

Apache Tomcat (ou simplesmente Tomcat, anteriormente também Tomcat Jacarta) é um servidor Web de software livre e o contêiner de servlet desenvolvidos pelo Apache Software Foundation (ASF). Tomcat implementa o Servlet Java e especificações de JavaServer Pages (JSP) da Sun Microsystems e fornece um ambiente de servidor Web HTTP Java puro na qual executará o código Java. Na configuração mais simples, o Tomcat é executado em um único processo do sistema operacional. Esse processo é executado em uma máquina virtual Java (JVM). Todas as solicitações HTTP de um navegador para o Tomcat são processadas como um thread separado do processo do Tomcat.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda a criação de um recurso com o modelo clássico de implantação.

Neste guia, você instalará o tomcat7 em uma imagem do Linux e vai implantá-lo no Microsoft Azure.

Você aprenderá:

-	Como criar uma máquina virtual no Azure.
-	Como preparar a máquina virtual para o tomcat7.
-	Como instalar o tomcat7.

Supõe-se que o leitor já tenha uma assinatura do Azure. Caso contrário, você poderá se inscrever em uma avaliação gratuita em [http://azure.microsoft.com](http://azure.microsoft.com). Se você tiver uma assinatura do MSDN, confira [Preço especial do Microsoft Azure: benefícios do MSDN, MPN, e Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Para saber mais sobre o Azure, confira [o que é o Azure?](http://azure.microsoft.com/overview/what-is-azure/).

Este tópico pressupõe que você tenha conhecimento prático básico do tomcat e Linux.

##Fase 1: Criar uma imagem
Nesta fase, você criará a máquina virtual usando uma imagem do Linux no Azure.

###Etapa 1: Gerar uma chave de autenticação SSH
O SSH é uma ferramenta importante para os administradores do sistema. No entanto, configurando a segurança de acesso com base em uma senha determinada por humanos não é uma prática recomendada. Usuários mal-intencionados podem entrar em seu sistema com base em um nome de usuário e uma senha fraca.

A boa notícia é que há uma maneira de deixar o acesso remoto aberto e não precisar se preocupar com as senhas. O método consiste na autenticação com criptografia assimétrica. A chave privada do usuário é a que concede a autenticação. Você ainda pode bloquear a conta do usuário para não permitir a autenticação de senha completamente.

Outra vantagem desse método é que você não precisa de senhas diferentes para fazer logon em servidores diferentes. Você pode autenticar usando a chave privada pessoal em todos os servidores, que impede que você precise se lembrar de várias senhas.

Também é possível fazer logon sem exigir uma senha com esse método.

Siga estas etapas para gerar a chave de autenticação SSH.

1.	Baixe e instale o puttygen do seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Execute PUTTYGEN.EXE.
3.	Clique em **Gerar** para gerar as chaves. No processo, você pode aumentar a aleatoriedade movendo o mouse sobre a área em branco na janela. ![][1]
4.	Após o processo de geração, Puttygen.exe mostrará a chave gerada. Por exemplo: ![][2]
5.	Selecione e copie a chave pública em **Chave** e salve-a em um arquivo chamado publicKey.pem. Não clique em **Salvar chave pública**, porque o formato de arquivo da chave pública salva é diferente da chave pública que queremos.
6.	Clique em **Salvar chave privada** e salve-a em um arquivo chamado privateKey.ppk.

###Etapa 2: Criar a imagem no Portal de Visualização do Azure.
No [Portal de visualização do Azure](https://portal.azure.com/), clique em **Novo** na barra de tarefas para criar uma imagem, escolha a imagem do Linux com base em suas necessidades. O exemplo a seguir usa a imagem do Ubuntu 14.04. ![][3]

Para o **Nome do Host**, especifique o nome para a URL que você e os clientes da Internet usarão para acessar esta máquina virtual. Defina a última parte do nome DNS, por exemplo tomcatdemo, e o Azure gerará a URL como tomcatdemo.cloudapp.net.

Para a **Chave de autenticação SSH**, copie o valor-chave do arquivo **publicKey.pem**, o qual contém a chave pública gerada pelo puttygen. ![][4]

Configure as outras configurações conforme necessário e, em seguida, clique em Criar.

##Fase 2: Preparar sua máquina virtual para o Tomcat7
Nesta fase, você configurará um ponto de extremidade para o tráfego do tomcat e, em seguida, se conectará à nova máquina virtual.
###Etapa 1: Abrir a porta HTTP para permitir o acesso via Web
Os pontos de extremidade no Azure são compostos por um protocolo (TCP ou UDP) juntamente com uma porta pública e privada. A porta privada é a porta que o serviço está escutando na máquina virtual. A porta pública é a porta que o serviço de nuvem do Azure está escutando externamente para tráfego de entrada baseado na Internet.

A Porta 8080 TCP é o número da porta padrão na qual o tomcat escuta. Abrindo essa porta com um ponto de extremidade do Azure permitirá que você e outros clientes de Internet acessem páginas do tomcat.

1.	No Portal de Visualização do Azure, clique em **Procurar** -> **Máquina Virtual** e clique na máquina virtual que você criou. ![][5]
2.	Para adicionar um ponto de extremidade à máquina virtual, clique na caixa **Pontos de extremidade**. ![][6]
3.	Clique em **Adicionar**.  
	1.	Para o **ponto de extremidade**, digite um nome para o ponto de extremidade no Ponto de extremidade e, em seguida, digite 80 na **Porta pública**.  

		Se você defini-la para 80, não é necessário incluir o número da porta na URL que permite que você acesse o tomcat. Por exemplo: http://tomcatdemo.cloudapp.net.

		Se você defini-la para outro valor, como 81, você precisa adicionar o número da porta para a URL para acessar o tomcat. Por exemplo: http://tomcatdemo.cloudapp.net:81/.
	2.	Digite 8080 na Porta Privada. Por padrão, o tomcat escuta na porta 8080 TCP. Se você alterou a porta de escuta padrão do tomcat, você deve atualizar a Porta Privada para ser a mesma que a porta de escuta do tomcat. ![][7]

4.	Clique em **OK** para adicionar o ponto de extremidade à máquina virtual.



###Etapa 2: Conectar-se à imagem criada.
Você pode escolher qualquer ferramenta SSH para se conectar à sua máquina virtual. Neste exemplo, usamos Putty.

Primeiro, obtenha o nome DNS da máquina virtual no Portal de Visualização do Azure. **Clique em Procurar** -> **Máquinas virtuais** -> o nome de sua máquina virtual -> **Propriedades** e, em seguida, verifique o campo **Nome do domínio** do bloco **Propriedades**.

Obtenha o número da porta para conexões SSH no campo **SSH**. Veja um exemplo. ![][8]

Baixe o Putty [aqui](http://www.putty.org/) .

Após o download, clique no arquivo executável PUTTY.EXE. Configure as opções básicas com o nome do host e o número da porta obtido nas propriedades da sua máquina virtual. Veja um exemplo: ![][9]

No painel esquerdo, clique em **Conexão** -> **SSH** -> **Autenticação** e, em seguida, clique em **Procurar** para especificar o local do arquivo **privateKey.ppk** que contém a chave privada gerada pelo puttygen na Fase 1: Criar uma imagem. Veja um exemplo: ![][10]

Clique em **Abrir**. Você pode ser alertado por uma caixa de mensagem. Se você tiver configurado o nome DNS e o número da porta corretamente, clique em **Sim**. ![][11]


Você deve ver o seguinte. ![][12]

Insira o nome de usuário especificado quando você criou a máquina virtual na Fase 1: Criar uma imagem Você verá algo semelhante ao que se segue: ![][13]





##Fase 3: Instalar o software
Nesta fase, você pode instalar o Java runtime environment, o tomcat e outros componentes do tomcat.

###Java runtime environment
O tomcat é escrito em Java. Há dois tipos de Kits de desenvolvimento Java (JDKs) (OpenJDK e JDK do Oracle), e você pode escolher o que você deseja.

>OBSERVAÇÃO.DO.AZURE: Ambos os JDKs têm quase o mesmo código para as classes na API do Java, mas o código para a máquina virtual é, na verdade, diferente. Quando se trata de bibliotecas, o OpenJDK tende a usar bibliotecas abertas enquanto Oracle tende a usar aquelas fechadas. Mas o JDK do Oracle tem mais classes e alguns bugs corrigidos e o JDK do Oracle é mais estável que o OpenJDK.

Os seguintes comandos baixam os diferentes JDKs.

open-jdk

	sudo apt-get update  
	sudo apt-get install openjdk-7-jre  

oracle-jdk

-	Para baixar o JDK do site do Oracle:  

		wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-	Para criar um diretório para conter os arquivos JDK:

		sudo mkdir /usr/lib/jvm  

-	Para extrair os arquivos JDK para diretório /usr/lib/jvm/:

		sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-	Para definir o JDK do Oracle como a JVM padrão:

		sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
		sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####Testar:
Você pode usar um comando semelhante ao seguinte para testar se o Java runtime environment está instalado corretamente:

	java -version  

Se você instalou o open-jdk, você verá uma mensagem semelhante à seguinte: ![][14]

Se você instalou o oracle-jdk, você verá uma mensagem semelhante à seguinte: ![][15]

###Tomcat7
Usando o seguinte comando para instalar o tomcat7:

	sudo apt-get install tomcat7  

Se você não estiver usando o tomcat7, use a variação apropriada desse comando.

####Testar:

Para verificar se o tomcat7 foi instalado com êxito, vá para o nome DNS do seu servidor tomcat (http://tomcatexample.cloudapp.net/ é a URL de exemplo neste artigo). Se você vê uma página semelhante à seguinte, o tomcat7 estará instalado corretamente. ![][16]


###Instalar outros componentes do Tomcat
Há outros componentes do tomcat opcionais que você também pode instalar.

Use o comando **sudo apt-cache search tomcat7** para ver todos os componentes disponíveis. Os comandos a seguir são exemplos para instalar algumas partes úteis.

	sudo apt-get install tomcat7-admin      #admin web applications
	sudo apt-get install tomcat7-user         #tools to create user instances  

##Fase 4: Configurar o Tomcat
Nesta fase, você administra o tomcat.
###Iniciar e parar o tomcat7
O servidor do tomcat7 será iniciado automaticamente quando você o instala. Você também pode iniciá-lo com o seguinte comando:

	sudo /etc/init.d/tomcat7 start

Para parar o tomcat7：

	sudo /etc/init.d/tomcat7 stop

Para exibir o status do tomcat7：

	sudo /etc/init.d/tomcat7 status

Para reiniciar os serviços do tomcat：

	sudo /etc/init.d/tomcat7 restart

###Administração do Tomcat
Você pode editar o arquivo de configuração de usuário do Tomcat para configurar as credenciais de administrador com o seguinte comando:

	sudo vi  /etc/tomcat7/tomcat-users.xml   

Veja um exemplo: ![][17]

>OBSERVAÇÃO.DO.AZURE: Crie uma senha forte para o nome de usuário administrativo.

Depois de editar esse arquivo, você deverá reiniciar serviços do tomcat7 com o comando a seguir para garantir que as alterações entrem em vigor:

	sudo /etc/init.d/tomcat7 restart  

Abra o navegador e digite a URL **http://<your tomcat server DNS name>/manager/html**. Para o exemplo deste artigo, a URL é http://tomcatexample.cloudapp.net/manager/html.

Após conectar, você deverá ver algo semelhante ao seguinte: ![][18]

##Problemas comuns

###Não é possível acessar a máquina virtual com o Tomcat e o Moodle por meio da Internet

-	**Sintoma** O Tomcat está sendo executado, mas você não consegue ver a página padrão do Tomcat com seu navegador.
-	**Possível causa raiz**   
	1.	A porta de escuta do tomcat não é a mesma que a Porta Privada do ponto de extremidade da máquina virtual para o tráfego do tomcat.  

		Verifique as configurações de ponto de extremidade da Porta Pública e da Porta Privada e certifique-se de que a Porta Privada é a mesma que a porta de escuta do tomcat. Consulte a Fase 1: Crie uma imagem para obter instruções sobre como configurar os pontos de extremidade para sua máquina virtual.

		Para determinar a porta de escuta do tomcat, abra /etc/httpd/conf/httpd.conf (versão Red Hat) ou /etc/tomcat7/server.xml (versão Debian). Por padrão, a porta de escuta do tomcat é 8080. Veja um exemplo:

			<Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

		Se você estiver usando uma máquina virtual como a Debian ou Ubuntu e você deseja alterar a porta padrão do Tomcat Listen (por exemplo 8081), você também deve abrir a porta para o sistema operacional. Primeiro, abra o perfil:

			sudo vi /etc/default/tomcat7  

		Remova a marca de comentário da última linha e altere “não” para “sim”.

			AUTHBIND=yes

	2.	O firewall desabilitou a porta de escuta do tomcat.

		Se você só pode ver a página padrão do Tomcat do host local, é mais provável que o problema seja que a porta que é ouvida por tomcat é bloqueada pelo firewall. Você pode usar a ferramenta w3m para procurar a página da Web. Os seguintes comandos instalar w3m e navegue até a página padrão do Tomcat:

			sudo yum  install w3m w3m-img
			w3m http://localhost:8080  

-	**Solução**
	1. Se a porta de escuta do tomcat não é o mesma que a Porta Privada do ponto de extremidade para o tráfego para a máquina virtual, você precisa alterar a Porta Privada para ser que o mesma que a porta de escuta do tomcat.   

	2.	Se o problema for causado pelo firewall/iptables, adicione as seguintes linhas para /etc/sysconfig/iptables:

			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

		Observe que a segunda linha é necessária apenas para tráfego https.

		Certifique-se de que estejam acima de todas as linhas que globalmente restringiriam o acesso, como as seguintes:

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		Para recarregar o iptables, execute o seguinte comando:

			service iptables restart  

		Isso foi testado no CentOS 6.3.

###Permissão negada ao carregar os arquivos do projeto para /var/lib/tomcat7/webapps/  

-	**Sintoma** Quando você usa qualquer cliente SFTP (por exemplo, o FileZilla) para se conectar à sua máquina virtual e navegar até /var/lib/tomcat7/webapps/ para publicar seu site, você recebe uma mensagem de erro semelhante à seguinte:  

		status:	Listing directory /var/lib/tomcat7/webapps
		Command:	put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
		Error:	/var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
		Error:	File transfer failed

-	**Possível causa raiz** Você não tem permissões para acessar a pasta /var/lib/tomcat7/webapps.
-	**Solução** Você precisa obter permissão da conta raiz. Você pode alterar a propriedade da pasta raiz para o nome de usuário usado ao provisionar o computador. Aqui está um exemplo com o nome de conta azureuser:  

		sudo chown azureuser -R /var/lib/tomcat7/webapps

	Use a opção -R para aplicar as permissões para todos os arquivos dentro de um diretório também.

	Observe que este comando também funciona para diretórios. A opção -R altera as permissões para todos os arquivos e diretórios dentro do diretório. Veja um exemplo:

		sudo chown -R username:group directory  

	Este comando altera a propriedade (tanto de usuário como de grupo) para todos os arquivos e diretórios dentro do diretório e para o próprio diretório.

	O comando a seguir só altera a permissão do diretório da pasta, mas deixa os arquivos e pastas sozinhos dentro do diretório.

		sudo chown username:group directory



[1]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-18.png

<!---HONumber=Sept15_HO4-->