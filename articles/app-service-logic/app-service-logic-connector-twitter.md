<properties
   pageTitle="Usando o conector do Twitter no Serviço de Aplicativo do Microsoft Azure"
   description="Como usar o aplicativo de API de conector do Twitter"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/14/2015"
   ms.author="sameerch"/>


# Conector do Twitter

Conecte-se ao seu feed do Twitter para publicar e receber tweets de sua linha do tempo, da linha do tempo de seus amigos e dos seguidores de sua conta do Twitter. Conectores podem ser usados em Aplicativos de Lógicos para obter, processar ou enviar dados como parte de um “fluxo de trabalho”. Utilizando o Conector do Twitter em seu fluxo de trabalho, você pode chegar a diversos resultados. Por exemplo, você pode:

- Obter novos tweets associados a uma palavra-chave ou texto específico. Quando um novo tweet é recuperado, ele dispara uma nova instância do fluxo de trabalho e passa os dados para o próximo conector no fluxo de trabalho. Por exemplo: você cria um conector do Twitter e usa o disparador de novo tweet da pesquisa para monitorar #peanutbutterandjelly. Sempre que houver um novo tweet com #peanutbutterandjelly, o fluxo de trabalho (também conhecido como aplicativo lógico) será iniciado automaticamente.
- Usando ações diferentes, como "Pesquisar Tweets", você obtém a resposta e a usa em seu fluxo de trabalho. Por exemplo: você pode pesquisar tweets com o nome da sua empresa. Quando eles são encontrados, você pode usar um aplicativo lógico para gravar esses dados em um banco de dados do SQL Server. Em seguida, use os dados do SQL Server para determinar o que está sendo tweetado sobre a sua empresa. 


## Gatilhos e Ações
*Gatilhos* são eventos que ocorrem. Por exemplo: um novo tweet pode disparar ou iniciar um processo ou um fluxo de trabalho. Uma *ação* é o resultado de algo. Por exemplo: você pode pesquisar um tweet específico e, quando localizá-lo, enviar um email ou atualizar um banco de dados.

O conector do Twitter pode ser usado como um gatilho ou uma ação em um aplicativo lógico e dá suporte a dados nos formatos JSON e XML.

O conector do Twitter tem os seguintes gatilhos e ações disponíveis:

Gatilhos | Ações
--- | ---
Novo tweet da pesquisa | <ul><li>Obter a linha do tempo do usuário</li><li>Pesquisar Tweets</li><li>Tweetar</li><li>Obter linha do tempo de menções</li><li>Obter linha do tempo inicial</li><li>Obter seguidores</li><li>Obter amigos</li><li>Obter detalhes do usuário</li><li>Tweetar para usuário</li><li>Enviar mensagem direta</li></ul>

> [AZURE.IMPORTANT]O gatilho **Novo tweet** foi arquivado. Atualmente, ele ainda está disponível e pode ser usado como uma operação avançada. A ação **Retweet** foi removida e não tem mais suporte. Se você usar a ação Retweet, ela falhará em tempo de execução. Como resultado, remova a ação Retweet dos aplicativos lógicos.


## Criar o conector do Twitter
Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Pesquise "Conector do Twitter".
3. Digite o nome, o plano do Serviço de Aplicativo e outras propriedades:

	![][1]
4.	Clique em **Criar**.


## Usando o Conector do Twitter em seu aplicativo lógico
Após a criação do aplicativo de API, você poderá usar o conector do Twitter como ação ou gatilho para seus aplicativos lógicos. Para fazer isso:

1.	Crie um novo aplicativo lógico ou abra um aplicativo lógico existente:

	![][2]
2.	Abra **Gatilhos e ações** para abrir o designer de aplicativos lógicos:

	![][3]
3.	O conector do Twitter está listado no lado direito. Selecione-o para adicioná-lo automaticamente ao aplicativo lógico:

	![][4]
4.	Selecione **Autorizar **, insira suas credenciais do Twitter e selecione **Autorizar aplicativo**:

	![][5]


Agora você pode configurar o conector do Twitter para compilar seu fluxo de trabalho. Você pode usar os tweets recuperados do gatilho do Twitter em outras ações no fluxo.

![][6]

De maneira semelhante, você pode usar as ações do Twitter no fluxo de trabalho. Selecione uma ação do Twitter e configure as entradas para a ação:

![][7] ![][8]

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Crie aplicativos de API usando APIs REST. Consulte [Referência a aplicativos de API e conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/triggers.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/actions.png

<!---HONumber=July15_HO5-->