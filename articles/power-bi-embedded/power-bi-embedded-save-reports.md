---
title: "Salvar relatórios no Azure Power BI Embedded | Microsoft Docs"
description: "Saiba como salvar relatórios no Power BI Embedded. Isso requer as permissões apropriadas para trabalhar com êxito."
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
ms.openlocfilehash: ad895004cc2972f2ded81566186325a16d401151
ms.lasthandoff: 03/14/2017

---
# <a name="save-reports-in-power-bi-embedded"></a>Salvar relatórios no Power BI Embedded

Saiba como salvar relatórios no Power BI Embedded. Isso requer as permissões apropriadas para trabalhar com êxito.

No Power BI Embedded, você pode editar os relatórios existentes e salvá-los. Você também pode criar um novo relatório e salvar como um novo relatório para criar um.

Para salvar um relatório, você precisa primeiro criar um token para o relatório específico com os escopos certos:

* Para habilitar salvar, o escopo Report.ReadWrite é necessário
* Para habilitar salvar como, os escopos Report.Read e Workspace.Report.Copy são necessários
* Para habilitar as ações salvar e salvar como, os escopos Report.ReadWrite e Workspace.Report.Copy são necessários

Para habilitar os botões de salvar/salvar como certos no menu Arquivo, respectivamente, você precisa fornecer a permissão certa na configuração ao inserir o relatório:

* models.Permissions.ReadWrite
* models.Permissions.Copy
* models.Permissions.All

> [!NOTE]
> O token de acesso também precisa dos escopos apropriados. Para obter mais informações, consulte [Escopos](power-bi-embedded-app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Inserir o relatório no modo de edição

Digamos que você deseja inserir um relatório em modo de edição dentro de seu aplicativo, portanto, basta passar as propriedades certas na configuração de inserção e chamar powerbi.embed(). Você precisará fornecer permissões e um viewMode para ver os botões salvar e salvar como em modo de edição. Para saber mais, confira [Detalhes de configuração do servidor de inserção](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Por exemplo, no JavaScript:

```
   <div id="reportContainer"></div>

    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
```

Agora, um relatório será inserido em seu aplicativo no modo de edição.

## <a name="save-report"></a>Salvar relatório

Depois de inserir o relatório no modo de edição com o token e permissões certos, você pode salvar o relatório do menu Arquivo ou do JavaScript:

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Salvar como

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
> Um novo relatório é criado somente depois do uso de *salvar como*. Depois de salvar, a tela ainda mostra o antigo relatório no modo de edição e não o novo relatório. Você precisará inserir o novo relatório que foi criado. Isso requer um novo token de acesso, já que eles são criados por relatório.

Em seguida, você precisará carregar o novo relatório após um *salvar como*. Isso é semelhante à inserção de qualquer relatório.

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

## <a name="see-also"></a>Consulte também

[Introdução a exemplos](power-bi-embedded-get-started-sample.md)  
[Inserir um relatório](power-bi-embedded-embed-report.md)  
[Criar um novo relatório de um conjunto de dados](power-bi-embedded-create-report-from-dataset.md)  
[Autenticando e autorizando com o Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Amostra de inserção de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
Mais perguntas? [Experimentar a comunidade do Power BI](http://community.powerbi.com/)


