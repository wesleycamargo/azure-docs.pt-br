<properties 
	pageTitle="Criar um novo recurso do Application Insights" 
	description="Configurar um novo aplicativo e obter uma nova chave de instrumentação. O Application Insights monitora o desempenho e o uso de aplicativos em tempo real." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="awills"/>

# Criar um novo recurso do Application Insights



[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

O Visual Studio Application Insights exibe dados sobre seu aplicativo no *recurso* do Microsoft Azure. Por isso, criar um novo recurso faz parte da [configuração do Application Insights para monitorar um novo aplicativo][start]. Em muitos casos, isso pode ser feito automaticamente pelo IDE e que é a maneira recomendada, se disponível. Mas, em alguns casos, você pode criar um recurso manualmente.

Depois de criar o recurso, você pode obter sua chave de instrumentação e usá-la para configurar o SDK no aplicativo. Isso envia a telemetria para o recurso.

## Inscreva-se no Microsoft Azure

Se você ainda não tiver uma [conta da Microsoft, obtenha uma agora mesmo](http://live.com). (Se usar serviços como Outlook.com, OneDrive, Windows Phone ou XBox Live, você já tem uma conta da Microsoft.)

Primeiro, você precisa de uma assinatura do [Microsoft Azure](http://azure.com). Se sua equipe ou organização tem uma assinatura do Azure, o proprietário pode adicioná-lo a ela usando seu Windows Live ID.

Ou então você pode criar uma nova assinatura. A avaliação gratuita permite que experimentar tudo no Azure. Depois que o período de avaliação expira, você pode achar a assinatura pré-paga apropriada, já que você não será cobrado pelos serviços gratuitos.

Quando você tem acesso a uma assinatura, entre no Application Insights em [http://portal.azure.com](http://portal.azure.com) e use sua Live ID para fazer logon.


## Criar um recurso do Application Insights
  

No [portal.azure.com](https://portal.azure.com), adicione um recurso do Application Insights:

![Clique em Novo, Application Insights](./media/app-insights-create-new-resource/01-new.png)


* O **tipo de aplicativo** afeta o que você vê na folha de visão geral e as propriedades disponíveis no [explorador de métricas][metrics]. Se você não vir o tipo de aplicativo, escolha um dos tipos da Web para páginas da Web e um dos tipos de telefone para outros dispositivos.
* **Grupo de recursos** é uma conveniência para o gerenciamento de propriedades, como controle de acesso. Se você já criou outros recursos do Azure, poderá optar por colocar esse novo recurso no mesmo grupo.
* **Assinatura** é a sua conta de pagamento no Azure.
* **Local** é onde podemos manter seus dados. Atualmente ele não pode ser alterado.
* **Adicionar ao quadro inicial** coloca um bloco de acesso rápido para o recurso em sua página inicial do Azure. Recomendável.

Quando seu aplicativo tiver sido criado, uma nova folha será aberta. Nela você verá os dados de uso e de desempenho do seu aplicativo.

Para retornar a ele na próxima vez que fizer logon no Azure, procure pelo bloco de início rápido do aplicativo na Tela Inicial. Ou então, clique em Procurar para localizá-lo.


## Copie a chave de instrumentação.


Você precisará dela em breve para direcionar os dados do SDK em seu aplicativo para o recurso que você acabou de criar.

![Clique em Propriedades, selecione a chave e pressione ctrl + C](./media/app-insights-create-new-resource/02-props.png)

## Configurar seu SDK

Use a chave de instrumentação para configurar o [SDK instalado no seu aplicativo][start].

Esta etapa depende muito do tipo de aplicativo com que você está trabalhando.

Em alguns casos, você deve instalar os módulos padrão que enviar telemetria sem precisar escrever nenhum código. Em todos os casos, você pode [usar a API][api] para enviar sua própria telemetria.

## <a name="monitor"></a>Consultar os dados de telemetria

Feche a folha de início rápido para retornar para a folha de aplicativo no Portal do Azure.

Clique no bloco de Pesquisa para ver a [Pesquisa de Diagnóstico][diagnostic], no qual os primeiros eventos serão exibidos.

Se você estiver esperando mais dados, clique em Atualizar depois de alguns segundos.

## Criando um recurso automaticamente

Você pode escrever um [script do PowerShell](app-insights-powershell-script-create-resource.md) para criar um recurso automaticamente.


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-get-started.md

 

<!---HONumber=62-->