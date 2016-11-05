---
title: Anotações de versão para o Application Insights | Microsoft Docs
description: Adicione uma implantação ou compile marcadores para seus gráficos do gerenciador de métricas no Application Insights.
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/28/2016
ms.author: awills

---
# Anotações de versão no Application Insights
As anotações de versão nos gráficos do [Metrics Explorer](app-insights-metrics-explorer.md) mostram onde você implantou uma nova compilação. Elas facilitam ver se suas alterações tinham qualquer efeito sobre o desempenho do aplicativo. Elas podem ser criadas automaticamente pelo [sistema de build do Visual Studio Team Services](https://www.visualstudio.com/pt-BR/get-started/build/build-your-app-vs) e você também pode [criá-las do PowerShell](#create-annotations-from-powershell).

![Exemplo de anotações com correlação visível com tempo de resposta do servidor](./media/app-insights-annotations/00.png)

As anotações de versão são um recurso de compilação baseado em nuvem e serviço de versão do Visual Studio Team Services.

## Instalar a extensão Annotations (uma vez)
Para poder criar anotações de versão, você precisará instalar uma das muitas extensões de Team Service disponíveis no Visual Studio Marketplace.

1. Entre no seu projeto do [Visual Studio Team Services](https://www.visualstudio.com/pt-BR/get-started/setup/sign-up-for-visual-studio-online).
2. No Visual Studio Marketplace, [obtenha a extensão Anotações de Versão](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) e adicione-a à sua conta do Team Services.

![Na parte superior direita da página da Web do Team Services, abra o Marketplace. Selecione Visual Team Services e, em Compilação e Versão, escolha Ver mais.](./media/app-insights-annotations/10.png)

Você só precisa fazer isso uma vez em sua conta de serviços de equipe do Visual Studio. As anotações de versão agora podem ser configuradas para qualquer projeto na sua conta.

## Obter uma chave API do Application Insights
Você precisa fazer isso para cada modelo de versão que você deseja criar anotações de versão.

1. Entre no [Portal do Microsoft Azure](https://portal.azure.com) e abra o recurso Application Insights que monitora seu aplicativo. (Ou [crie um agora](app-insights-overview.md), se você ainda não fez isso.)
2. Abra **Acesso à API** e faça uma cópia da **ID do Application Insights**.
   
    ![Em portal.azure.com, abra o recurso do Application Insights e escolha Configurações. Abra Acesso à API. Copie a ID do Aplicativo](./media/app-insights-annotations/20.png)
3. Em um janela de navegador separada, abra (ou crie) o modelo de versão que gerencia suas implantações do Visual Studio Team Services.
   
    Adicione uma tarefa e selecione a tarefa de anotação de versão do Application Insights do menu.
   
    Cole a **ID do Aplicativo** que você copiou da folha Acesso à API.
   
    ![No Visual Studio Team Services, abra Versão, selecione uma definição de versão e escolha Editar. Clique em Adicionar Tarefa e selecione Anotação de Versão do Application Insights. Cole a ID do Application Insights.](./media/app-insights-annotations/30.png)
4. Defina o campo **APIKey** como uma variável `$(ApiKey)`.
5. De volta à folha de Acesso à API, crie uma nova chave de API e faça uma cópia dela.
   
    ![Na folha de Acesso à API na janela do Azure, clique em Criar Chave de API. Forneça um comentário, marque Gravar anotações e clique em Gerar a Chave. Copie a nova chave.](./media/app-insights-annotations/40.png)
6. Abra a guia Configuração do modelo de versão.
   
    Crie uma definição de variável para `ApiKey`.
   
    Cole sua chave de API para a definição da variável ApiKey.
   
    ![Na janela Team Services, selecione a guia Configuração e clique em Adicionar Variável. Defina o nome para ApiKey e no Valor, cole a chave que você acabou de gerar.](./media/app-insights-annotations/50.png)
7. Por fim, **Salve** a definição da versão.

## Criar anotações do PowerShell
Você também pode criar anotações de qualquer processo que desejar (sem usar o VS Team System).

Obtenha o [script do Powershell do GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

Use-o assim:

    .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }

Obtenha o `applicationId` e um `apiKey` do seu recurso Application Insights: abra Configurações, Acesso à API e copie a ID do aplicativo. Em seguida, clique em Criar chave de API e copie a chave.

## Anotações da versão
Agora, sempre que você usar o modelo de versão para implantar uma nova versão, uma anotação será enviada ao Application Insights. As anotações aparecerão em gráficos no Metrics Explorer.

Clique em qualquer marcador de anotação para abrir os detalhes sobre a versão, incluindo o solicitante, a ramificação de controle do código-fonte, a definição da versão, o ambiente e muito mais.

![Clique em qualquer marcador de anotação de versão.](./media/app-insights-annotations/60.png)

<!---HONumber=AcomDC_0713_2016-->