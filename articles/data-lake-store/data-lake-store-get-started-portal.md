---
title: "Usar o portal do Azure para uma introdução ao Data Lake Store | Microsoft Docs"
description: "Use o portal do Azure para criar uma conta do Data Lake Store e executar operações básicas no Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: fea324d0-ad1a-4150-81f0-8682ddb4591c
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/06/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: fa13266993017374ba49709f8e22fbe6b03a28c7
ms.contentlocale: pt-br
ms.lasthandoff: 08/07/2017

---
# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Introdução ao Azure Data Lake Store usando o portal do Azure
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [CLI 2.0 do Azure](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Saiba como usar o portal do Azure para criar uma conta do Azure Data Lake Store e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta etc. Para obter mais informações, consulte [Visão geral do Azure Data Lake Store](data-lake-store-overview.md).

Os dois vídeos a seguir contêm as mesmas informações conforme descrito neste artigo:

* [Criar uma conta do Repositório Data Lake](https://mix.office.com/watch/1k1cycy4l4gen)
* [Gerenciar dados no Repositório Data Lake usando o Explorador de Dados](https://mix.office.com/watch/icletrxrh6pc)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter os seguintes itens:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta do Repositório Azure Data Lake

1. Inscreva-se no novo [portal do Azure](https://portal.azure.com).
2. Clique em **NOVO**, clique em **Dados + Armazenamento** e clique em **Azure Data Lake Store**. Leia as informações na folha **Azure Data Lake Store** e clique em **Criar** no canto inferior esquerdo da folha.
3. Na folha **Novo Data Lake Store**, forneça os valores conforme mostrado na captura de tela a seguir:
   
    ![Criar uma nova conta do Azure Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Criar uma nova conta do Azure Data Lake Store")
   
   * **Nome**. Insira um nome exclusivo para a conta do Data Lake Store.
   * **Assinatura**. Selecione a assinatura na qual você deseja criar uma nova conta do Data Lake Store.
   * **Grupo de Recursos**. Selecione um grupo de recursos existente ou selecione a opção **Criar novo** para criar um. Um grupo de recursos é um contêiner que mantém os recursos relacionados para um aplicativo. Para saber mais, consulte [Grupos de Recursos no Azure](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Local**: selecione um local no qual você deseja criar a conta do Data Lake.
   * **Configurações de Criptografia**. Há três opções:
     
     * **Não habilite a criptografia**.
     * **Use chaves gerenciadas pelo Azure Data Lake**.  se quiser que o Azure Data Lake Store gerencie suas chaves de criptografia.
     * **Escolha as chaves no Azure Key Vault**. Você pode selecionar um Azure Key Vault existente ou criar um novo Key Vault. Para usar as chaves em um Key Vault, você deve atribuir permissões para a conta do Azure Data Lake Store para acessar o Azure Key Vault. Para obter instruções, consulte [Atribuir permissões ao Azure Key Vault](#assign-permissions-to-azure-key-vault).
       
        ![Criptografia do Data Lake Store](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Criptografia do Data Lake Store")
       
        Clique em **OK** na folha **Configurações de Criptografia**.

        Para obter mais informações, consulte [Criptografia dos dados no Azure Data Lake Store](./data-lake-store-encryption.md).

4. Clique em **Criar**. Se você escolher fixar a conta no painel, você será levado de volta para o painel e poderá ver o progresso de seu provisionamento de conta do Data Lake Store. Após o provisionamento da conta do Repositório Data Lake, a folha da conta será exibida.

Você também pode criar uma conta do Data Lake Store usando modelos do Azure Resource Manager. Esses modelos são acessíveis nos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):

- Sem criptografia de dados: [Implantar conta do Azure Data Lake Store sem criptografia de dados](https://azure.microsoft.com/en-us/resources/templates/101-data-lake-store-no-encryption/).
- Com criptografia de dados usando o Data Lake Store: [Implantar conta do Data Lake Store com criptografia (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
- Com criptografia de dados usando o Azure Key Vault: [Implantar conta do Data Lake Store com criptografia (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).

### <a name="assign-permissions-to-azure-key-vault"></a>Atribuir permissões ao Azure Key Vault
Se você tiver usado as chaves de um Cofre de Chaves do Azure para configurar a criptografia na conta do Data Lake Store, deverá configurar o acesso entre a conta do Data Lake Store e a conta do Cofre de Chaves do Azure. Execute as seguintes etapas para fazê-lo.

1. Se você tiver usado as chaves do Cofre de Chaves do Azure, a folha da conta do Data Lake Store exibirá um aviso na parte superior. Clique no aviso para abrir a folha **Configurar permissões do Cofre de Chaves**.
   
    ![Criptografia do Data Lake Store](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Criptografia do Data Lake Store")
2. A folha mostra duas opções para configurar o acesso.
   
   * Na primeira opção, clique em **Conceder Permissão** para configurar o acesso. A primeira opção só será habilitada quando o usuário que criou a conta do Data Lake Store também for um administrador do Cofre de Chaves do Azure.
   * A outra opção é executar o cmdlet do PowerShell exibido na folha. Você precisa ser o proprietário do Cofre de Chaves do Azure ou ter a capacidade de conceder permissões no Cofre de Chaves do Azure. Depois de executar o cmdlet, volte para a folha e clique em **Habilitar** para configurar o acesso.

## <a name="createfolder"></a>Criar pastas na conta do Repositório Azure Data Lake
Você pode criar pastas em sua conta do Repositório Data Lake para gerenciar e armazenar dados.

1. Abra a conta do Data Lake Store que você criou. No painel esquerdo, clique em **Procurar**, clique em **Data Lake Store** e, na folha Data Lake Store, clique no nome da conta sob a qual você deseja criar as pastas. Se você tiver fixado a conta no quadro inicial, clique no bloco da conta.
2. Na folha de sua conta do Repositório Data Lake, clique em **Gerenciador de Dados**.
   
    ![Criar pastas na conta do Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Criar pastas na conta do Data Lake Store")
3. Na folha de sua conta do Data Lake Store, clique em **Nova Pasta**, insira um nome para a nova pasta e clique em **OK**.
   
    ![Criar pastas na conta do Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Criar pastas na conta do Data Lake Store")
   
    A pasta recém-criada é listada na folha **Data Explorer**. Você pode criar pastas aninhadas em qualquer nível.
   
    ![Criar pastas na conta do Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Criar pastas na conta do Data Lake")

## <a name="uploaddata"></a>Carregar dados na conta do Repositório Azure Data Lake
É possível carregar seus dados em uma conta do Repositório Data Lake diretamente no nível da raiz ou em uma pasta que você criou na conta. Na captura de tela a seguir, acompanhe as etapas para carregar um arquivo em uma subpasta na folha **Data Explorer**. Nessa captura de tela, o arquivo é carregado em uma subpasta mostrada no caminho navegado (marcado em uma caixa vermelha).

Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Carregar dados](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Carregar dados")

## <a name="properties"></a>Propriedades e ações disponíveis para os dados armazenados
Clique no arquivo recém-adicionado para abrir a folha **Propriedades** . As propriedades associadas ao arquivo e as ações que você pode executar no arquivo estão disponíveis nesta folha. Você também pode copiar o caminho completo para o arquivo em sua conta do Azure Data Lake Store, em destaque na caixa vermelha na captura de tela a seguir:

![Propriedades nos dados](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Propriedades nos dados")

* Clique em **Visualizar** para uma visualização do arquivo, diretamente do navegador. Você também pode especificar o formato da visualização. Clique em **Visualização**, clique em **Formato** na folha **Visualização de Arquivo** e, na folha **Formato de Visualização do Arquivo**, especifique as opções, como o número de linhas para exibição, codificação a ser usada, o delimitador, etc.
  
  ![Formato de arquivo de visualização](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Formato de arquivo de visualização")
* Clique em **Baixar** para baixar o arquivo em seu computador.
* Clique em **Renomear arquivo** para renomear o arquivo.
* Clique em **Excluir arquivo** para excluir o arquivo.

## <a name="secure-your-data"></a>Proteja seus dados
Você pode proteger os dados armazenados em sua conta do Repositório Azure Data Lake usando o Active Directory do Azure e o controle de acesso (ACLs). Para obter instruções sobre como fazer isso, consulte [Protegendo dados no Repositório Azure Data Lake](data-lake-store-secure-data.md).

## <a name="delete-azure-data-lake-store-account"></a>Excluir a conta do Repositório Azure Data Lake
Para excluir uma conta do Repositório Azure Data Lake da folha de seu Repositório Data Lake, clique em **Excluir**. Para confirmar a ação, você receberá uma solicitação para inserir o nome da conta que você deseja excluir. Insira o nome da conta e clique em **Excluir**.

![Excluir conta do Data Lake](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Excluir conta do Data Lake")

## <a name="next-steps"></a>Próximas etapas
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
* [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Acessar os logs de diagnóstico do Azure Data Lake Store](data-lake-store-diagnostic-logs.md)


