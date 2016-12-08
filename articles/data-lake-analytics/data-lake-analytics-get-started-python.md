---
title: "Introdução ao Azure Data Lake Analytics usando o Python | Microsoft Docs"
description: 'Aprenda a usar o Python para criar uma conta do Data Lake Store e enviar trabalhos. '
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5618650671badfc54860c3ad8af5d1e727d3d8c9
ms.openlocfilehash: 40ccfc59cccd86a7634ec89656571b3cd23566b4


---


# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-python"></a>Tutorial: introdução ao Azure Data Lake Analytics usando o Python
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como usar o Python para criar contas do Azure Data Lake Analytics, definir trabalhos do Data Lake Analytics em [U-SQL](data-lake-analytics-u-sql-get-started.md) e enviar trabalhos para contas da Data Lake Analytics. Para saber mais sobre a Análise Data Lake, consulte a [Visão geral da Análise Data Lake do Azure](data-lake-analytics-overview.md).

Neste tutorial, você desenvolverá um trabalho que lê um arquivo TSV (Valores Separados por Tabulação) e o converterá em um arquivo CSV (Valores Separados por Vírgula). Para acompanhar o mesmo tutorial usando outras ferramentas compatíveis, clique nas guias na parte superior desta seção.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter os seguintes itens:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Um aplicativo do Azure Active Directory**. Você pode usar o aplicativo Azure AD para autenticar o aplicativo Data Lake Store com o Azure AD. Há diferentes abordagens para autenticar com o Azure AD, que são a autenticação de usuário final ou a autenticação serviço a serviço. Para obter instruções e saber mais sobre como autenticar, confira [Autenticar com o Data Lake Store usando o Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).
- **[Python](https://www.python.org/downloads/)**. Você deve usar a versão de 64 bits. Este artigo usa o Python versão 3.5.2.


## <a name="install-azure-python-sdk"></a>Instalar o SDK do Python do Azure

Para trabalhar com o Data Lake Store usando o Python, você precisa instalar três módulos.

O módulo azure-mgmt-datalake-store inclui as operações de gerenciamento de contas do Azure Data Lake Store. O módulo azure-mgmt-resource inclui outros módulos do Azure para o Active Directory, etc. O módulo de armazenamento azure-datalake-store inclui as operações de sistema de arquivos do Azure Data Lake Store. O módulo de análise azure-datalake-analytics inclui as operações do Azure Data Lake Analytics. Use os comandos a seguir para instalar os módulos.

    pip install azure-mgmt-resource
    pip install azure-mgmt-datalake-store
    pip install azure-mgmt-datalake-analytics
    pip install azure-datalake-store

## <a name="create-a-python-application"></a>Criar um aplicativo Python

1. Use o IDE de sua escolha para criar um novo aplicativo Python, por exemplo, mysample.py.

2. Adicione as linhas a seguir para importar os módulos necessários

        ## Use this only for Azure AD service-to-service authentication
        from azure.common.credentials import ServicePrincipalCredentials

        ## Use this only for Azure AD end-user authentication
        from azure.common.credentials import UserPassCredentials

        ## Required for Azure Resource Manager
        from azure.mgmt.resource.resources import ResourceManagementClient
        from azure.mgmt.resource.resources.models import ResourceGroup

        ## Required for Azure Data Lake Store account management
        from azure.mgmt.datalake.store.account import DataLakeStoreAccountManagementClient
        from azure.mgmt.datalake.store.account.models import DataLakeStoreAccount

        ## Required for Azure Data Lake Store filesystem management
        from azure.datalake.store import core, lib, multithread

        ## Required for Azure Data Lake Analytics account management
        from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeAnalyticsAccountProperties, DataLakeStoreAccountInfo

        ## Required for Azure Data Lake Analytics job management
        from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
        from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

        ## Required for Azure Data Lake Analytics catalog management
        from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

        ## Use these as needed for your application
        import logging, getpass, pprint, uuid, time

3. Salve as alterações no arquivo de aplicativo de Python.

## <a name="authentication"></a>Autenticação

Use um dos seguintes métodos para se autenticar:

### <a name="end-user-authentication-for-account-management"></a>Autenticação de usuário final para o gerenciamento de conta

Use esse método para se autenticar com o Azure AD para operações de gerenciamento de conta (criar/excluir conta do Data Lake Store etc.). Você deve fornecer o nome de usuário e a senha para um usuário do Azure AD. A conta de usuário não pode ser configurada para autenticação multifator e não pode ser uma conta da Microsoft/Live ID, por exemplo, @outlook.com, e @live.com.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Autenticação de serviço a serviço com o segredo do cliente para gerenciamento de conta

Use esse método para se autenticar com o Azure AD para operações de gerenciamento de conta (criar/excluir conta do Data Lake Store etc.). O trecho a seguir pode ser usado para autenticar seu aplicativo de forma não interativa, usando segredo do cliente para entidade de segurança/aplicativo. Use-o com o aplicativo "Aplicativo Web" Azure AD existente.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

## <a name="create-resource-management-group"></a>Criar grupo de Gerenciamento de Recursos

Use o seguinte trecho de código para criar um Grupo de Recursos do Azure:

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'

    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Resource Group
    armGroupResult = resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )


