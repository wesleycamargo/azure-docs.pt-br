<properties 
	pageTitle="Monitorar seus Aplicativos Lógicos no Serviço de Aplicativo do Azure | Microsoft Azure" 
	description="Como ver o que os Aplicativos Lógicos fizeram" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="stepsic"/>

# Monitorar seus aplicativos lógicos

Depois de criar um aplicativo lógico (etapas descritas em [Criar um aplicativo lógico](app-service-logic-create-a-logic-app.md)), você pode ver o histórico completo de sua execução no portal do Azure. Para exibir o histórico, selecione **Procurar**, **Web + Móvel** e **Aplicativos Lógicos**. É exibida uma lista com todos os Aplicativos Lógicos em sua assinatura. Um aplicativo lógico pode estar **Habilitado** ou **Desabilitado**. Aplicativos Lógicos **Habilitados** significam que os gatilhos executam-no em resposta a eventos de gatilho. Um Aplicativo Lógico **Desabilitado** não é executado em resposta a eventos.

![Visão geral](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Quando a folha para seu aplicativo lógico aparecer, há duas seções úteis:

- **Resumo**, que informa o status mais recente e é um ponto de entrada para editor seu aplicativo lógico.
- **Todas as execuções**, que mostra uma lista das execuções que esse aplicativo lógico teve.

## Exibir as execuções do aplicativo

![Todas as execuções](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

Essa lista de execuções mostra a **Hora de início**, o **Identificador de execução** (você pode usar isso ao chamar a API REST) e a **Duração** das execuções específicas. Clique em qualquer linha para ver os detalhes da execução.

A folha de detalhes mostra um gráfico com o tempo de execução e a sequência de todas as ações na execução. Abaixo está uma lista completa de todas as ações foram executadas:

![Execução e ações](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

Finalmente, em uma ação específica, você pode obter todos os dados que foram enviados à ação e que foram recebidos da ação nas seções **Entradas** e **Saídas**. Selecione os links para ver todo o conteúdo (você também pode copiar os links para baixar o conteúdo).

Outra informação importante é a **ID de Acompanhamento**. Esse identificador é transmitido nos cabeçalhos de todas as chamadas de ação. Se você tiver o registro em log dentro do seu próprio serviço, fazer registrar a ID de Acompanhamento e então fazer referência cruzada de seus próprios logs com esse identificador.

## Exibir o histórico de gatilho 

Gatilhos de sondagem verificam uma API em um intervalo de sondagem, mas não necessariamente iniciam uma execução, dependendo da resposta (por exemplo, um `200` significa executar e um `202` significa não executar). O histórico de gatilho permite ver todas as chamadas que aconteceram, mas que não executam o Aplicativo Lógico (as respostas `202`).

![Histórico de gatilho](./media/app-service-logic-monitor-your-logic-apps/triggerhistory.png)

Para cada gatilho, você pode ver se ele foi **disparado** ou não, ou se houve algum tipo de erro (**Falha**). Para inspecionar o motivo da falha do gatilho, você pode clicar no link **Saídas**. Se ele disparou, selecione o link **Executar** para ver o que aconteceu após o disparo.

Observe que, para gatilhos de *Push*, você *não* vê as horas de início das execuções. Em vez disso, você vê as chamadas de *registro de retorno de chamada*, que são o registro do Aplicativo Lógico para receber o retorno de chamada. Se o gatilho de push não estiver funcionando, pode ser que haja um problema com o registro (que você poderá ver nas saídas), mas, caso contrário, talvez seja necessário investigar especificamente essa API.

## Habilitar o controle de versão

Há uma funcionalidade adicional que atualmente não está disponível na interface do usuário (em breve), mas está na [API REST](http://go.microsoft.com/fwlink/p/?LinkID=525617&clcid=0x409). Quando você atualiza a definição de um aplicativo lógico, a versão anterior da definição é armazenada. Isso ocorre porque, se você já tiver uma execução em andamento, ela fará referência à versão do Aplicativo Lógico que existia quando a execução teve início. Definições de execuções não podem mudar enquanto estiverem em andamento. O histórico de versão de API REST permite o acesso a essas informações.
 

<!---HONumber=AcomDC_1210_2015-->