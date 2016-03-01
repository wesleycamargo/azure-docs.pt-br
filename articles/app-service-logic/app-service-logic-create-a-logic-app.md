<properties
	pageTitle="Criar um aplicativo lógico | Microsoft Azure"
	description="Saiba como criar um Aplicativo Lógico conectando serviços SaaS"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/17/2016"
	ms.author="stepsic"/>

# Criar um novo aplicativo lógico conectando serviços SaaS

| Referência rápida |
| --------------- |
| [Linguagem de definição de aplicativos lógicos](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [Documentação do conector de aplicativos lógicos](https://azure.microsoft.com/documentation/articles/app-service-logic-connectors-list/) |
| [Fórum de aplicativos lógicos](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=azurelogicapps) |

Este tópico demonstra como, em apenas alguns minutos, você pode começar a usar os [Aplicativos Lógicos dos Serviços de Aplicativos](app-service-logic-what-are-logic-apps.md). Vamos percorrer um fluxo de trabalho que permite fornecer um conjunto de tweets em que você está interessado a uma pasta do Dropbox.

Para usar este cenário, você precisará de:

- Uma assinatura do Azure
- Uma conta do Twitter
- Uma conta do Office 365

## Criar um novo aplicativo lógico para enviar tweets por email

1. No painel do portal do Azure, selecione **Marketplace**. 
2. Em Tudo, procure 'aplicativos lógicos' e selecione **Aplicativo Lógico (visualização)**. Você também pode selecionar **Novo**, **Web + Móvel** e selecione **Aplicativo Lógico (visualização)**. 
3. Insira um nome para seu aplicativo lógico, selecione o plano de serviço de aplicativo e selecione **Criar**. Nesta etapa, pressupomos que você tenha um plano de serviço de aplicativo e esteja familiarizado com as propriedades necessárias. Caso contrário, não se preocupe, você pode começar pela [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md). 

4. Quando o aplicativo lógico for aberto pela primeira vez, será necessário um gatilho. Por exemplo, você pode usar a **Recorrência**: ![Recorrência](./media/app-service-logic-create-a-logic-app/recurrence.png)

	Com o uso de Recorrência, seu aplicativo lógico é executado continuamente durante o período escolhido por você. Por exemplo, você pode executar seu aplicativo lógico a cada 30 segundos, a cada 5 horas, todos os dias às 8h PST, toda noite às 2h EST e muito mais. Você também pode excluir a Recorrência do seu aplicativo lógico. Se você excluir a Recorrência, inicie manualmente seu aplicativo lógico.

5. No entanto, para este tutorial, executaremos o gatilho sempre que houver um tweet que corresponda a uma condição. Clique no menu `...` para excluir o gatilho de Recorrência.

6. Procure **twitter** na caixa de pesquisa do gatilho e selecione-o.

7. Agora você digitará a palavra-chave que deseja pesquisar no twitter. ![Pesquisa do Twitter](./media/app-service-logic-create-a-logic-app/twittersearch.png)

5. Selecione o sinal de adição e escolha **Adicionar uma ação** ou **Adicionar uma condição**: ![Plus](./media/app-service-logic-create-a-logic-app/plus.png)
6. Quando você seleciona **Adicionar uma ação**, todos os conectores com suas ações disponíveis são listados. Então, você pode escolher qual conector e qual ação será adicionada ao aplicativo lógico. Por exemplo, você pode selecionar **Office 365 - Enviar Email** e mais ações do Office 365: ![Ações](./media/app-service-logic-create-a-logic-app/actions.png)

7. Agora você precisa preencher os parâmetros para o email desejado: ![Parâmetros](./media/app-service-logic-create-a-logic-app/parameters.png)

8. Por fim, você pode selecionar **Salvar** para ativar seu aplicativo lógico.

## Gerenciar seu aplicativo lógico após a criação

Agora seu aplicativo lógico está em execução. Sempre que o fluxo de trabalho agendado é executado, ele procura tweets com a hashtag específica. Quando encontra uma tweet correspondente, ele o coloca no seu Dropbox. Por fim, você verá como desabilitar o aplicativo ou como está seu desempenho.

1. Clique em **Procurar** no lado esquerdo da tela e selecione **Aplicativos Lógicos**.

2. Clique no novo aplicativo lógico que você acabou de criar para ver o status atual e as informações gerais.

3. Para editar seu novo aplicativo lógico, clique em **Gatilhos e Ações**.

5. Para desabilitar o aplicativo, clique em **Desabilitar** na barra de comandos.

Em menos de 5 minutos, você configurou um aplicativo lógico simples e o colocou em execução na nuvem. Para saber mais sobre como usar os recursos de aplicativos lógicos, consulte [Usar os recursos de aplicativos lógicos]. Para saber mais sobre as definições de aplicativos lógicos, consulte [Criar definições de aplicativos lógicos](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Usar os recursos de aplicativos lógicos]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0224_2016-->