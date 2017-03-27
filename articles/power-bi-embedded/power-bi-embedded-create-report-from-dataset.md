---
title: "Criar um novo relatório de um conjunto de dados no Azure Power BI Embedded | Microsoft Docs"
description: "Os relatórios do Power BI Embedded agora podem ser criados de um conjunto de dados em seu próprio aplicativo."
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
ms.openlocfilehash: 457f53aa76059dbb2faed6b264102f1f59b9918a
ms.lasthandoff: 03/14/2017

---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-embedded"></a>Criar um novo relatório de um conjunto de dados no Power BI Embedded

Os relatórios do Power BI Embedded agora podem ser criados de um conjunto de dados em seu próprio aplicativo. 

O método de autenticação é semelhante ao de inserir relatório. Ele se baseia em tokens de acesso que são específicos para um conjunto de dados. Tokens usados para o PowerBI.com são emitidos pelo AAD (Azure Active Directory) e tokens do Power BI Embedded são emitidos por seu próprio serviço.

Ao criar um relatório inserido, os tokens emitidos são para um conjunto de dados específico. Tokens devem ser associados com a URL de inserção no mesmo elemento para assegurar que cada um tenha um token exclusivo. Para criar um relatório inserido, os escopos *Dataset.Read e Workspace.Report.Create* devem ser fornecidos no token de acesso.

## <a name="create-access-token-needed-to-create-new-report"></a>Criar o token de acesso necessário para criar um novo relatório

O Power BI Embedded usa tokens de inserção, que são Tokens Web JSON assinados por HMAC. Os tokens são assinados com a tecla de acesso da sua coleção de espaço de trabalho do Azure Power BI Embedded. Tokens de inserção, por padrão, são usados para fornecer acesso somente leitura a um relatório a ser inserido em um aplicativo. Tokens de inserção são emitidos para um relatório específico e devem ser associados uma URL de inserção.

Tokens de acesso devem ser criados no servidor conforme as chaves de acesso são usadas para assinar/criptografar os tokens. Para obter informações sobre como criar um token de acesso, consulte [Autenticação e autorização com o Power BI Embedded](power-bi-embedded-app-token-flow.md). Você também pode examinar o método [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_). Aqui está um exemplo de qual seria a aparência disso usando o SDK do .NET para o Power BI.

Neste exemplo, temos nossa ID de conjunto de dados na qual desejamos criar o novo relatório. Também precisamos adicionar escopos para *Dataset.Read e Workspace.Report.Create*.

A *classe PowerBIToken* exige que você instale o [pacote NuGet do Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalar o pacote NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Código C#**

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Criar um novo relatório em branco

Para criar um novo relatório, a configuração de criação deve ser fornecida. Isso deve incluir o token de acesso, o embedURL e o datasetID com base nos quais desejamos criar o relatório. Isso exige que você instale o [pacote JavaScript Power BI](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/) do NuGet. O embedUrl será apenas https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Você pode usar a [Amostra de inserção de relatório JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) para testar a funcionalidade. Ele também fornece exemplos de código para as diferentes operações que estão disponíveis.

**Instalar o pacote NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Código JavaScript**

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
```

Chamar *powerbi.createReport()* fará com que uma tela em branco no modo de edição apareça dentro do elemento *div*.

![](media/power-bi-embedded-create-report-from-dataset/pbi-embedded-create-new-report.png)

## <a name="save-new-reports"></a>Salvar novos relatórios

O relatório não será realmente criado até que você chame a operação **salvar como**. Isso pode ser feito do menu Arquivo ou do JavaScript.

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Um novo relatório é criado somente depois que **salvar como** é chamada. Depois de você salvar, a tela ainda mostrará o conjunto de dados no modo de edição e não no relatório. Você precisará recarregar o novo relatório como faria com qualquer outro relatório.

![](media/power-bi-embedded-create-report-from-dataset/pbi-embedded-save-new-report.png)

## <a name="load-the-new-report"></a>Carregar o novo relatório

Para interagir com o novo relatório você precisa inseri-lo da mesma maneira que o aplicativo insere um relatório normal, ou seja, um novo token deve ser emitido especificamente para o novo relatório e, em seguida, o método de inserção deve ser chamado.

```
<div id="reportContainer"></div>
  
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
```

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Automatizar as ações de salvar e carregar um novo relatório usando o evento "saved"

Para automatizar o processo de "salvar como" e em seguida carregar o novo relatório, você pode fazer uso do evento "saved". Este evento é disparado quando a operação save for concluída e retorna um objeto JSON que contém o novo reportId, o nome do relatório, o antigo reportId (se houver algum) e se a operação foi saveAs ou save.

```
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Para automatizar o processo, você pode ouvir o evento de "saved", usar o novo reportId, crie o novo token e inseri-lo no novo relatório.

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
```

## <a name="see-also"></a>Consulte também

[Introdução a exemplos](power-bi-embedded-get-started-sample.md)  
[Salvar relatórios](power-bi-embedded-save-reports.md)  
[Inserir um relatório](power-bi-embedded-embed-report.md)  
[Autenticando e autorizando com o Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Amostra de inserção de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Pacote NuGet do Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Pacote JavaScript do Power BI](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
Mais perguntas? [Experimentar a comunidade do Power BI](http://community.powerbi.com/)
