---
title: "Inserir um relatório no Azure Power BI Embedded | Microsoft Docs"
description: "Saiba como inserir um relatório no Power BI Embedded em seu aplicativo."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 3d865af2418c9c557c861a379766a125d75cebf1
ms.lasthandoff: 03/14/2017


---
# <a name="embed-a-report-in-power-bi-embedded"></a>Inserir um relatório em um Power BI Embedded

Saiba como inserir um relatório no Power BI Embedded em seu aplicativo.

Vamos examinar como realmente inserir um relatório em seu aplicativo. Isso presume que você já tenha um relatório que existe dentro de um espaço de trabalho em sua coleção de espaço de trabalho. Se você ainda não fez essa etapa, consulte [Introdução ao Power BI Embedded](power-bi-embedded-get-started.md).

É possível usar o SDK para .NET (C#) ou Node.js junto com o JavaScript para criar seu aplicativo facilmente com o Power BI Embedded. 

## <a name="using-the-access-keys-to-use-rest-apis"></a>Usando as teclas de acesso para usar APIs REST

Para chamar a API REST, você pode passar a tecla de acesso que você pode obter no Portal do Azure para uma determinada coleção de espaço de trabalho. Para obter mais informações, consulte [Introdução ao Power BI Embedded](power-bi-embedded-get-started.md).

## <a name="get-a-report-id"></a>Obter uma ID de relatório

Cada token de acesso se baseia em um relatório. Você precisará obter a ID de relatório específica do relatório que você deseja inserir. Isso pode ser feito com base em chamadas para a API REST [Obter Relatórios](https://msdn.microsoft.com/library/azure/mt711510.aspx). Isso retornará a ID do relatório e a URL de inserção. Isso pode ser feito usando o SDK do .NET do Power BI ou chamando a API REST diretamente.

### <a name="using-the-power-bi-net-sdk"></a>Usando o SDK do .NET do Power BI

Ao usar o SDK do .NET, você precisará criar uma credencial de token que se baseia na tecla de acesso que você obtém do Portal do Azure. Isso exige que você instale o [pacote NuGet da API do Power BI](https://www.nuget.org/profiles/powerbi).

**Instalar o pacote NuGet**

```
Install-Package Microsoft.PowerBI.Api
```

**Código C#**

```
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>Chamar a API REST diretamente

```
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>Criar um token de acesso

O Power BI Embedded usa tokens de inserção, que são Tokens Web JSON assinados por HMAC. Os tokens são assinados com a tecla de acesso da sua coleção de espaço de trabalho do Azure Power BI Embedded. Tokens de inserção, por padrão, são usados para fornecer acesso somente leitura a um relatório a ser inserido em um aplicativo. Tokens de inserção são emitidos para um relatório específico e devem ser associados uma URL de inserção.

Tokens de acesso devem ser criados no servidor conforme as chaves de acesso são usadas para assinar/criptografar os tokens. Para obter informações sobre como criar um token de acesso, consulte [Autenticação e autorização com o Power BI Embedded](power-bi-embedded-app-token-flow.md). Você também pode examinar o método [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_). Aqui está um exemplo de qual seria a aparência disso usando o SDK do .NET para o Power BI.

Você usará a ID de relatório que você recuperou anteriormente. Depois de criar o token de inserção, você usará a tecla de acesso para gerar o token que você pode usar da perspectiva do javascript. A *classe PowerBIToken* exige que você instale o [pacote NuGet do Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalar o pacote NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Código C#**

```
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Adicionando escopos de permissão para tokens de inserção

Ao usar os tokens de inserção, convém restringir o uso de recursos aos quais você concede acesso. Por esse motivo, você pode gerar um token com as permissões no escopo. Para obter mais informações, consulte [Escopos](power-bi-embedded-app-token-flow.md#scopes)

## <a name="embed-using-javascript"></a>Inserir usando JavaScript

Depois de ter o token de acesso e a ID do relatório, é possível inserir o relatório usando JavaScript. Isso exige que você instale o [pacote JavaScript Power BI](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/) do NuGet. O embedUrl será apenas https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Você pode usar a [Amostra de inserção de relatório JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) para testar a funcionalidade. Ele também fornece exemplos de código para as diferentes operações que estão disponíveis.

**Instalar o pacote NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Código JavaScript**

```
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
```

### <a name="set-the-size-of-embedded-elements"></a>Defina o tamanho dos elementos inseridos

O relatório será inserido automaticamente com base no tamanho do respectivo contêiner. Substituir o tamanho padrão das inserções simplesmente adiciona um atributo de classe CSS ou estilos embutidos para largura e altura.

## <a name="see-also"></a>Consulte também

[Introdução a exemplos](power-bi-embedded-get-started-sample.md)  
[Autenticando e autorizando com o Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Amostra de inserção de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Pacote JavaScript do Power BI](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Pacote NuGet da API do Power BI](https://www.nuget.org/profiles/powerbi)
[Pacote NuGet do Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Repositório de Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repositório de Git PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  
Mais perguntas? [Experimentar a comunidade do Power BI](http://community.powerbi.com/)