## <a name="create-data-lake-store-account"></a>Criar uma conta do Data Lake Store

Cada conta do Data Lake Analytics requer uma conta do Data Lake Store. Para obter instruções, confira [Criar uma conta do Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).



## <a name="create-data-lake-analytics-account"></a>Criar conta da Análise Data Lake

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'
    adlsAccountName = '<Azure Data Lake Store Account Name>'
    adlaAccountName = '<Azure Data Lake Analytics Account Name>'

    ## Create management client object
    adlaAcctClient = DataLakeAnalyticsAccountManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Data Lake Analytics account
    adlaAcctResult = adlaAcctClient.account.create(
        resourceGroup,
        adlaAccountName,
        DataLakeAnalyticsAccount(
            location=location,
            default_data_lake_store_account=adlsAccountName,
            data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adlsAccountName)]
        )
    ).wait()


##<a name="submit-data-lake-analytics-jobs"></a>Enviar trabalhos da Análise Data Lake

Os trabalhos da Análise Data Lake são escritos na linguagem U-SQL. Para saber mais sobre o U-SQL, confira [Introdução à linguagem U-SQL](data-lake-analytics-u-sql-get-started.md) e [Referência da linguagem U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

    ## Declare variables
    adlsAccountName = '<Azure Data Lake Store Account Name>'

    ## Create management client object
    adlaJobClient = DataLakeAnalyticsJobManagementClient(
        credentials,
        'azuredatalakeanalytics.net'
    )

    ## Submit a U-SQL job
    jobId = str(uuid.uuid4())
    jobResult = adlaJobClient.job.create(
        adlaAccountName,
        jobId,
        JobInformation(
            name='Sample Job',
            type='USql',
            properties=USqlJobProperties(
                script='DROP DATABASE IF EXISTS FOO; CREATE DATABASE FOO;'
            )
        )
    )

    ## Print the job result
    while(jobResult.state != JobState.ended):
        print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
        time.sleep(3)
        jobResult = adlaJobClient.job.get(adlaAccountName, jobId)
        
    print ('Job finished with result: ' + jobResult.result.value)

## <a name="next-steps"></a>Próximas etapas

- Para ver o mesmo tutorial usando outras ferramentas, clique nos seletores de guias na parte superior da página.
- Para ver uma consulta mais complexa, consulte [Analisar logs de site usando a Análise Data Lake do Azure](data-lake-analytics-analyze-weblogs.md).
- Para começar a desenvolver aplicativos U-SQL, consulte [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para aprender a usar o U-SQL, veja [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Para obter as tarefas de gerenciamento, confira [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md).
- Para obter uma visão geral da Análise Data Lake, consulte [Visão geral da Análise Data Lake do Azure](data-lake-analytics-overview.md).




<!--HONumber=Nov16_HO4-->


