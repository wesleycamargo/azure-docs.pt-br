---
title: Usar CLI do Databricks do Azure Cloud Shell | Microsoft Docs
description: Saiba como usar a CLI do Databricks do Azure Cloud Shell.
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: nitinme
ms.openlocfilehash: 8e4213813ff23586ac683556d4a3c0c587edea58
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Usar Databricks CLI do Azure Cloud Shell

Saiba como usar a CLI do Databricks do Azure Cloud Shell para executar operações em Databricks.

## <a name="prerequisites"></a>pré-requisitos

* Um cluster e espaço de trabalho do Azure Databricks. Para obter instruções, consulte [Introdução ao Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Configurar um token de acesso pessoal no Databricks. Para obter instruções, consulte o [Gerenciamento de Token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Usar o Azure Cloud Shell

1. Faça logon no [Portal do Azure](https://portal.azure.com).
 
2. No canto superior direito, clique no ícone do **Cloud Shell**.

   ![Iniciar Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Iniciar ODBC do Excel")

3. Certifique-se de selecionar **Bash** para o ambiente do Cloud Shell. É possível selecionar pela opção da lista suspensa, como mostrado na captura de tela a seguir.

   ![Iniciar Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Iniciar ODBC do Excel") 

4. Crie um ambiente virtual onde você poderá instalar a CLI do Databtricks. No trecho de código abaixo, você cria um ambiente virtual chamado `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Alterne para o ambiente virtual criado.

       source databrickscli/bin/activate

6. Instale a CLI do Databricks.

       pip install databricks-cli

7. Configure a autenticação com o Databricks utilizando o token de acesso que você criou e listado como parte dos pré-requisitos. Use o seguinte comando:

       databricks configure --token

    Você receberá os prompts a seguir:

    * Você será solicitado a inserir o host do Databricks. Insira o valor no formato `https://eastus2.azuredatabricks.net`. Aqui, **Leste dos EUA 2** é a região do Azure onde o espaço de trabalho do Azure Databricks foi criado.

    * Você será solicitado a inserir um nome de usuário. Insira o **token**.

    * Finalmente, você será solicitado a inserir a senha. Insira o token que você criou anteriormente.

Após concluir essas etapas, será possível iniciar usando a CLI do Databricks do Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Usar CLI do Databricks

Agora, você pode iniciar usando a CLI do Databricks. Por exemplo, execute o comando a seguir para listar todos os clusters do Databricks que existirem no espaço de trabalho.

    databricks clusters list

Também é possível usar o comando a seguir para acessar o DBFS (Sistema de Arquivos Databricks).

    databricks fs ls


Para obter uma referência completa nos comandos, consulte [CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a CLI do Azure, consulte [Visão geral da CLI do Azure](../cloud-shell/overview.md)
* Para ver uma lista de comandos da CLI do Azure, consulte [Referência da CLI do Azure CLI](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Para ver uma lista de comandos da CLI do Databricks, consulte [CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)


