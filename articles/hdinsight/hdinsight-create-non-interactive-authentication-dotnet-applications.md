<properties
	pageTitle="Criar aplicativos .NET HDInsight de autenticação não interativa | Microsoft Azure"
	description="Saiba como criar aplicativos .NET HDInsight de autenticação não interativa."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/13/2016"
	ms.author="jgao"/>

# Criar aplicativos .NET HDInsight de autenticação não interativa

Você pode executar seu aplicativo .NET do Azure HDInsight na própria identidade do aplicativo (não interativo) ou na identidade do usuário conectado do aplicativo (interativo). Para ver um exemplo de aplicativo interativo, confira [Enviar trabalhos Hive/Pig/Sqoop usando o SDK .NET do HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Este artigo mostra como criar um aplicativo .NET de autenticação não interativa para se conectar ao Azure HDInsight e enviar um trabalho Hive.

No aplicativo .NET, você precisará:

- Da sua ID de locatário da assinatura do Azure
- Da ID de cliente do aplicativo do Azure Directory
- Da chave secreta do aplicativo do Azure Directory  

O processo principal inclui as seguintes etapas:

2. Criar um aplicativo do Azure Directory.
2. Atribuir funções ao aplicativo do AD.
3. Desenvolver o aplicativo cliente.


##Pré-requisitos

- Cluster HDInsight. Você pode criar um usando as instruções encontradas no [tutorial de introdução](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 




## Criar um aplicativo do Azure Directory 
Quando você cria um aplicativo do Active Directory, o aplicativo e uma entidade de serviço são realmente criados. É possível executar o aplicativo sob a identidade do aplicativo.

Atualmente, você deve usar o Portal clássico do Azure para criar um novo aplicativo do Active Directory. Essa capacidade será adicionada ao portal do Azure em uma versão posterior. Você também pode executar essas etapas através do Azure PowerShell ou da CLI do Azure. Para saber mais sobre como usar o PowerShell ou a CLI com a entidade de serviço, confira [Autenticação de uma entidade de serviço com o Azure Resource Manager](../resource-group-authenticate-service-principal.md).

**Para criar um aplicativo do Azure Directory**

1.	Entre no [portal clássico do Azure](https://manage.windowsazure.com/).
2.	Selecione **Active Directory** no painel à esquerda.

    ![Active Directory do Portal clássico do Azure](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.	Selecione o diretório que você deseja usar para criar o novo aplicativo. Deve ser o existente.
4.	Clique em **Aplicativos** na parte superior para listar os aplicativos existentes.
5.	Clique em **Adicionar** na parte inferior para adicionar um novo aplicativo.
6.	Insira o **nome**, escolha **Aplicativo Web e/ou API Web** e clique em **Avançar**.

    ![novo aplicativo do azure active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.	Insira a **URL de Logon** e o **URI da ID do Aplicativo**. Para **URL DE LOGON**, forneça o URI para um site da Web que descreve seu aplicativo. A existência do site Web não é validada. Para o URI DA ID DO APLICATIVO, forneça o URI que identifica seu aplicativo. Clique em **Concluir**. O aplicativo demora alguns minutos para ser criado. Depois que o aplicativo tiver sido criado, o portal mostrará a página Visão Rápida do novo aplicativo. Não feche o portal.

    ![propriedades do novo aplicativo do azure active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**Para obter a ID de cliente e a chave secreta do aplicativo**

1.	Na página do aplicativo do AD recém-criado, clique em **Configurar** no menu superior.
2.	Faça uma cópia da **ID de Cliente**. Você precisará dela no aplicativo .NET.
3.	Em **Chaves**, clique no menu suspenso **Selecionar duração** e escolha **1 ano** ou **2 anos**. O valor da chave não será exibido até que você salve a configuração.
4.	Na parte inferior da página, clique em **Salvar**. Quando a chave secreta aparecer, faça uma cópia dela. Você precisará dela no aplicativo .NET.

##Atribuir o aplicativo do AD à função

Você deve atribuir o aplicativo a uma [função](../active-directory/role-based-access-built-in-roles.md) para conceder a ele permissões para executar ações. Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissões são herdadas de níveis inferiores do escopo (por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contiver). Neste tutorial, você definirá o escopo no nível de grupo de recursos. Como o Portal clássico do Azure não é compatível com grupos de recursos, essa parte precisa ser executada no Portal do Azure.

**Para adicionar a função de Proprietário ao aplicativo do AD**

1.	Entre no [Portal do Azure](https://portal.azure.com).
2.	Clique no **Grupo de Recursos** no painel esquerdo.
3.	Clique no grupo de recursos que contém o cluster HDInsight, no qual você executará a consulta Hive posteriormente neste tutorial. Se houver muitos grupos de recursos, você poderá usar o filtro.
4.	Clique em **Acessar** na folha do cluster.

    ![ícone de nuvem e raio = início rápido](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.	Clique em **Adicionar** na folha **Usuários**.
6.	Siga as instruções para adicionar a função **Proprietário** ao aplicativo do AD que você criou no último procedimento. Ao concluir a tarefa com êxito, você deverá ver o aplicativo listado na folha Usuários com a função de Proprietário.


##Desenvolver aplicativos do cliente HDInsight

Crie um aplicativo de console .net do C# seguindo as instruções encontrada em [Enviar trabalhos Hadoop no HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Em seguida, substitua o método GetTokenCloudCredentials por:

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

Para recuperar a ID de Locatário usando o PowerShell:

    Get-AzureRmSubscription

ou o CLI do Azure:

    azure account show --json

      
## Consulte também

- [Enviar trabalhos Hadoop no HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Criar o aplicativo do Active Directory e a entidade de serviço usando o portal](../resource-group-create-service-principal-portal.md)
- [Autenticação de uma entidade de serviço com o Azure Resource Manager](../resource-group-authenticate-service-principal.md)
- [Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_0615_2016-->