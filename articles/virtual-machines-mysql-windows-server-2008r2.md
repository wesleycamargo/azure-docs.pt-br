<properties 
	pageTitle="Criar uma máquina virtual que executa MySQL no Azure" 
	description="Crie uma máquina virtual do Azure executando o Windows Server 2008 R2 e, em seguida, instale e configure um banco de dados MySQL na máquina virtual." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="kathydav"/>


#Instala MySQL na máquina virtual executando o Windows Server 2008 R2 no Azure

[MySQL](http://www.mysql.com) é um banco de dados SQL de software livre popular. Usando o [Portal de Gerenciamento Azure][AzurePreviewPortal], você pode criar uma máquina virtual que execute o Windows Server 2008 R2 da galeria de imagens. Em seguida, você pode instalar e configurar um banco de dados MySQL na máquina virtual.

Este tutorial mostra como:

- Usar o Portal de Gerenciamento para criar uma máquina virtual com o Windows Server 2008 R2.

- Instalar e executar MySQL Community Server na máquina virtual.

##Criar uma máquina virtual na qual o Windows Server está em execução

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

##Anexar um disco de dados

Depois de criar uma máquina virtual, anexe um disco de dados. O disco fornece o armazenamento de dados necessário quando você instala o MySQL. Consulte [Como anexar um disco de dados para uma máquina virtual do Windows](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/) e siga as instruções para anexar um disco vazio.

##Faça logon na máquina virtual
A seguir, você fará logon na máquina virtual para que você possa instalar o MySQL.

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

##Instalar e executar MySQL Community Server na máquina virtual
Execute o seguinte comando para instalar, configurar e executar o MySQL Community Server:

1. Depois de se conectar à máquina virtual usando a Área de Trabalho Remota, abra o **Internet Explorer** a partir do menu **Iniciar**. 

2. Selecione o botão **Ferramentas** no canto superior direito. Em **Opções da Internet**, selecione a guia **Segurança**, em seguida selecione o ícone **Sites confiáveis** e, por fim, clique no botão **Sites**. Adicione *http://\*.mysql.com* à lista de sites confiáveis.

3. Vá para [Download MySQL Community Server][MySQLDownloads].

4. Selecione **Microsoft Windows** no **plataforma** lista suspensa menu e clique **selecione**.

5. Localizar a versão mais recente do **o Windows (x86, 64-bit), o instalador MSI** e clique em **Download**. 

6. Selecione **Não, obrigado, basta começar meu download!** (ou registre-se para uma conta).  Se solicitado, selecione um local de espelhamento para baixar o instalador do MySQL e salvar o instalador na área de trabalho.

7. Clique duas vezes no arquivo na área de trabalho para iniciar a instalação.

8. Clique em **Próximo**.

9. Aceite os termos de licença e clique em **Avançar**.

10. Clique em **típica** para instalar recursos comuns.

11. Clique em **Instalar**.

12. Inicie o Assistente de configuração do MySQL e clique em **próximo**.

13. Selecione **configuração detalhada** e clique em Avançar.

14. Selecione **máquina servidor** se você planeja executar MySQL junto com outros aplicativos no servidor, ou selecione a opção que melhor atenda às suas necessidades.  Clique em **Próximo**.

15. Selecione **múltiplas funções de banco de dados**, ou a opção de selecionar que melhor atenda às suas necessidades.  Clique em **Próximo**.

16. Selecione a unidade de dados que você anexou na seção anterior.

	![Configure MySQL][MySQLConfig5]

17. Selecione **Suporte a decisão (DSS)/OLAP**, ou a opção de selecionar que melhor atenda às suas necessidades.  Clique em **Próximo**.

18. Selecione **habilitar rede TCP/IP** e **Adicionar exceção de firewall para esta porta** (Isso criará uma regra de entrada no Firewall do Windows chamado **servidor MySQL**).

	![Configure MySQL][MySQLConfig7]

19. Se precisar armazenar texto em muitos idiomas diferentes, selecione **Melhor suporte para múltiplos idiomas**. Caso contrário, escolha uma das outras opções.  Clique em **Próximo**.

	![Configure MySQL][MySQLConfig8]

20. Selecione **instalar como serviço Windows** e **iniciar o servidor MySQL automaticamente**.  Selecione também **incluir diretório Bin no caminho do Windows**. Clique em **Próximo**.

	![Configure MySQL][MySQLConfig9]

21. Digite a senha raiz. Não verificar **permitir acesso à raiz de máquinas remotas** ou **criar uma conta anônima**.  Clique em **Próximo**.

	![Configure MySQL][MySQLConfig10]

22. Clique em **Execute** e aguarde que a configuração seja concluída.

23. Clique em **Concluir**.

24. Clique em **iniciar** e selecione **MySQL 5. x, o cliente de linha de comando** para iniciar o cliente de linha de comando.

25.  Digite a senha raiz no prompt (que você definiu na etapa anterior) e será exibido um prompt onde você poderá executar instruções SQL para interagir com o banco de dados.

26. Para criar um novo usuário do MySQL, execute o seguinte no prompt **mysql>**:

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	The semi-colons (;) at the end of the lines are required for ending the commands.

27. Para criar um banco de dados e conceder as permissões de usuário `mysqluser`nele, execute os comandos seguintes:

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Observe que as senhas e nomes de usuário do banco de dados só são usadas pelos scripts de conexão com o banco de dados.  Nomes de conta de usuário do banco de dados não representam, necessariamente, contas de usuário reais no computador.

28. Para fazer logon de outro computador, execute o seguinte comando:

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	where `ip-address` is the IP address of the computer from which you will connect to MySQL.
	
29. Para sair do cliente da linha de comando MySQL, execute o seguinte comando:

		quit

30. Após o MySQL estar instalado, configure um ponto de extremidade para que o MySQL possa ser acessado remotamente. Faça logon no [Portal de Gerenciamento do Azure][AzurePreviewPortal]. No Portal do Azure, clique em **Máquinas Virtuais** e, em seguida, clique no nome da sua nova máquina virtual e clique em **Pontos de extremidade**, e em seguida em **Adicionar Pontos de extremidade**.

31. Selecione **Adicionar ponto final** e clique na seta para continuar.
	

32. Adicione um ponto de extremidade com o nome "MySQL", protocolo **TCP**, e portas **públicas** e **particulares** definidas como "3306". Clique na marca de seleção. Isso permite acessar o MySQL remotamente.
	

33. Teste sua conexão remota com MySQL.  De um computador local executando MySQL, execute um comando similar ao seguinte para fazer logon como o usuário **mysqluser**:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	For example, if you named the virtual machine "testwinvm", run this command:

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

##Recursos
Para obter informações sobre o MySQL, consulte a [Documentação do MySQL](http://dev.mysql.com/doc/).

[AzurePortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/

<!--HONumber=42-->
