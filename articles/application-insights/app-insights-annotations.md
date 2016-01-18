<properties
    pageTitle="Anotações de implantação do Application Insights | Microsoft Azure"
    description="Adicione uma implantação ou compile marcadores para seus gráficos do gerenciador de métricas no Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
	ms.date="01/05/2016"
    ms.author="awills"/>

# Anotações de versão no Application Insights

As anotações de versão nos gráficos do [Metrics Explorer](app-insights-metrics-explorer.md) mostram onde você implantou uma nova compilação. Elas facilitam ver se suas alterações tinham qualquer efeito sobre o desempenho do aplicativo. Elas podem ser criadas automaticamente pelo [sistema de compilação do Visual Studio Team Services](https://www.visualstudio.com/pt-BR/get-started/build/build-your-app-vs).

![Exemplo de anotações com correlação visível com tempo de resposta do servidor](./media/app-insights-annotations/00.png)

As anotações de versão são um recurso de compilação baseado em nuvem e serviço de versão do Visual Studio Team Services.

## Instalar a extensão Annotations (uma vez)

Para poder criar anotações de versão, você precisará instalar uma das muitas extensões de Team Service disponíveis no Visual Studio Marketplace.

1. Entre no seu projeto do [Visual Studio Team Services](https://www.visualstudio.com/pt-BR/get-started/setup/sign-up-for-visual-studio-online).
2. Abra o Visual Studio Marketplace, localize a extensão Application Insights Annotations e adicione-a à sua conta de Team Services.

![Na parte superior direita da página da Web do Team Services, abra o Marketplace. Pesquise e instale o Application Insights Annotations em sua conta.](./media/app-insights-annotations/10.png)

Você só precisa fazer isso uma vez em sua conta de serviços de equipe do Visual Studio. As anotações de versão agora podem ser configuradas para qualquer projeto na sua conta.



## Adicionar uma tarefa de anotação ao seu modelo de versão

Você precisa fazer isso para cada modelo de versão que você deseja criar anotações de versão.

Abra (ou crie) o modelo de versão que gerencia suas implantações do Visual Studio Team Services.

Adicione uma tarefa e selecione a tarefa de anotação de versão do Application Insights do menu.

![Na parte superior direita da página da Web do Team Services, abra o Marketplace. Pesquise e instale o Application Insights Annotations em sua conta.](./media/app-insights-annotations/40.png)

Para concluir esta etapa, você precisará de alguns detalhes do recurso do Application Insights que você pode usar para monitorar seu aplicativo.

Mantenha a janela Team Services aberta enquanto você obtém os detalhes do Application Insights.

## Copie uma chave API do Application Insights

Em uma janela separada do navegador:

1. Entre no [Portal do Microsoft Azure](https://portal.azure.com) e abra o recurso Application Insights que monitora o seu aplicativo. (Ou [crie um agora](app-insights-overview.md), se você ainda não fez isso.)
2. Abra o menu suspenso **Essentials** e copie a Id da assinatura, o grupo de recursos e o nome do recurso para a tarefa de anotação de versão. ![](./media/app-insights-annotations/50.png)
2. Abra **Configurações**, **Chaves de API** e crie uma nova chave. Copie isso. ![](./media/app-insights-annotations/30.png)

Por fim, **Salve** a definição de versão.

## Marcadores de implantação

Agora, sempre que você usar o modelo de versão para implantar uma nova versão, uma anotação será enviada ao Application Insights. As anotações aparecerão em gráficos no Metrics Explorer.

<!---HONumber=AcomDC_0107_2016-->