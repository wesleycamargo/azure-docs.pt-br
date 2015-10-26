<properties
	pageTitle="Criar uma pilha LAMP com o Azure | Microsoft Azure"
	description="Saiba como criar uma pilha de LÂMPADA com o Microsoft Azure usando máquinas virtuais do Azure (VMs) que executam o Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/10/2015"
	ms.author="ningk"/>

#Como criar uma pilha LAMP com o Microsoft Azure

Uma pilha “LAMP" é um grupo de software livre que é tipicamente instalado em conjunto para habilitar um servidor a hospedar sites dinâmicos e aplicativos Web. Na verdade, este termo é um acrônimo que representa o sistema operacional Linux com o servidor Web Apache. Os dados do site são armazenados em um banco de dados MySQL e o conteúdo dinâmico é processado pelo PHP.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Neste guia, vamos obter uma pilha LAMP instalada em uma imagem do Linux e implantá-la no Microsoft Azure.

Você aprenderá a:

-	Como criar uma máquina virtual do Azure.
-	Como preparar a máquina virtual para a pilha LAMP.
-	Como instalar o software necessário pelo servidor LAMP na máquina virtual.

Supõe-se que o leitor já tenha uma assinatura do Azure. Caso contrário, você poderá se inscrever em uma avaliação gratuita em [http://azure.microsoft.com](http://azure.microsoft.com). Se você tiver uma assinatura do MSDN, confira [Preço especial do Microsoft Azure: benefícios do MSDN, MPN, e Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Para saber mais sobre o Azure, confira [O que é o Azure?](http://azure.microsoft.com/overview/what-is-azure/)

Além deste tópico, se você já tiver uma máquina virtual e estiver apenas procurando obter as noções básicas de instalação de uma pilha LAMP em diferentes distribuições do Linux, consulte [Instalar a Pilha LAMP em uma máquina virtual do Linux no Azure](virtual-machines-linux-install-lamp-stack.md).

Você também pode implantar imagens da LAMP pré-configuradas do Azure Marketplace. O vídeo de 10 minutos a seguir apresenta a implantação de imagens da LAMP pré-criadas do Azure Marketplace: (pilha LAMP em VMs do Azure](https://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman).

##Fase 1: Criar uma imagem
Nesta fase, você criará a máquina virtual usando uma imagem do Linux no Azure.

###Etapa 1: Gerar uma chave de autenticação SSH
O SSH é uma ferramenta importante para os administradores do sistema. No entanto, contar com uma senha de segurança determinada por humanos nem sempre é aconselhável. Uma chave SSH forte permite que você deixe o acesso remoto aberto sem se preocupar com senhas. O método consiste na autenticação com criptografia assimétrica. A chave privada do usuário é a que concede a autenticação. Você ainda pode bloquear a conta do usuário para não permitir a autenticação de senha completamente.

Siga estas etapas para gerar a chave de autenticação SSH.

-	Baixe e instale o PuTTYGen no seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/](http://www.chiark.greenend.org.uk/~sgtatham/)putty/download.html
-	Execute puttygen.exe.
-	Clique em **Gerar** para gerar as chaves. No processo, você pode aumentar a aleatoriedade movendo o mouse sobre a área em branco na janela. ![][1]
-	Após o processo de geração, Puttygen.exe mostrará a chave gerada. Por exemplo: ![][2]
-	Selecione e copie a chave pública em **Chave** e salve-a em um arquivo chamado **publicKey.pem**. Não clique em **Salvar chave pública**, porque o formato de arquivo da chave pública salva é diferente da chave pública que queremos.
-	Clique em **Salvar chave privada** e salve-a em um arquivo chamado **privateKey.ppk**.

###Etapa 2: Criar a imagem no Portal do Azure.
No [Portal do Azure](https://portal.azure.com/), clique em **Novo** na barra de tarefas e crie uma imagem seguindo estas instruções e escolhendo a imagem do Linux de acordo com as suas necessidades. Este exemplo usa a imagem do Ubuntu 14.04.

![][3]

Para o **Nome do Host**, especifique o nome para a URL que você e os clientes da Internet usarão para acessar esta máquina virtual. Defina a última parte do nome DNS, por exemplo LAMPDemo, e o Azure gerará a URL como Lampdemo.cloudapp.net.

Para o **Nome de Usuário**, selecione um nome que você usará mais tarde para fazer logon na máquina virtual.

Para a **Chave de Autenticação SSH**, copie a chave-valor do arquivo **publicKey.pem**, que contém a chave pública gerada pelo PuTTYGen.

![][4]

Configure as outras configurações, conforme necessário e clique em **Criar**.

##Fase 2: Preparar sua máquina virtual para a pilha LAMP
Nesta fase, você configurará um ponto de extremidade para o tráfego da Web e, em seguida, conectar-se à nova máquina virtual.

###Etapa 1: Abrir a porta HTTP para permitir o acesso via Web
Os pontos de extremidade no Azure são compostos por um protocolo (TCP ou UDP) juntamente com uma porta pública e privada. A porta privada é a porta que o serviço está escutando na máquina virtual. A porta pública é a porta que o serviço de nuvem do Azure está escutando externamente para o tráfego baseado na Internet. Em alguns casos, trata-se do mesmo número da porta.

A porta TCP 80 é o número da porta padrão no qual o Apache escuta. Abrir essa porta com um ponto de extremidade do Azure permitirá que você e outros clientes da Internet acessem o servidor Web Apache.

No Portal do Azure, clique em **Procurar -> Máquina Virtual** e clique na máquina virtual que você criou.

![][5]

Para adicionar um ponto de extremidade a uma máquina virtual, clique na caixa **Pontos de Extremidade**.

![][6]

Clique em **Adicionar**. Ao provisionar uma nova máquina virtual, você pode habilitar ou desabilitar os pontos de extremidade, conforme necessário.

Configure o ponto de extremidade:

1.	Digite um nome para o ponto de extremidade em **Ponto de Extremidade**.
2.	Digite 80 na **Porta Pública**. Se você alterou a porta de escuta padrão do Apache, você deve atualizar a Porta privada para ser a mesma que a porta de escuta do Apache.
3.	Digite 80 na **Porta Pública**. Por padrão, o tráfego HTTP usa a porta 80. Se você defini-lo como 80, não é necessário incluir o número da porta na URL que permite que você acesse o serviço Web Apache. Por exemplo: http://lampdemo.cloudapp.net. Se você definir a porta de escuta do Apache para outro valor, como 81, você precisará adicionar o número da porta à URL para acessar o serviço Web Apache. Por exemplo, http://lampdemo.cloudapp.net:81/.

![][7]

Clique em **OK** para adicionar o ponto de extremidade à máquina virtual.




###Etapa 2: Conectar-se à imagem criada
Você pode escolher qualquer ferramenta SSH para se conectar à nova máquina virtual. Neste exemplo, usamos Putty.

Primeiro, obtenha o nome DNS da máquina virtual no Portal do Azure. Clique em **Procurar -> Máquinas virtuais ->** o nome de sua máquina virtual **-> Propriedades** e, em seguida, verifique o campo **Nome do Domínio** do bloco **Propriedades**.

Obtenha o número da porta para conexões SSH no campo **SSH**. Aqui está um exemplo.

![][8]

Baixe o Putty [aqui](http://www.putty.org/) .

Após o download, clique no arquivo executável PUTTY.EXE. Configure as opções básicas com o nome do host e o número da porta obtido nas propriedades da sua máquina virtual. Aqui está um exemplo:

![][9]

No painel esquerdo, clique em **Conexão -> SSH -> Autenticação** e, em seguida, clique em **Procurar** para especificar o local do arquivo **privateKey.ppk**, que contém a chave privada gerada pelo PuTTYGen na Fase 1: criar uma imagem. Aqui está um exemplo:

![][10]

Clique em **Abrir**. Você pode ser alertado por uma caixa de mensagem. Se você configurou o nome DNS e o número da porta corretamente, clique em **Sim**.

![][11]


Você deve ver o seguinte.

![][12]

Insira o nome de usuário especificado quando você criou a máquina virtual na Fase 1: Criar uma imagem Você verá algo semelhante ao que se segue:

![][13]

##Fase 3: Instalar a pilha LAMP

Dependendo da distribuição do Linux que você usou para criar sua máquina virtual, há diferentes maneiras de instalar a pilha LAMP. As seguintes seções contêm as etapas comuns para alguns sistemas operacionais do Linux.

###Base de Red Hat, CentOS

####Instalar o Apache
Para instalar o Apache, abra o terminal e execute este comando:

	sudo yum install httpd

Uma vez instalado, inicie o Apache com este comando:

	sudo service httpd start

####Testar Apache
Para verificar se o Apache foi instalado com êxito, navegue até o nome DNS do seu servidor Apache (para a URL de exemplo neste artigo, http://lampdemo.cloudapp.net/)). A página deve exibir as palavras “It works!”![][14]

####Solucionar problemas
Se o Apache estiver sendo executado, mas você não conseguir ver a página padrão do Apache acima, você precisará verificar o seguinte:

-	Endereço / porta de escuta do Serviço Web Apache
	-	Verifique a configuração do ponto de extremidade da sua máquina virtual do Azure. Certifique-se de que a configuração do ponto de extremidade seja apropriada. Consulte a Fase 1: Criar instruções de imagem neste artigo.
	-	Abra /etc/httpd/conf/httpd.conf e, em seguida, pesquise a cadeia de caracteres “Listen”. Certifique-se de que a porta de escuta do Apache seja igual à porta privada que você configurou para o ponto de extremidade. A porta padrão para Apache é 80. Aqui está um exemplo.  

			……
			......
				# prevent Apache from glomming onto all bound IP addresses (0.0.0.0)
				#
				#Listen 12.34.56.78:80
				Listen 80
				#
				# Dynamic Shared Object (DSO) Support
			……
			……  

-	Configurações de firewall, iptables. Se você pode ver a página padrão do Apache no host local, então, o problema pode ser que a porta na qual o Apache está escutando esteja bloqueada pelo firewall. Você pode usar a ferramenta w3m para navegar até a página da Web do Apache. Os seguintes comandos instalam o w3m e navegam até a página padrão do Apache:

		sudo yum  install w3m w3m-img  
		w3m http://localhost

	Se o problema for causado pelo firewall ou iptables, adicione as seguintes linhas para /etc/sysconfig/iptables:

		-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
		-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

	Observe que a segunda linha é necessária apenas para tráfego https.

	Certifique-se de que essas linhas estejam acima de todas as linhas que globalmente restringiriam o acesso, como as seguintes:

		-A INPUT -j REJECT --reject-with icmp-host-prohibited  

	Para que as novas configurações tenham efeito, use o seguinte comando:

		service iptables restart

####Instalar MySQL
Para instalar o MySQL, abra terminal e execute estes comandos:

	sudo yum install mysql-server
	sudo service mysqld start

Durante a instalação, o MySQL solicitará sua permissão duas vezes. Após dizer “Sim” a ambas as consultas, o MySQL será instalado.

####Configurar MySQL
Após a conclusão da instalação, você pode definir uma senha raiz para o MySQL com o seguinte:

	sudo /usr/bin/mysql_secure_installation  

O prompt solicitará sua senha raiz atual.

Uma vez que você acabou de instalar o MySQL, você provavelmente não deve ter uma, então, deixe em branco pressionando ENTER.

	Enter current password for root (enter for none):
	OK, successfully used password, moving on...  

Será solicitado que você crie uma senha raiz. Vá em frente, escolha “Sim” e siga as instruções.

CentOS automatiza o processo de configuração do MySQL, solicitando uma série de perguntas de “Sim” ou “Não”. Essas perguntas são mostradas abaixo. Escolha “Sim” ou “Não” para sua configuração. No final, o MySQL será recarregado e implementará as novas alterações.

	By default, a MySQL installation has an anonymous user, allowing anyone
	to log into MySQL without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.

	Remove anonymous users? [Y/n] y
	 ... Success!

	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.

	Disallow root login remotely? [Y/n] y
	... Success!

	By default, MySQL comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.

	Remove test database and access to it? [Y/n] y
	 - Dropping test database...
	 ... Success!
	 - Removing privileges on test database...
	 ... Success!

	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.

	Reload privilege tables now? [Y/n] y
	 ... Success!

	Cleaning up...

	All done!  If you've completed all of the above steps, your MySQL
	installation should now be secure.

	Thanks for using MySQL!  

####Instalar PHP
PHP é uma linguagem de scripts da Web de software livre amplamente usada para criar páginas da web dinâmicas.

Para instalar PHP na sua máquina virtual, abra o terminal e execute este comando:

	sudo yum install php php-mysql  

Responda “sim" para baixar os pacotes de software. Em seguida, responda “sim” para Importar chave GPG 0xE8562897 “CentOS-5 Key (chave de assinatura oficial de CentOS 5). PHP será instalado.

	warning: rpmts_HdrFromFdno: Header V3 DSA signature: NOKEY, key ID e8562897
	updates/gpgkey                                                                                                                                                                       | 1.5 kB     00:00
	Importing GPG key 0xE8562897 "CentOS-5 Key (CentOS 5 Official Signing Key) <centos-5-key@centos.org>" from /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
	Is this ok [y/N]: y  

###Base de Debian, Ubuntu
Isso foi testado no Ubuntu 14.04.

O Ubuntu baseia-se no Debian. Você pode instalar a pilha LAMP da mesma forma como para a série Red Hat. Para simplificar as etapas, use a ferramenta Tasksel.

Tasksel é uma ferramenta do Debian/Ubuntu que instala vários pacotes relacionados como uma tarefa coordenada no seu sistema. Para obter mais informações, veja [Tasksel – Ajuda da Comunidade Wiki](https://help.ubuntu.com/community/Tasksel).

Use tasksel para instalar o software necessário para a pilha LAMP.

- Para baixar as listas de pacotes nos repositórios e atualizá-las para obter informações sobre as versões mais recentes dos pacotes e suas dependências:  

		sudo apt-get update
-	Para instalar a pilha LAMP Ubuntu usando Tasksel:  

		sudo apt-get install tasksel
		sudo tasksel install lamp-server

Em seguida, use o assistente e escolha sua **senha raiz do MySQL**.

![][15]


##Testar a LAMP no seu servidor
Você pode testar o sistema LAMP criando uma página de informações rápidas de php.

Primeiro, crie um novo arquivo:

	sudo nano /var/www/html/info.php  

Adicione a linha a seguir:

	<?php
	phpinfo();
	?>  

Em seguida, salve e saia.

Reinicie o Apache para que todas as alterações entrem em vigor em seu computador. Se o sistema operacional da máquina virtual é o CentOS, use o seguinte comando para reiniciar o Apache:

	sudo service httpd restart

Se o sistema operacional da máquina virtual é o Ubuntu, use o seguinte comando para reiniciar o Apache:

	sudo service apache2 restart  

Conclua navegando até a página de informações de PHP (para o servidor Web de exemplo neste tópico, a URL seria http://lampdemo.cloudapp.net/info.php)).

O seu navegador deve ser semelhante a este:

![][16]

##Etapas adicionais

Como prática geral, você alterará algumas configurações padrão para se preparar para a implantação de aplicativo Web.

###Permita o acesso remoto ao MySQL
Se você tiver mais de uma VM instalada com o MySQL e elas necessitarem trocar dados, você deve habilitar o acesso remoto do MySQL e conceder as permissões apropriadas.

**Formato de referência do comando:**

	grant [authority] on [databaseName].[tableName] to [username]@[login host] identified by "[passwd]"  

**Exemplo:**

	grant select,insert,update,delete on studentManage.student to user1@"%" Identified by "abc";

Você também deve alterar o perfil /etc/mysql/my.cnf. Se você tiver linhas como esta:

	skip-networking
	bind-address = 127.0.0.1  

Você deve comentá-las (adicionar um # no início das linhas) e, em seguida, reiniciar o MySQL.

Para adicionar um ponto de extremidade para permitir o acesso remoto, consulte as instruções na Fase 1: Criar uma imagem para criar um novo ponto de extremidade. O número padrão da porta TCP de acesso do MySQL é 3306. Aqui está um exemplo:

![][17]

###Implantar seus aplicativos Web para o servidor apache
Uma vez que você tenha configurado a pilha LAMP com êxito, você pode implantar seu aplicativo Web existente para o servidor Web Apache (sua máquina virtual). Trata-se das mesmas etapas da implantação de um aplicativo Web existente num servidor Web físico.

-	A raiz do servidor Web está localizada em **/var/www/html**. Você deve conceder privilégios aos usuários que precisem carregar arquivos nesta pasta. O exemplo a seguir mostra como adicionar permissões de gravação a um grupo chamado lampappgroup e colocar o nome de usuário azureuser neste grupo:  

		sudo groupadd lampappgroup                      # Create a group
		sudo gpasswd -a azureuser lampappgroup    # Add azureuser to lampappgroup
		sudo chgrp lampappgroup /var/www/html/  # Change the ownership to group lampappgroup
		sudo chmod g+w /var/www/html/                 # grant write permission to group lampappgroup

	>[AZURE.NOTE]Talvez seja necessário fazer logon novamente se você quiser modificar um arquivo em /var/www/html /.
-	Use qualquer cliente SFTP (por exemplo, o FileZilla) para se conectar ao nome DNS da máquina virtual (por exemplo, lampdemo.cloudapp.net) e navegue até /**var/www/html** para publicar seu site.![][18]



##Problemas comuns e solução de problemas

###Não é possível acessar a Máquina Virtual com o Apache e o Moodle por meio da Internet

-	**Sintoma** O Apache está sendo executado, mas você não consegue ver a página padrão do Apache com o seu navegador.
-	**Possível causa raiz**
	1.	A porta de escuta do Apache não é a mesma que a Porta Privada do ponto de extremidade de sua máquina virtual para o tráfego da Web.</br> Verifique as configurações de ponto de extremidade da Porta Pública e da Porta Privada e certifique-se de que a Porta Privada é a mesma que a porta de escuta do Apache. Veja a Fase 1: crie uma imagem para obter instruções sobre como configurar pontos de extremidade para sua máquina virtual.</br> Para determinar a porta de escuta do Apache, abra /etc/httpd/conf/httpd.conf (versão Red Hat) ou /etc/apache2/ports.conf (versão Debian) e pesquise a cadeia de caracteres “Listen”. A porta padrão é 80.

	2.	O firewall desabilitou a porta de escuta do Apache.</br> Se você pode ver a página padrão do Apache no host local, então, o problema pode ser que a porta que está sendo escutada pelo Apache esteja bloqueada pelo firewall. Você pode usar a ferramenta w3m para procurar a página da Web. Os seguintes comandos instalam o w3m e navegam até a página padrão do Apache:

			sudo yum  install w3m w3m-img
			w3m http://localhost

-	**Solução**

	1.	Se a porta de escuta do Apache não for a mesma que a Porta Privada do ponto de extremidade para o tráfego da web na máquina virtual, você precisará alterar a Porta Privada do ponto de extremidade para ser a mesma que a porta de escuta do Apache.
	2.	Se o problema for causado pelo firewall/iptables, adicione as seguintes linhas para /etc/sysconfig/iptables:  

			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

		Observe que a segunda linha é necessária apenas para tráfego https.

		Certifique-se de que estejam acima de todas as linhas que globalmente restringiriam o acesso, como as seguintes:

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		Para recarregar o iptables, execute o seguinte comando:

			service iptables restart  

		Isso foi testado no CentOS 6.3.

###Permissão negada ao carregar seus arquivos de projeto para /var/www/html/  

-	**Sintoma** Quando você usa qualquer cliente SFTP (por exemplo, o FileZilla) para se conectar à sua máquina virtual e navegar até /var/www/html para publicar seu site, você recebe uma mensagem de erro semelhante à seguinte:  

		status:	Listing directory /var/www/html
		Command:	put "C:\Users\liang\Desktop\info.php" "info.php"
		Error:	/var/www/html/info.php: open for write: permission denied
		Error:	File transfer failed

-	**Possível causa raiz** Você não tem permissões para acessar a pasta /var/www/html.
-	**Solução** Você precisa obter permissão da conta raiz. Você pode alterar a propriedade da pasta raiz para o nome de usuário usado ao provisionar o computador. Aqui está um exemplo com o nome de conta azureuser:  

		sudo chown azureuser -R /var/www/html  

	Use a opção -R para aplicar as permissões para todos os arquivos dentro de um diretório também.

	Observe que este comando também funciona para diretórios. A opção -R altera as permissões para todos os arquivos e diretórios dentro do diretório. Veja um exemplo:

		sudo chown -R username:group directory  

	Este comando altera a propriedade (tanto de usuário como de grupo) para todos os arquivos e diretórios dentro do diretório e para o próprio diretório.

	O comando a seguir só altera a permissão do diretório da pasta, mas deixa os arquivos e pastas sozinhos dentro do diretório.

		sudo chown username:group directory  

###Não foi possível determinar com segurança o nome de domínio totalmente qualificado do servidor

-	**Sintoma** Quando você reinicia o servidor Apache usando um dos seguintes comandos:  

		sudo /etc/init.d/apache2 restart  # Debian release  

	ou o

		sudo /etc/init.d/httpd restart   # Red Hat release  

	Você obtém o seguinte erro:

		Restarting web server apache2
		apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName
		... waiting apache2:
		Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName  

-	**Possível causa raiz** Você não definiu o nome do servidor do Apache.

-	**Solução** Insira uma linha “ServerName localhost” em httpd.conf (versão Red Hat) ou apache2.conf (versão Debian) em /etc/apache2 e reinicie o Apache. O aviso desaparecerá.



[1]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-01.png
[2]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-02.png
[3]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-03.png
[4]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-04.png
[5]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-05.png
[6]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-06.png
[7]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-07.png
[8]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-08.png
[9]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-09.png
[10]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-10.png
[11]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-11.png
[12]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-12.png
[13]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-13.png
[14]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-14.png
[15]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-15.png
[16]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-16.png
[17]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-17.png
[18]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-18.jpg

<!---HONumber=Oct15_HO3-->