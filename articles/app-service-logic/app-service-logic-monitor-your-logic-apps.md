<properties 
	pageTitle="Monitorar seus aplicativos lógicos" 
	description="Como ver o que o aplicativo lógico fez." 
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
	ms.date="03/10/2015"
	ms.author="stepsic"/>

#Monitorar seus aplicativos lógicos

Depois que você criou um aplicativo lógica seguindo as etapas descritas em [Criado um aplicativo de lógica](app-service-logic-create-a-logic-app.md), você pode ver o histórico completo de sua execução no portal do Azure. Para exibir o histórico, clique em **Procurar** no lado esquerdo da tela do portal e selecione **Aplicativos Lógicos**. Será exibida uma lista de aplicativos lógicos na sua assinatura. Um aplicativo lógico pode estar **Habilitado** ou **Desabilitado**. Ter os aplicativos lógicos **Habilitados** significa que gatilhos serão executados no aplicativo lógico em resposta a eventos de gatilho; aplicativos lógicos **Desabilitados** não serão executados em resposta a eventos.

![Visão geral](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Quando a folha para seu aplicativo lógico aparecer, há duas seções úteis:

- **Resumo**, que informa o status mais recente e é um ponto de entrada para editor seu aplicativo lógico.
- **Todas as execuções**, que mostra uma lista das execuções que esse aplicativo lógico teve.

##Execuções

![Todas as execuções](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

Esta lista de execuções mostra a **Hora de início**, o **Identificador de execução** (você pode usar isso ao chamar a API REST) e a **Duração** das execuções específicas. Clique em qualquer linha para ver detalhes dessa execução.

A folha de detalhes mostra um gráfico com o tempo de execução e a sequência de todas as ações na execução. Abaixo, está uma lista completa de todas as ações foram executadas.

![Execução e ações](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

Finalmente, em uma ação específica, você pode obter todos os dados que foram enviados à ação e que foram recebidos da ação nas seções **Entradas** e **Saídas**.

Outra informação importante é a **ID de Acompanhamento**. Esse identificador é transmitido nos cabeçalhos de todas as chamadas de ação. Se você tiver o registro em log dentro do seu próprio serviço, fazer registrar a ID de Acompanhamento e então fazer referência cruzada de seus próprios logs com esse identificador.

##Histórico de gatilho e controle de versão

Há dois recursos adicionais que não são possíveis no momento na interface do usuário (em breve), mas estão disponíveis por meio da [API REST](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409).

1. **Histórico de gatilho** - gatilhos de sondagem verificam a API em um intervalo de sondagem, mas não necessariamente iniciam uma execução, dependendo da resposta (por exemplo um `200` significa executar e um `202` significa que não executar). O histórico de gatilho fornece uma maneira de ver todas as chamadas aconteceram, mas que não executam o aplicativo lógico (a respostas `202`).

2. **Versões anteriores** - quando você atualizar a definição de um aplicativo lógico, a versão anterior da definição é armazenada. Isso ocorre porque, se você já tiver uma execução em andamento, essa execução fará referência à versão do aplicativo lógico que existia quando a execução teve início. Definições de execuções não podem mudar enquanto estiverem em andamento. O histórico de versão de API REST fornece acesso a essas informações.

<!--HONumber=54--> 