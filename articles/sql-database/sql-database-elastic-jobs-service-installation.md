<properties 
	pageTitle="Instalando trabalhos de banco de dados elástico" 
	description="Percorra a instalação do recurso de trabalho elástico." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="sidneyh"/>

# Instalando os componentes do trabalho de banco de dados elástico

O [pool de banco de dados elástico (visualização)](sql-database-elastic-pool-portal.md) fornece um modelo previsível para implantação de uma grande quantidade de bancos de dados. Após a criação de um pool de bancos de dados elásticos, você pode usar os **trabalhos de banco de dados elásticos** para executar tarefas administrativas em cada banco de dados no pool de bancos de dados elásticos. Por exemplo, você pode implantar um novo esquema, por exemplo, configurar uma política RLS em cada banco de dados a fim de restringir os dados apenas à pessoa com as credenciais corretas para exibir dados confidenciais. Veja como instalar os **trabalhos de banco de dados elástico**.

**Tempo estimado para conclusão:** 10 minutos.

## Pré-requisitos
* Uma assinatura do Azure. Para obter uma avaliação gratuita, confira [Um mês de avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).
* Um pool de bancos de dados elásticos. Consulte [Criar um pool elástico do Banco de Dados SQL (visualização)](sql-database-elastic-pool-portal.md).

## Instalar os componentes do serviço
Primeiro, entre no [Portal de Visualização do Azure](https://ms.portal.azure.com/#).


1. No modo de exibição de painel do pool de bancos de dados elásticos, clique em **Criar trabalho**.
2. Se você estiver criando um trabalho pela primeira vez, será necessário instalar os **trabalhos de banco de dados elástico** clicando em **TERMOS DA VISUALIZAÇÃO**. 
3. Aceite os termos clicando na caixa de seleção.
4. No modo de exibição "Instalar serviços", clique em **CREDENCIAIS DO TRABALHO**.

	![Instalando os serviços][1]

5. Digite um nome de usuário e uma senha para um administrador de banco de dados. Se já houver um usuário comum para execução de scripts em cada banco de dados no pool de bancos de dados elásticos, considere o uso desse mesmo usuário para eliminar a necessidade de adicionar um novo usuário a cada banco de dados para execução de script. Como parte da instalação, um novo servidor de Banco de Dados SQL é criado. Dentro desse novo servidor, um novo banco de dados, conhecido como banco de dados de controle, é criado e usado para conter os metadados para trabalhos de banco de dados elástico. O nome de usuário e senha criados aqui são usados para duas finalidades: (1) fazer logon no banco de dados de controle e (2) como a credencial para fazer logon em cada banco de dados no pool elástico sempre que você executa um trabalho de execução de script.
 
	![Criar nome de usuário e senha][2]
6. Clique no botão OK. Os componentes são criados para você em questão de minutos em um novo [Grupo de recursos](../resource-group-portal.md). O novo grupo de recursos é fixado à tela inicial, conforme mostrado abaixo. Depois de criados, os trabalhos de banco de dados elástico (Serviço de Nuvem, Banco de Dados SQL, Barramento de Serviço e Armazenamento) são criados no grupo.

	![grupo de recursos na tela inicial][3]


7. Se você tentar criar ou gerenciar um trabalho durante instalação dos trabalhos de banco de dados elástico, ao fornecer as **Credenciais**, verá a mensagem a seguir.

	![Implantação ainda em andamento][4]

8. Se a instalação falhar por algum motivo, exclua o grupo de recursos. Confira [Como desinstalar os componentes de trabalho do banco de dados elástico](sql-database-elastic-jobs-uninstall.md)


## Próximas etapas

Se uma nova credencial tiver sido fornecida durante a instalação dos trabalhos de banco de dados elástico, e que ainda não exista em cada banco de dados no pool de bancos de dados elásticos com os direitos apropriados para execução de script, a credencial deverá ser criada em cada banco de dados. Consulte [Como adicionar usuários a um pool de bancos de dados elásticos](sql-database-elastic-jobs-add-logins-to-dbs.md). Para entender a criação do trabalho, confira [Criando e gerenciando um trabalho de banco de dados elástico](sql-database-elastic-jobs-create-and-manage.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/incomplete.png
 

<!---HONumber=62-->