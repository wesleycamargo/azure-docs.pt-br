---
title: Microsoft Dynamics CRM e Azure Application Insights | Microsoft Docs
description: Obtenha a telemetria do Microsoft Dynamics CRM Online usando o Application Insights. Passo a passo da instalação, obtenção de dados, visualização e exportação.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/16/2018
ms.reviewer: mazhar
ms.author: mbullwin
ms.openlocfilehash: 6119f1116d255f7cd2a2bfc20e86eeca9e5dfe82
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121151"
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Passo a passo: Habilitar a Telemetria do Microsoft Dynamics CRM Online usando Application Insights
Este artigo mostra como obter dados de telemetria no [Microsoft Dynamics CRM Online](https://www.dynamics.com/) usando o [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Percorreremos o processo completo de adição de um script do Application Insights ao seu aplicativo, captura de dados e visualização de dados.

> [!NOTE]
> [Procurar a solução de exemplo](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Adicionar o Application Insights a uma instância nova ou existente do CRM Online
Para monitorar seu aplicativo, você adiciona um SDK do Application Insights a ele. O SDK envia a telemetria para o [Portal do Application Insights](https://portal.azure.com), no qual você pode usar nossas potentes ferramentas de análise e diagnóstico ou exportar os dados para o armazenamento.

### <a name="create-an-application-insights-resource-in-azure"></a>Criar um recurso do Application Insights no Azure
1. Obtenha [uma conta no Microsoft Azure](https://azure.com/pricing). 
2. Entre no [Portal do Azure](https://portal.azure.com) e adicione um novo recurso do Application Insights. Este é o local em que os dados serão processados e exibidos.

    ![Clique em +, Serviços de Desenvolvedor, Application Insights.](./media/sample-mscrm/01.png)

    Escolha ASP.NET como o tipo de aplicativo.
3. Siga as instruções para [obter o script de SDK JavaScript para seu aplicativo](../../azure-monitor/app/javascript.md#set-up-application-insights-for-your-web-page), copie o trecho de JavaScript e substitua a Chave de Instrumentação pelo valor correto para seu recurso do Application Insights.

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Criar um recurso da Web em JavaScript no Microsoft Dynamics CRM
1. Abra sua instância do CRM Online e faça logon com privilégios de administrador.
2. Abra o Microsoft Dynamics CRM Configurações, Personalizações, Personalizar o Sistema

    ![Configurações do Microsoft Dynamics CRM](./media/sample-mscrm/00001.png)

    ![Configurações > Personalizações](./media/sample-mscrm/00002.png)

1. Crie um recurso de JavaScript.

    ![Caixa de diálogo Novo Recurso da Web](./media/sample-mscrm/07.png)

    Atribua um nome, selecione **Script (JScript)** e abra o editor de texto.

    ![Abrir o editor de texto](./media/sample-mscrm/00004.png)
2. Copie o código do SDK JavaScript do Application Insights em que você configurou a Chave de Instrumentação antes. Ao copiar, certifique-se de ignorar marcas de script. Confira a captura de tela abaixo:

    ![Configurar sua chave de instrumentação](./media/sample-mscrm/000005.png)

    O código inclui a chave de instrumentação que identifica seu recurso do Application insights.
3. Salve e publique.

    ![Salvar e publicar](./media/sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Formulários de instrumento
1. No Microsoft CRM Online, abra o formulário Conta

    ![Formulário da Conta](./media/sample-mscrm/00007.png)
2. Abra as propriedades do formulário

    ![Propriedades do formulário](./media/sample-mscrm/00008.png)
3. Adicione o recurso da Web de JavaScript que você criou

    ![Adicionar menu](./media/sample-mscrm/13.png)

4. Salve e publique as personalizações do formulário.

## <a name="metrics-captured"></a>Métricas capturadas
Agora você configurou a captura de telemetria do formulário. Sempre que ele for usado, os dados serão enviados ao recurso do Application Insights.

Aqui estão exemplos dos dados que você verá.

#### <a name="application-health"></a>Integridade do aplicativo
![Tempo de carregamento de página de exemplo](./media/sample-mscrm/15.png)

![Gráfico de exibições de página de exemplo](./media/sample-mscrm/16.png)

Exceções de navegador:

![Gráfico de exceções de navegador](./media/sample-mscrm/17.png)

Clique no gráfico para obter mais detalhes:

![Lista de exceções](./media/sample-mscrm/18.png)

#### <a name="usage"></a>Uso
![Usuários, seções e exibição de página](./media/sample-mscrm/19.png)

![Gráficos de sessão](./media/sample-mscrm/20.png)

![Versões do navegador](./media/sample-mscrm/21.png)

#### <a name="browsers"></a>Navegadores
![Divisão de tempo de carregamento de página](./media/sample-mscrm/22.png)

![Contagem de sessões por versão do navegador](./media/sample-mscrm/23.png)

#### <a name="geolocation"></a>Geolocalização
![Contagem de sessões por país](./media/sample-mscrm/24.png)

![Sessões e usuários por país](./media/sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Solicitação de exibição de página interna
![Resumo de exibição de página](./media/sample-mscrm/26.png)

![Pesquisar em eventos de exibição de página](./media/sample-mscrm/27.png)

![Exibições de páginas semelhantes](./media/sample-mscrm/28.png)

![Propriedades de exibição de página](./media/sample-mscrm/29.png)

![Páginas por sessão](./media/sample-mscrm/30.png)

## <a name="sample-code"></a>Exemplo de código
[Procurar no código de amostra](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Você pode fazer análises ainda mais profundas se [exportar os dados para o Microsoft Power BI](../../azure-monitor/app/export-power-bi.md ).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Exemplo de Solução para o Microsoft Dynamics CRM
[Veja a solução do exemplo implementada no Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Saiba mais
* [O que é o Application Insights?](../../azure-monitor/app/app-insights-overview.md)
* [Application Insights para páginas da Web](../../azure-monitor/app/javascript.md)
* [Mais exemplos e explicações passo a passo](../../azure-monitor/app/app-insights-overview.md)
