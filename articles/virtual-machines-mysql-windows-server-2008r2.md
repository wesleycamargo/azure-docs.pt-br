<properties linkid="manage-windows-common-tasks-install-mysql" urlDisplayName="Instalar MySQL" pageTitle="Cria uma máquina virtual executando o MySQL no Azure " metaKeywords="Máquinas virtuais Azure, servidor Azure do Windows, Azure instalando o MySQL, Azure configurando o MySQL, banco de dados do Azure" description="Cria uma máquina virtual Azure executando o Windows Server 2008 R2, e em seguida instala e configura o banco de dados MySQL na máquina virtual." metaCanonical="" services="virtual-machines" documentationCenter="" title="Instala MySQL na máquina virtual executando o Windows Server 2008 R2 no Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />





#Instala MySQL na máquina virtual executando o Windows Server 2008 R2 no Azure

[MySQL](http://www.mysql.com) é um Banco de Dados SQL fonte aberto popular. Usando o [Portal de Gerenciamento Azure][AzurePreviewPortal], você pode criar uma máquina virtual que execute o Windows Server 2008 R2 da galeria de imagens.  Em seguida, você pode instalar e configurar um banco de dados MySQL na máquina virtual.

Neste tutorial, você aprenderá a:

- Use o Portal de Gerenciamento para criar uma máquina virtual com o Windows Server 2008 R2.

- Instalar e executar MySQL Community Server em uma máquina virtual.

##Crie uma máquina virtual que executa Windows Server 2008 R2

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

##Anexar um disco de dados

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

##Instalar e executar MySQL Community Server em uma máquina virtual
Execute o seguinte comando para instalar, configurar e executar o MySQL Community Server:

1. Depois de se conectar à máquina virtual usando a Área de Trabalho Remota, abra o **Internet Explorer** a partir do menu **Iniciar**. 

2. Selecione o botão **Ferramentas** no canto superior direito. Em **Opções da Internet**, selecione a guia **Segurança**, em seguida selecione o ícone **Sites confiáveis** e, por fim, clique no botão **Sites**. Adicione *http://\*.mysql.com* à lista de sites confiáveis.

3. Vá para [Download MySQL Community Server][MySQLDownloads]

4. Selecione **Microsoft Windows** no **plataforma** lista suspensa menu e clique **selecione**

5. Localizar a versão mais recente do **o Windows (x86, 64-bit), o instalador MSI** e clique em **Download** 

6. Selecione **não, Obrigado, basta começar meu download!** (ou registre-se para uma conta).  Se solicitado, selecione um site de espelhamento para baixar o instalador do MySQL e salvar o instalador na área de trabalho.

7. Clique duas vezes no arquivo na área de trabalho para iniciar a instalação.

8. Clique em **Avançar**.

	![O programa de instalação do MySQL][MySQLInstall1]

9. Aceite os termos de licença e clique em **Avançar**.

	![O programa de instalação do MySQL][MySQLInstall2]

10. Clique em **típica** para instalar recursos comuns.

	![O programa de instalação do MySQL][MySQLInstall3]

11. Clique em Instalar **Agora**.

	![O programa de instalação do MySQL][MySQLInstall4]

12. Inicie o Assistente de configuração do MySQL e clique em **próximo**

	![Configurar MySQL][MySQLConfig1]

13. Selecione **configuração detalhada** e clique em Avançar.

	![Configurar MySQL][MySQLConfig2]

14. Selecione **máquina servidor** se você planeja executar MySQL junto com outros aplicativos no servidor, ou selecione a opção que melhor atenda às suas necessidades.  Clique em **Avançar**.

	![Configurar MySQL][MySQLConfig3]

15. Selecione **múltiplas funções de banco de dados**, ou a opção de selecionar que melhor atenda às suas necessidades.  Clique em **Avançar**.

	![Configurar MySQL][MySQLConfig4]

16. Selecione a unidade de dados que você anexou nas etapas acima.

	![Configurar MySQL][MySQLConfig5]

17. Selecione **Suporte a decisão (DSS)/OLAP**, ou a opção de selecionar que melhor atenda às suas necessidades.  Clique em **Avançar**.

	![Configurar MySQL][MySQLConfig6]

18. Selecione **habilitar rede TCP/IP** e **Adicionar exceção de firewall para esta porta** (Isso criará uma regra de entrada no Firewall do Windows chamado **servidor MySQL**).

	![Configurar MySQL][MySQLConfig7]

19. Selecione **melhor suporte para Multilingualism** se você precisa armazenar texto em vários idiomas ou selecione a opção que melhor atenda às suas necessidades.  Clique em **Avançar**.

	![Configurar MySQL][MySQLConfig8]

20. Selecione **instalar como serviço Windows** e **iniciar o servidor MySQL automaticamente**  Selecione também **incluir diretório Bin no caminho do Windows** Clique em **Avançar**.

	![Configurar MySQL][MySQLConfig9]

21. Digite a senha raiz. Não verificar **permitir acesso à raiz de máquinas remotas** ou **criar uma conta anônima**  Clique em **Avançar**.

	![Configurar MySQL][MySQLConfig10]

22. Clique em **Execute** e aguarde que a configuração seja concluída.

	![Configurar MySQL][MySQLConfig11]

23. Clique em **Concluir**.

	![Configurar MySQL][MySQLConfig12]

24. Clique em **iniciar** e selecione **MySQL 5. x, o cliente de linha de comando** para iniciar o cliente de linha de comando.

25.  Digite a senha raiz no prompot (que você definiu na etapa anterior) e será exibido um prompt onde você poderá executar instruções SQL para interagir com o banco de dados.

26. Para criar um novo usuário do MySQL, execute o seguinte no prompt **mysql>**:

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Observe que o ponto e vírgula (;) no final das linhas são cruciais para encerrar os comandos.

27. Para criar um banco de dados e conceder as permissões de usuário `mysqluser` execute os seguintes comandos:

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Observe que as senhas e nomes de usuário do banco de dados só são usadas pelos scripts de conexão com o banco de dados.  Nomes de conta de usuário do banco de dados não representam, necessariamente, contas de usuário reais no computador.

28. Para fazer logon em outro computador, execute o seguinte:

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	onde `ip-address` é o endereço IP do computador do qual você se conectará ao MySQL.
	
29. Para sair do cliente da linha de comando MySQL, emita o seguinte comando:

		quit

30. Depois que o MySQL for instalado, você deverá configurar um ponto de extremidade para que o MySQL possa ser acessado remotamente. Faça logon no [Portal de Gerenciamento do Azure][AzurePreviewPortal]. No Portal do Azure, clique em **Máquinas Virtuais** e, em seguida, clique no nome da sua nova máquina virtual e clique em **Pontos de extremidade**, e em seguida em  **Adicionar Pontos de extremidade**.

	![Pontos de extremidade][AddEndPoint]

31. Selecione **Adicionar ponto final** e clique na seta para continuar.
	
	![Pontos de extremidade][AddEndPoint2]

32. Adicione um ponto de extremidade com o nome "MySQL", protocolo **TCP**, e portas **públicas** e **particulares** definidas como "3306". Clique na marca de seleção. Isso permitirá que o MySQL seja acessado remotamente.
	
	![Pontos de extremidade][AddEndPoint3]

33. Você pode se conectar remotamente ao MySQL em execução em sua máquina virtual no Azure.  De um computador local executando o MySQL, execute o seguinte comando para efetuar login como o **mysqluser** usuário que você criou nas etapas acima:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Por exemplo, usando a máquina virtual criada neste tutorial, o comando seria:

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

##Resumo

Neste tutorial, você aprendeu como criar uma máquina virtual de Windows 2008 R2 e conectar-se remotamente a ele. Você também aprendeu como instalar e configurar o MySQL na máquina virtual, criar um banco de dados e um novo usuário do MySQL. Para obter mais informações sobre o MySQL, consulte [Documentação do MySQL](http://dev.mysql.com/doc/).

[AzurePreviewPortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/


[MySQLInstall1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png
[MySQLInstall2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png
[MySQLInstall3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png
[MySQLInstall4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png
[MySQLConfig1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png
[MySQLConfig2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png
[MySQLConfig3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png
[MySQLConfig4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png
[MySQLConfig5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
[MySQLConfig6]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png
[MySQLConfig7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
[MySQLConfig8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
[MySQLConfig9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
[MySQLConfig10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png
[MySQLConfig11]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png
[MySQLConfig12]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png
[AddEndPoint]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png
[AddEndPoint2]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png
[AddEndPoint3]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png

