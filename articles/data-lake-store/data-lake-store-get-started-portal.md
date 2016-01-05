<properties 
   pageTitle="Introdução ao Repositório Data Lake | Azure" 
   description="Usar o portal para criar uma conta do Repositório Data Lake e executar operações básicas no Repositório Data Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="11/13/2015"
   ms.author="nitinme"/>

# Introdução ao Repositório do Azure Data Lake usando o Portal do Azure

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)

Saiba como usar o Portal do Azure para criar uma conta do Repositório do Azure Data Lake e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para saber mais sobre o Repositório Data Lake, consulte [Visão geral do Repositório Data Lake](data-lake-store-overview.md).

## Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="signup"></a>Habilitar sua assinatura do Azure para a visualização pública do Repositório Data Lake

Primeiro você precisa solicitar a habilitação de sua assinatura do Azure para a visualização pública do Repositório Data Lake. Execute as etapas abaixo.

1. Entre no novo [Portal do Azure](https://portal.azure.com).
2. Clique em **NOVO**, clique em **Dados + Armazenamento** e clique em **Repositório Azure Data Lake**.
3. Na folha **Novo Repositório Data Lake**, clique em **Inscreva-se para visualizar**. Revise as informações e clique em **OK**. Você receberá um email quando sua assinatura tiver sido habilitada para a Visualização Pública.

	![Inscrever-se para a Visualização Pública](./media/data-lake-store-get-started-portal/preview-signup.png "Criar uma nova conta do Azure Data Lake")

## Criar uma conta do Repositório Azure Data Lake

1. Entre no novo [Portal do Azure](https://portal.azure.com).

2. Clique em **NOVO**, clique em **Dados + Armazenamento** e clique em **Repositório Azure Data Lake**. Leia as informações na folha **Repositório Azure Data Lake** e clique em **Criar** no canto inferior esquerdo da folha.

3. Na folha **Novo Repositório Data Lake**, forneça os valores conforme mostrado na captura de tela abaixo:

	![Criar uma nova conta do Repositório Azure Data Lake](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Criar uma nova conta do Azure Data Lake")

	- **Assinatura**. Selecione a assinatura na qual você deseja criar uma nova conta do Repositório Data Lake.
	- **Grupo de Recursos**. Selecione um grupo de recursos existente ou clique em **Criar um grupo de recursos** para criar um. Um grupo de recursos é um contêiner que mantém os recursos relacionados para um aplicativo. Para saber mais, consulte [Grupos de Recursos no Azure](resource-group-overview.md#resource-groups).
	- **Local**: selecione um local no qual você deseja criar a conta do Data Lake.

4. Selecione **Fixar no Quadro Inicial** se você quiser que a conta do Repositório Data Lake possa ser acessada no Quadro inicial.

5. Clique em **Criar**. Se você escolher fixar a conta no quadro inicial, você será levado de volta para o quadro inicial e poderá ver o progresso de seu provisionamento de conta do Repositório Data Lake. Após o provisionamento da conta do Repositório Data Lake, a folha da conta será exibida.

6. Expanda o menu suspenso **Essenciais** para ver as informações sobre sua conta do Repositório Data Lake, como o grupo de recursos do qual ela faz parte, o local etc. Clique no ícone **Início Rápido** para ver links para outros recursos relacionados ao Repositório Data Lake.

	![Sua conta do Repositório Azure Data Lake](./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Sua conta do Azure Data Lake")

## <a name="createfolder"></a>Criar pastas na conta do Repositório Azure Data Lake

Você pode criar pastas em sua conta do Repositório Data Lake para gerenciar e armazenar dados.

1. Abra a conta do Repositório Data Lake que você acabou de criar. No painel esquerdo, clique em **Procurar**, clique em **Repositório Data Lake** e, na folha do Repositório Data Lake, clique no nome da conta sob a qual você deseja criar as pastas. Se você tiver fixado a conta no quadro inicial, clique no bloco da conta.

2. Na folha de sua conta do Repositório Data Lake, clique em **Gerenciador de Dados**.

	![Criar pastas na conta do Repositório Data Lake](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Criar pastas na conta do Repositório Data Lake")

3. Na folha de sua conta do Repositório Data Lake, clique em **Nova Pasta**, insira um nome para a nova pasta e clique em **OK**.
	
	![Criar pastas na conta do Repositório Data Lake](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Criar pastas na conta do Repositório Data Lake")
	
	A pasta recém-criada será listada na folha **Gerenciador de Dados**. Você pode criar pastas aninhadas em qualquer nível.

	![Crie pastas na conta do Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Crie pastas na conta do Data Lake")


## <a name="uploaddata"></a>Carregar dados na conta do Repositório Azure Data Lake

É possível carregar seus dados em uma conta do Repositório Data Lake diretamente no nível da raiz ou em uma pasta que você criou na conta. Na captura de tela abaixo, execute as etapas para carregar um arquivo em uma subpasta da folha **Gerenciador de Dados**. Nessa captura de tela, o arquivo é carregado em uma subpasta mostrada na trilha (marcada em uma caixa vermelha).

Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData).

![Carregar dados](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Carregar dados")


## <a name="properties"></a>Propriedades e ações disponíveis para os dados armazenados

Clique no arquivo recém-adicionado para abrir a folha **Propriedades**. As propriedades associadas ao arquivo e as ações que você pode executar no arquivo estão disponíveis nesta folha. Você também pode copiar o caminho completo para o arquivo em sua conta do Repositório Azure Data Lake, realçada na caixa vermelha na captura de tela abaixo.

![Propriedades nos dados](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Propriedades nos dados")

* Clique em **Visualizar** para uma visualização do arquivo, diretamente do navegador. Você também pode especificar o formato da visualização. Clique em **Visualizar**, clique em **Formato** na folha **Visualização de Arquivo** e, na folha **Formato de Visualização do Arquivo**, especifique as opções, como o número de linhas para exibição, codificação a ser usada, o delimitador etc.

  ![Formato de visualização do arquivo](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Formato de visualização do arquivo")

* Clique em **Baixar** para baixar o arquivo em seu computador.

* Clique em **Renomear arquivo** para renomear o arquivo.

* Clique em **Excluir arquivo** para excluir o arquivo.


## Proteja seus dados

Você pode proteger os dados armazenados em sua conta do Repositório Azure Data Lake usando o Active Directory do Azure e o controle de acesso (ACLs). Para obter instruções sobre como fazer isso, consulte [Protegendo dados no Repositório Azure Data Lake](data-lake-store-secure-data.md).


## Excluir a conta do Repositório Azure Data Lake

Para excluir uma conta do Repositório Azure Data Lake da folha de seu Repositório Data Lake, clique em **Excluir**. Para confirmar a ação, você receberá uma solicitação para inserir o nome da conta que você deseja excluir. Insira o nome da conta e clique em **Excluir**.

![Excluir conta do Data Lake](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Excluir conta do Data Lake")

## Outras maneiras de criar uma conta do Repositório Data Lake

- [Introdução ao Repositório Data Lake usando o PowerShell](data-lake-store-get-started-powershell.md)
- [Introdução ao Repositório Data Lake usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
- [Introdução ao Repositório Data Lake usando o SDK do CLI](data-lake-store-get-started-cli.md)


## Próximas etapas

- [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
- [Usar a Análise Data Lake do Azure com o Repositório Data Lake](data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_1217_2015-->