---
title: 'Início rápido: consultar dados usando a biblioteca Python do Azure Data Explorer'
description: Neste início rápido, você aprende como consultar dados do Azure Data Explorer usando Python.
services: data-explorer
author: mgblythe
ms.author: mblythe
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: fee982812456548ed6d1e15d86151df88532389f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956091"
---
# <a name="quickstart-query-data-using-the-azure-data-explorer-python-library"></a>Início rápido: consultar dados usando a biblioteca Python do Azure Data Explorer

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O Azure Data Explorer fornece uma biblioteca cliente de [dados para Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Esta biblioteca permite consultar dados do seu código. Neste início rápido, você se conecta a uma tabela no *help cluster* que configuramos para ajudar no aprendizado. Em seguida, você consulta uma tabela nesse cluster e retorna os resultados.

Este início rápido também está disponível como um [Azure Notebook](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de email da organização que é membro do Azure Active Directory (AAD)

* [Python](https://www.python.org/downloads/) instalado no seu computador de desenvolvimento

## <a name="install-the-data-library"></a>Instalar a biblioteca de dados

Instale *azure-kusto-data*.

```python
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Adicionar instruções de importação e constantes

Importe classes da biblioteca, bem como *pandas*, uma biblioteca de análise de dados.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
import pandas as pd
```

Para autenticar um aplicativo, o Azure Data Explorer usa seu ID de locatário do AAD. Para encontrar seu ID de locatário, use o seguinte URL, substituindo seu domínio por *YourDomain*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Por exemplo, se o seu domínio for *contoso.com*, o URL será: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Clique nesta URL para ver os resultados; a primeira linha é a seguinte. 

```
`"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"`
```

O ID do inquilino neste caso é `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Defina o valor para AAD_TENANT_ID antes de executar este código.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE  = "Samples"
```

Agora, construa a cadeia de caracteres de conexão. Este exemplo usa a autenticação do dispositivo para acessar o cluster. Você também pode usar o certificado de aplicativo AAD, a chave de aplicativo AAD e o usuário e a senha AAD.

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Conecte-se ao Azure Data Explorer e execute uma consulta

Execute uma consulta no cluster e armazene a saída em um quadro de dados. Quando esse código é executado, ele retorna uma mensagem como a seguinte: *Para entrar, use um navegador da web para abrir a página https://microsoft.com/devicelogin e digite o código F3W4VWZDM para autenticar*. Siga as etapas para entrar e retorne para executar o próximo bloco de código.

```python
KUSTO_CLIENT  = KustoClient(KCSB)
KUSTO_QUERY  = "StormEvents | sort by StartTime desc | take 10"

df = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, KUSTO_QUERY).primary_results[0].to_dataframe()
```

## <a name="explore-data-in-dataframe"></a>Explorar dados no DataFrame

Depois que você insere um sinal, a consulta retorna resultados e eles são armazenados em um quadro de dados. Você pode trabalhar com os resultados como qualquer outro quadro de dados.

```python
df
```

Você deve ver os dez primeiros resultados da tabela StormEvents.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início rápido: insira dados usando a biblioteca Python do Data Explorer do Azure](python-ingest-data.md)