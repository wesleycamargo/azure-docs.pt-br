---
title: Criar um cluster do Azure Data Explorer e um banco de dados usando a CLI
description: Este artigo descreve como criar um cluster e um banco de dados do Azure Data Explorer usando a CLI do Azure
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: howto
ms.date: 1/31/2019
ms.openlocfilehash: a4c9156ef80f05e247b1cfef0acd56b601a2db65
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812677"
---
# <a name="create-an-azure-data-explorer-cluster-and-a-database-using-cli"></a>Criar um cluster do Azure Data Explorer e um banco de dados usando a CLI

Este artigo descreve como criar um cluster e um banco de dados do Azure Data Explorer usando a CLI do Azure.

## <a name="whats-the-difference-between-the-management-plane-and-data-plane-apis"></a>Qual é a diferença entre as APIs do plano de dados e do plano de gerenciamento

Há duas bibliotecas diferentes de API, as APIs do Plano de Dados e de Gerenciamento.
As APIs de Gerenciamento são usadas para gerenciar os recursos, por exemplo, criar um cluster, criar um banco de dados, excluir uma conexão de dados, alterar a contagem do número de instâncias etc. As APIs do plano de dados são usadas para interagir com os dados, executar consultas, ingerir dados etc.

## <a name="configure-the-cli-parameters"></a>Configurar os parâmetros da CLI

Faça logon na sua conta

```Bash
az login
```

Defina a assinatura em que você gostaria de criar o cluster.

```Bash
az account set --subscription "your_subscription"
```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster do Azure Data Explorer

Crie o cluster usando o comando a seguir.

```Bash
az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
```

Forneça os seguintes valores

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | Nome | *azureclitest* | O nome desejado do cluster.|
   | sku | *D13_v2* | A SKU que será usada para o cluster. |
   | resource-group | *testrg* | O nome do grupo de recursos em que o cluster seria criado. |

Se você quiser, há mais parâmetros opcionais que você pode usar, como a capacidade do cluster etc.

Para verificar se o cluster foi criado com êxito, você pode executar

```Bash
az kusto cluster show --name azureclitest --resource-group testrg
```

Se o resultado contém "provisioningState" com valor de "Succeeded", isso significa que o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar o banco de dados no cluster do Azure Data Explorer

Crie seu banco de dados usando o comando a seguir.

```Bash
az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
```

Forneça os seguintes valores

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | nome do cluster | *azureclitest* | O nome do cluster em que deve ser criado.|
   | Nome | *clidatabase* | O nome desejado do seu banco de dados.|
   | resource-group | *testrg* | O nome do grupo de recursos em que o cluster seria criado. |
   | soft-delete-period | *3650:00:00:00* | Tempo pelo qual os dados devem ser mantidos para que estejam disponíveis para consulta. |
   | hot-cache-period | *3650:00:00:00* | Tempo pelo qual os dados devem ser mantidos no cache. |

Você pode ver o banco de dados criado executando

```Bash
az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
```

Isso é tudo, agora você tem um cluster e um banco de dados.

## <a name="clean-up-resources"></a>Limpar recursos

Caso você planeje seguir nossos outros guias de início rápido e tutoriais, mantenha os recursos que você criou.

Quando você exclui um cluster, também exclui todos os bancos de dados nele. Use o comando abaixo para excluir o cluster.

```Bash
az kusto cluster delete --name azureclitest --resource-group testrg
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início Rápido: Ingerir dados do Hub de Eventos no Azure Data Explorer](ingest-data-event-hub.md)
