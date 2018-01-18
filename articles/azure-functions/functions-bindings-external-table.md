---
title: "Associação de tabela externa do Azure Functions (experimental)"
description: "Usar as associações de tabela externa no Azure Functions"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 8a4358fa67e45d0b7a2df1519d649099b5ef5850
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Associação de tabela externa do Azure Functions (experimental)

Este artigo explica como trabalhar com dados tabulares em provedores de SaaS, como o Sharepoint e o Dynamics, no Azure Functions. O Azure Functions dá suporte a associações de entrada e saída para tabelas externas.

> [!IMPORTANT]
> A associação de Tabela Externa é experimental e pode nunca alcançar o status de GA (Disponibilidade Geral). Ela foi incluída somente no Azure Functions 1.x e não há planos de adicioná-la ao Azure Functions 2.x. Para os cenários que exigem acesso aos dados de provedores de SaaS, considere o uso de [aplicativos lógicos que chamem funções](functions-twitter-email.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Conexões de API

As associações de tabela aproveitam conexões de API externas para autenticar com provedores de SaaS terceirizados. 

Ao atribuir uma associação você pode criar uma nova conexão de API ou usar uma conexão de API existente dentro do mesmo grupo de recursos.

### <a name="available-api-connections-tables"></a>Conexões de API disponíveis (tabelas)

|Conector|Gatilho|Entrada|Saída|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 para Operações](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Google Sheets](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 para Finanças](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Banco de dados Oracle](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x

> [!NOTE]
> Conexões de tabela externa também podem ser usadas nos [Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="creating-an-api-connection-step-by-step"></a>Criar uma conexão de API: passo a passo

1. Na página portal do Azure para seu aplicativo de função, selecione o sinal de adição (**+**) para criar uma função.

1. Na caixa **Cenário**, selecione **Experimental**.

1. Selecione **Tabela externa**.

1. Selecione um idioma.

2. Em **Conexão da Tabela externa**, selecione uma conexão existente ou selecione **Nova**.

1. Para uma nova conexão, defina as configurações e selecione **Autorizar**.

1. Selecione **Criar** para criar a função.

1. Selecione **Integrar > Tabela Externa**.

1. Configure a conexão para usar sua tabela de destino. Essas configurações irão variar entre provedores de SaaS. Os exemplos são mostrados na seção a seguir.

## <a name="example"></a>Exemplo

Este exemplo conecta a uma tabela denominada "Contato" com as colunas Id, Nome e Sobrenome. O código lista as entidades de contato na tabela e registra os nomes e sobrenomes.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```

Aqui está o código de script do C#:

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retrieve table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

### <a name="sql-server-data-source"></a>Fonte de dados do SQL Server

Para criar uma tabela no SQL Server para usar com este exemplo, veja um script. `dataSetName` é “padrão”.

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets-data-source"></a>Fonte de dados de Planilhas do Google

Para criar uma tabela a ser usada com este exemplo no Google Docs, crie uma planilha com uma planilha denominada `Contact`. O conector não pode usar o nome de exibição da planilha. O nome interno (em negrito) precisa ser usado como dataSetName, por exemplo: `docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** adicione os nomes de coluna `Id`, `LastName`, `FirstName` à primeira linha e, em seguida, preencha os dados nas linhas seguintes.

### <a name="salesforce"></a>Salesforce

Para usar este exemplo com o Salesforce, `dataSetName` é "padrão".

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json*.

|Propriedade function.json | DESCRIÇÃO|
|---------|----------------------|
|**tipo** | Deve ser definido como `apiHubTable`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | O nome da variável que representa o item de evento no código de função. | 
|**conexão**| Identifica a configuração de aplicativo que armazena a cadeia de conexão da API. A configuração do aplicativo é criada automaticamente quando você adiciona uma conexão de API à interface de usuário integrada.|
|**dataSetName**|O nome do conjunto de dados que contém a tabela a ser lida.|
|**tableName**|O nome da tabela|
|**entityId**|Deve estar vazia para associações de tabela.

Um conector tabular fornece conjuntos de dados e cada conjunto de dados contém tabelas. O nome do conjunto de dados padrão é “padrão”. Os títulos de um conjunto de dados e uma tabela em vários provedores de SaaS estão listados abaixo:

|Conector|Conjunto de dados|Tabela|
|:-----|:---|:---| 
|**SharePoint**|Site|Lista do SharePoint
|**SQL**|Banco de dados|Tabela 
|**Google Sheet**|Planilha|Planilha 
|**Excel**|Arquivo do Excel|Folha 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
