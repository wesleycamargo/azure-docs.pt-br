---
title: "Atualização assíncrona para modelos do Azure Analysis Services | Microsoft Docs"
description: "Aprenda a codificar atualização assíncrona usando a API REST."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/18/2017
ms.author: owend
ms.openlocfilehash: 06d807b83f700c675c6979998dd8f74372a4845f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2017
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Atualização assíncrona com a API REST
Ao usar qualquer linguagem de programação que seja compatível com chamadas REST, você pode executar operações de atualização de dados assíncronas em seus modelos de tabela do Azure Analysis Services. Isso inclui a sincronização de réplicas somente leitura para expansão de consulta. 

As operações de atualização de dados podem demorar algum tempo, dependendo de uma série de fatores, incluindo o volume de dados, o nível de otimização usando partições, etc. Essas operações têm sido tradicionalmente invocadas com métodos existentes, como o uso de [TOM](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (Tabular Object Model), de cmdlets do [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) ou de [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (Tabular Model Scripting Language). No entanto, esses métodos geralmente exigem conexões HTTP não confiáveis de execução longa.

A API REST do Azure Analysis Services permite que as operações de atualização de dados sejam realizadas de forma assíncrona. Ao usar a API REST, as conexões HTTP de execução longa de aplicativos cliente não são necessárias. Também há outros recursos internos para a confiabilidade, como repetições automáticas e confirmações em lote.

## <a name="base-url"></a>URL base

A URL base segue este formato:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Por exemplo, considerando um modelo chamado AdventureWorks, em um servidor chamado myserver, localizado na região do Azure Oeste dos EUA, o nome do servidor será:

```
asazure://westus.asazure.windows.net/myserver 
```

A URL base para esse nome do servidor será:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Ao usar a URL base, os recursos e as operações poderão ser acrescentados com base no seguinte: 

![Atualização assíncrona](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Qualquer coisa que termine em **s** é uma coleção.
- Qualquer coisa que termine com **()** é uma função.
- Qualquer outra coisa será um recurso/objeto.

Por exemplo, você pode usar o verbo POST na coleção Refreshes para executar uma operação de atualização, dessa forma:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um token do Azure Active Directory (OAuth 2) válido no cabeçalho Authorization e devem atender aos seguintes requisitos:

- O token deve ser um token de usuário ou uma entidade de serviço de aplicativo.
- O usuário ou o aplicativo deve ter permissões suficientes no servidor ou no modelo para fazer a chamada solicitada. O nível de permissão é determinado pelas funções no modelo ou pelo grupo de administradores no servidor.
- O token deve ter a audiência correta definida como `https://*.asazure.windows.net`.

## <a name="post-refreshes"></a>POST /refreshes

Para executar uma operação de atualização, use o verbo POST na coleção /refreshes para adicionar um novo item de atualização à coleção. O cabeçalho Location na resposta inclui a ID de atualização. O aplicativo cliente poderá se desconectar e verificar o status mais tarde, se necessário, porque ele é assíncrono.

Apenas uma operação de atualização por vez é aceita para um modelo. Se houver uma operação de atualização atualmente em execução e outra operação for enviada, o código de status HTTP 409 Conflito será retornado.

O corpo pode ser semelhante ao seguinte:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>parâmetros
Não é necessário especificar parâmetros. O padrão será aplicado.

|NOME  |type  |DESCRIÇÃO  |Padrão  |
|---------|---------|---------|---------|
|type     |  Enum       |  O tipo de processamento a ser executado. Os tipos são alinhados com os tipos de [comandos de atualização](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) da TMSL: full, clearValues, calculate, dataOnly, automatic, add e defragment.       |   automático      |
|CommitMode     |  Enum       |  Determina se os objetos serão confirmados em lotes ou somente na conclusão. Os modos incluem: default, transactional, partialBatch.  |  transacional       |
|MaxParallelism     |   int      |  Esse valor determina o número máximo de threads nos quais executar comandos de processamento em paralelo. Isso é alinhado com a propriedade MaxParallelism, que pode ser definida no [comando Sequence](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) da TMSL ou com o uso de outros métodos.       | 10        |
|RetryCount    |    int     |   Indica o número de vezes que a operação será repetida antes de falhar.      |     0    |
|Objetos     |   Matriz      |   Uma matriz de objetos a serem processados. Cada objeto inclui: "table" ao processar a tabela inteira ou "table" e "partition" ao processar uma partição. Se nenhum objeto for especificado, todo o modelo será atualizado. |   Processar todo o modelo      |

O CommitMode é igual ao partialBatch. Ele é usado ao fazer uma carga inicial de grandes conjuntos de dados, que pode levar horas. Se a operação de atualização falhar depois de confirmar com êxito um ou mais lotes, os lotes confirmados com êxito permanecerão confirmados (ela não reverterá lotes confirmadas com êxito).

> [!NOTE]
> No em que esse texto foi escrito, o tamanho do lote era o valor de MaxParallelism, mas esse valor poderá ser alterado.

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

Para verificar o status de uma operação de atualização, use o verbo GET na ID da atualização. Aqui está um exemplo do corpo da resposta. Se a operação estiver em andamento, **inProgress** será retornado no status.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>GET /refreshes

Para obter uma lista com o histórico das operações de atualização de um modelo, use o verbo GET na coleção /refreshes. Aqui está um exemplo do corpo da resposta. 

> [!NOTE]
> No momento em que esse texto foi escrito, os últimos 30 dias de operações de atualização eram armazenados e retornados, mas esse número poderá ser alterado.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshes/\<refreshId>

Para cancelar uma operação de atualização em andamento, use o verbo DELETE na ID da atualização.

## <a name="post-sync"></a>POST /sync

Ao realizar as operações de atualização, poderá ser necessário sincronizar os novos dados com as réplicas para a expansão da consulta. Para executar uma operação de sincronização de um modelo, use o verbo POST na função /sync. O cabeçalho Location na resposta inclui a ID da operação de sincronização.

## <a name="get-sync-status"></a>GET /sync status

Para verificar o status de uma operação de sincronização, use o verbo GET passando a ID da operação como um parâmetro. Aqui está um exemplo do corpo da resposta:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Valores de syncstate:

- 0: replicando. Os arquivos do banco de dados estão sendo replicados para uma pasta de destino.
- 1: reidratação. O banco de dados está sendo reidratado em instâncias de servidor somente leitura.
- 2: concluída. A operação de sincronização foi concluída com êxito.
- 3: falha. A operação de sincronização falhou.
- 4: finalizando. A operação de sincronização foi concluída, mas está executando etapas de limpeza.

## <a name="code-sample"></a>Exemplo de código

Aqui está um exemplo de código em C# para você começar, [RestApiSample on GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Para usar o exemplo de código

1.  Clone ou baixe o repositório. Abra a solução RestApiSample.
2.  Localize a linha **client.BaseAddress = …** e forneça sua [URL base](#base-url).

O exemplo de código pode usar um logon interativo, nome de usuário/senha, ou uma [entidade de serviço](#service-principle).

#### <a name="interactive-login-or-usernamepassword"></a>Logon interativo ou nome de usuário/senha

Essa forma de autenticação exige que um aplicativo do Azure seja criado com as permissões de API necessárias atribuídas. 

1.  No Portal do Azure, clique em **Novo** > **Azure Active Directory** > **Registros do aplicativo** > **Novo registro do aplicativo**.

    ![Novo Registro de aplicativo](./media/analysis-services-async-refresh/aas-async-app-reg.png)


2.  Em **Criar**, digite um nome e, em seguida, selecione **Nativo** em tipo de aplicativo. Para **URI de Redirecionamento**, digite **urn:ietf:wg:oauth:2.0:oob** e, em seguida, clique em **Criar**.

    ![Configurações](./media/analysis-services-async-refresh/aas-async-app-reg-name.png)

3.  Selecione seu aplicativo e, em seguida, copie e salve a **ID do Aplicativo**.

    ![Copiar ID do aplicativo](./media/analysis-services-async-refresh/aas-async-app-id.png)

4.  Em **Configurações**, clique em **Permissões necessárias** > **Adicionar**.

    ![Adicionar acesso à API](./media/analysis-services-async-refresh/aas-async-add.png)

5.  Em **Selecionar uma API**, digite **SQL Server Analysis Services** na caixa de pesquisa e, em seguida, selecione **Azure Analysis Services (Azure SQL Server Analysis Services)**.

    ![Selecionar API](./media/analysis-services-async-refresh/aas-async-select-api.png)

6.  Selecione **Leitura e gravação em todos os modelos** e, em seguida, clique em **Selecionar**. Quando as duas estiverem selecionadas, clique em **Concluído** para adicionar as permissões. Pode levar alguns minutos para propagar.

    ![Selecionar Leitura e gravação em todos os modelos](./media/analysis-services-async-refresh/aas-async-select-read.png)

7.  No exemplo de código, localize o método **UpdateToken()**. Observe o conteúdo deste método.
8.  Localize **string clientID = …** e, em seguida, insira a **ID do Aplicativo** que você copiou na etapa 3.
9.  Execute o exemplo.

#### <a name="service-principal"></a>Entidade de serviço

Consulte a postagem de blog [Automação do Azure Analysis Services com entidades de serviço e o PowerShell](https://azure.microsoft.com/blog/automation-of-azure-analysis-services-with-service-principals-and-powershell/) para saber como configurar uma entidade de serviço e atribuir as permissões necessárias no Azure Analysis Services. Ao concluir as etapas detalhadas na postagem do blog, execute mais essas etapas:

1.  No exemplo de código, localize **string authority = …**, substitua **common** pela ID de locatário da sua organização.
2.  Comente/remova a marca de comentário para que a classe ClientCredential seja usada para instanciar o objeto de credencial. Verifique se os valores \<App ID> e \<App Key> podem ser acessados de forma segura ou use autenticação baseada em certificado para as entidades de serviço.
3.  Execute o exemplo.


## <a name="see-also"></a>Consulte também

[Exemplos](analysis-services-samples.md)   
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)   


