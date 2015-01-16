<properties urlDisplayName="How to create and configure an Azure SQL DB" pageTitle="Como criar e configurar um banco de dados SQL do Azure - tutorial do Azure" metaKeywords="Criar banco de dados SQL do Azure, Configurar o banco de dados SQL do Azure" description="Como criar e configurar um banco de dados SQL do Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Create and Configure an Azure SQL Database" authors="sidneyh" solutions="" manager="jhubbard" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sidneyh" />

<h1><a id="configLogical"></a>Como criar e configurar um banco de dados SQL do Azure</h1>

Neste tópico, você vai criar e configurar um banco de dados SQL do Azure usando o Portal de Gerenciamento do Azure. Neste fluxo de trabalho, você criará o servidor primeiro. Talvez você prefira essa abordagem se tiver bancos de dados do SQL Server existentes que deseja carregar.

##Sumário##
* [Como: Criar um servidor lógico](#createLogical)
* [Como: Configurar o firewall para o servidor lógico](#configFWLogical)

<h2><a id="createLogical"></a>Como: Criar um servidor lógico</h2>

1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com).

2. Na parte inferior da página, clique em **NOVO**.

	![Click SQL Databases][1]

3. Clique em **Serviços de Dados** e **Banco de dados SQL**, depois em **Criação Rápida**.

	![Click New, Data Services, and Quick Create][2]
	 
5. Nas **configurações do servidor de banco de dados SQL**, selecione uma assinatura.

	![Select a subscription][3]

6. Em **configurações do servidor de banco de dados SQL**, insira um nome de logon como uma palavra sem espaços. 

	O Banco de Dados SQL usa a Autenticação do SQL por meio de uma conexão criptografada. Será criado um novo login de autenticação do SQL Server atribuído à função do servidor fixo do sysadmin usando o nome fornecido. 

	Declarações ou autenticação do Windows não são suportadas no Banco de Dados SQL. O login não pode ser um endereço de e-mail, conta de usuário do Windows ou um ID do Windows Live.
	
7. Forneça uma senha forte com mais de oito caracteres, usando uma combinação de letras maiúsculas e minúsculas e um número ou um símbolo.

7. Escolha uma região. A Região determina a localização geográfica do servidor. As Regiões não podem ser facilmente alternadas, portanto, escolha uma que faça sentido para este servidor. Escolha um local mais próximo de você. Ao manter seu aplicativo do Azure e o banco de dados na mesma região, você economiza em custo de largura de banda de entrada e em latência de dados.

8. Certifique-se de manter a opção **Permitir que os Serviços do Windows Azure acessem o servidor** selecionada para que você possa se conectar ao banco de dados usando o Portal de Gerenciamento, os serviços de armazenamento e outros serviços no Azure. 

9. Clique na marca de seleção localizada na parte inferior da página quando tiver concluído.

###Nome do servidor gerado automaticamente

Observe que você não especificou um nome do servidor. O Banco de dados SQL gera automaticamente o nome do servidor para garantir que não existam entradas do DNS duplicadas. O nome do servidor é uma cadeia de 10 caracteres alfanuméricos. Você não pode alterar o nome do seu servidor de Banco de Dados SQL.

Na próxima etapa, você configurará o firewall para que as conexões de aplicativos em execução em sua rede tenham permissão de acesso.

##Como: Configurar o firewall para o servidor lógico

1. No [Portal de Gerenciamento](http://manage.windowsazure.com), clique em **banco de dados SQL**, então clique em **Servidores**

	![Click Servers][4]
2. Na lista, clique no servidor que você acabou de criar.

2. Clique em **Configurar**.

	![Click Configure][5]

3. Na seção **endereços ip permitidos**, clique em **ADICIONAR OS ENDEREÇOS IP PERMITIDOS**. Esse é o endereço IP no qual o roteador ou o servidor proxy está escutando no momento. O Banco de Dados SQL detecta o endereço IP usado pela conexão atual e cria uma regra de firewall para aceitar solicitações de conexão nesse dispositivo. 
	![Click Add to the allowed IP addresses][6]

	Um nome padrão para a regra é gerado. Edite o nome conforme desejar. 
	

4. Quando você se conectar ao banco de dados de outro computador, deve criar uma nova regra para permitir que o endereço IP se conecte. Use as caixas Início e Término para criar um intervalo de endereços IP.

	Se os computadores cliente usam endereços IP atribuídos dinamicamente, você pode especificar um intervalo que seja amplo o suficiente para incluir os endereços IP atribuídos aos computadores em sua rede. Inicie com um intervalo estreito e depois expanda-o apenas se for necessário.

7. Clique em **Salvar** na parte inferior da página para concluir a etapa. 

Agora você tem um servidor lógico e uma regra de firewall que permite conexões de entrada de seu endereço IP e um logon de administrador. 

**Observação:** O servidor lógico recém-criado é virtual e será hospedado dinamicamente em servidores físicos em um data center. A exclusão do servidor é uma ação não recuperável. Certifique-se de fazer backup dos bancos de dados que você posteriormente carregar para o servidor. 


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png

