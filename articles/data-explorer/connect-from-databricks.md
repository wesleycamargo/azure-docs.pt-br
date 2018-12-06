---
title: Conectar-se ao Azure Data Explorer a partir de bancos de dados do Azure Databricks usando Python
description: Este tópico mostrará a você como usar a biblioteca Python em bancos de dados do Azure Databricks para acessar dados do Azure Data Explorer (ADX) usando um dos dois métodos de autenticação.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 53f51db8d1b495f9a6faec86450d2b4e08a4fb72
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52429921"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-using-python"></a>Conectar-se ao Azure Data Explorer a partir de bancos de dados do Azure Databricks usando Python

O [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) é uma plataforma de análise baseada no Apache Spark otimizada para a plataforma de Serviços de Nuvem do Microsoft Azure. Este artigo mostra como usar a biblioteca Python em bancos de dados do Azure Databricks para acessar dados do Azure Data Explorer (ADX). Há várias maneiras de se autenticar com o ADX, incluindo o login do dispositivo e o aplicativo do Azure Active Directory Domain Services (Azure AD).

## <a name="prerequisites"></a>Pré-requisitos

- [Criar um cluster do Azure Data Explorer e o banco de dados](/azure/data-explorer/create-cluster-database-portal)
- [Crie uma área de trabalho do Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)

    Sob **serviço do Azure Databricks**, no **tipo de preço** lista suspensa, selecione **Premium**. Isso permite que você use os segredos do Azure Databricks para armazenar suas credenciais e referenciá-las em blocos de anotações e tarefas.

- [Crie um cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) em bancos de dados do Azure Databricks com as seguintes especificações (configurações mínimas necessárias para executar os blocos de notas de amostra):

![Criar cluster](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-python-library-on-your-azure-databricks-cluster"></a>Instalar a biblioteca Python em seu cluster do Azure Databricks

Para instalar a [Biblioteca Python](/azure/kusto/api/python/kusto-python-client-library) em seu cluster do Azure Databricks:

1. Vá para o espaço de trabalho do Azure Databricks e [Crie uma biblioteca](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)
2. [Carregar um pacote de PyPI Python ou Ovo de Python](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg)
    - Carregue, instale e anexe a biblioteca ao seu cluster do Databricks.
    - Digite o nome do PyPi: *azure-kusto-data*

## <a name="connect-to-adx-using-device-login"></a>Conecte-se ao ADX usando o login do dispositivo

[Importe um Notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) usando o bloco de notas [Query-ADX-device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) para se conectar ao ADX usando suas credenciais.

## <a name="connect-to-adx-using-azure-ad-app"></a>Conectar-se ao ADX usando o Aplicativo Azure AD

1. Crie o Aplicativo Azure AD [Provisionando um aplicativo do AAD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Conceder acesso ao seu Aplicativo Azure AD em seu banco de dados do Data Explorer do Azure da seguinte maneira:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | o nome do seu banco de dados |
    | ```AAD App ID``` | seu ID de Aplicativo Azure AD |
    | ```AAD Tenant ID``` | seu ID de locatário do Microsoft Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Encontre seu ID de locatário do Microsoft Azure AD

Para autenticar um aplicativo, o Data Explorer do Azure usa seu ID de locatário do Microsoft Azure AD. Para encontrar seu ID de locatário, use a seguinte URL, substituindo seu domínio por *YourDomain*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Por exemplo, se o seu domínio for *contoso.com*, a URL será: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Clique nesta URL para ver os resultados; a primeira linha é a seguinte. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Seu locatário ID é `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Armazenar e proteger sua ID e chave de Aplicativo Azure AD 

Armazene e proteja o ID e a chave de Aplicativo Azure AD usando os Segredos do [Azure Databricks](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) da seguinte forma:
1. [Configurar a CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)
1. [Instalar a CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) 
1. [Configurar a autenticação](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Configurar o [segredos](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) usando os comandos de exemplo a seguir:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importar um Notebook
[Importe um Notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) usando o bloco de anotações [Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) para conectar-se ao ADX. Atualize os valores de espaço reservado com seu nome do cluster, o nome de banco de dados e a ID do locatário do Microsoft Azure AD.