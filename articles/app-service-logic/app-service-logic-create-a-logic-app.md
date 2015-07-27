<properties
	pageTitle="Criar um aplicativo lógico"
	description="Introdução à criação de um aplicativo lógico básico"
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
	ms.topic="hero-article"
	ms.date="05/23/2015"
	ms.author="stepsic"/>

#Criar um novo aplicativo lógico
Este tópico demonstra como, em apenas alguns minutos, você pode começar a usar os [Aplicativos Lógicos dos Serviços de Aplicativos](app-service-logic-what-are-logic-apps.md). Vamos percorrer um fluxo de trabalho que permite fornecer um conjunto de tweets em que você está interessado a uma pasta do Dropbox.

Para usar esse cenário, você precisará de:

- Uma assinatura do Azure
- Uma conta do Twitter
- Uma conta do Dropbox

<!--- TODO: Add try it now information here -->

##Obtendo seus conectores

Primeiro, você precisa criar os dois conectores que usará: [**Conector do Dropbox**](app-service-logic-connector-dropbox.md) e [**Conector do Twitter**](app-service-logic-connector-twitter.md). Para criá-los:

0. Entre no Portal do Azure.

1. Clique em [**Marketplace** ](https://portal.azure.com/#blade/HubsExtension/GalleryFeaturedMenuItemBlade/selectedMenuItemId/apiapps) na tela inicial e pesquise pelo **Twitter** (ou [clique aqui](https://portal.azure.com/#create/microsoft_com.TwitterConnector.0.2.2)).

2. Selecione o Conector do Twitter e clique no botão Criar. Você obterá uma exibição com todas as suas configurações. Você pode deixar o nome como **Conector do Twitter**.

3. Digite um nome do plano em **Criar novo plano de serviço de aplicativo**.

	>[AZURE.NOTE]As etapas nesta seção pressupõem que você esteja criando um novo plano de serviço de aplicativo. Se estiver usando um plano de serviço de aplicativo existente, clique em **Selecionar Existente**, selecione o plano existente e vá para a última etapa desta seção. Você precisa de um plano para hospedar todos os seus aplicativos.

4.  Selecione uma **Camada de Preços** para seu novo plano.

	>[AZURE.NOTE]Por padrão, somente planos recomendados para aplicativos lógicos serão exibidos. Clique em **Exibir Tudo** para ver todos os planos disponíveis. Quando executa um aplicativo lógico na camada Gratuito, você pode executar apenas de hora em hora e usar até 1.000 ações por mês.

5. Crie um **Grupo de recursos** para o fluxo.

	Grupos de recursos atuam como contêineres para seus aplicativos. Todos os recursos para seu aplicativo ficarão no mesmo grupo de recursos.

6. Se tiver mais de uma assinatura do Azure, escolha aquela que você usará.

7. Escolha o **Local** onde o aplicativo lógico será executado.

	![Criar exibição de aplicativo de API](./media/app-service-logic-create-a-logic-app/gallery.png)

8. Clique em **Criar**. A etapa de provisionamento pode levar um ou dois minutos.

9. Agora, repita o processo com o [Dropbox](https://portal.azure.com/#create/microsoft_com.DropboxConnector.0.2.2).

##Iniciando o aplicativo lógico

Agora, você precisa criar um novo aplicativo lógico:

1. Clique no botão **+Novo** na parte inferior esquerda da tela, expanda **Web + Móvel** e clique em **Aplicativo Lógico**.

 	Isso mostra a exibição Criar aplicativo lógico, em que você fornece algumas configurações básicas para começar.

	![Criar exibição de aplicativo lógico](./media/app-service-logic-create-a-logic-app/createlogicapp.png)

2. Em **Nome**, digite um nome significativo para seu aplicativo lógico.

3. Escolha o **Plano de serviço de aplicativo** que você usou ao criar os conectores. Isso deve escolher o local, a assinatura e o grupo de recursos automaticamente para você.

Isso cuida das configurações básicas, mas não clique em **Criar** ainda. Em seguida, você adicionará gatilhos e ações ao fluxo de trabalho.

## Adicionando um gatilho

São os gatilhos que fazem seu aplicativo lógico ser executado. Em seguida, você adicionará um gatilho de recorrência, que inicia o fluxo de trabalho em um cronograma predefinido.

1. Ainda na exibição **Criar aplicativo lógico**, clique em **Gatilhos e ações**.

	Isso exibirá um designer de tela cheia que exibe seu fluxo. Ao lado direito, há uma lista de todos os serviços que podem ter gatilhos.

2. Na seção superior, clique em **Recorrência**.

	Isso adiciona uma caixa onde você pode especificar as configurações de recorrência.

	![Recorrência](./media/app-service-logic-create-a-logic-app/recurrence.png)


4.  Escolha uma **Frequência** e um **Intervalo** de recorrência (como a cada 1 hora) e clique na marca de seleção verde.

Agora, você adicionará uma ação ao fluxo.

## Adicionando uma ação do Twitter

As ações são o que seu fluxo de trabalho faz. Você pode ter qualquer quantidade de ações e organizá-las para que as informações de uma ação sejam passadas para a ação seguinte.

1. No painel direito, localize o **Conector do Twitter** e clique nele.


2. Após ele ser carregado, clique no botão **Autorizar**, entre sua conta do Twitter e clique em **Autorizar aplicativo**.

	Isso concede ao conector acesso à sua conta do Twitter. Uma lista de possíveis operações fornecidas pelo conector do Twitter é exibida.

	![Ações](./media/app-service-logic-create-a-logic-app/actions.png)

3. Clique em **Pesquisar tweets**, e em **Especificar uma consulta**, digite algo como `#MicrosoftAzure` e clique na marca de seleção verde.

	![Pesquisa do Twitter](./media/app-service-logic-create-a-logic-app/twittersearch.png)

O Conector do Twitter agora faz parte do fluxo de trabalho.

## Adicionar uma ação do Dropbox e criar o aplicativo

A etapa final é adicionar uma ação que carrega um tweet em um arquivo do Dropbox.

1. No painel direito, clique em **Conector do Dropbox**.

2. Após o provisionamento ser concluído, clique no botão **Autorizar**, entre em sua conta do Dropbox e clique em **Permitir**.

	![Autorizar o conector do Dropbox](./media/app-service-logic-create-a-logic-app/authorize.png)

	Isso concede ao conector acesso à sua conta do Dropbox. Uma lista de possíveis operações fornecidas pelo conector do Dropbox é exibida.

4. Clique em **Carregar arquivo**.

	Isso exibe as configurações de Conector do Dropbox, que devem ser definidas para transmitir os dados da pesquisa do Twitter ao Dropbox.

	![Dropbox](./media/app-service-logic-create-a-logic-app/dropbox.png)

3. No campo **FilePath**, digite `/tweet.txt`

4. No campo **Conteúdo**, clique no botão `...` e na opção **Texto do tweet**.

	Isso insere o valor `@first(body('twitterconnector')).TweetText` na caixa de texto. Esse valor gerado contém as seguintes partes:

	Parte de conteúdo | Descrição
	------------------------------------------ | ------------
	 `@` | Indica que você está inserindo uma função, e não um valor real.
	`actions('twitterconnector').outputs.body` | Obtém os tweets retornados pela consulta do Conector do Twitter.
	`first()` | A ação pesquisar tweets retorna uma lista, mas você deseja carregar apenas um arquivo
	`.TweetText` | Seleciona a propriedade de mensagem do tweet.

5. Clique na marca de seleção verde para salvar as configurações do conector.

5. Agora que o design está pronto, clique em **Exibição de código** na parte superior esquerda do designer e observe que este é o código JSON que define o fluxo de trabalho você acabou de criar no designer. Falaremos mais sobre este código no [próximo tópico][Use logic app features].

6. Clique no botão **OK** na parte inferior da tela e clique no botão **Criar**.

	Isso cria um novo aplicativo lógico.

## Gerenciando seu aplicativo lógico após a criação

Agora seu aplicativo lógico está em execução. Sempre que o fluxo de trabalho agendado é executado, ele procura tweets com a hashtag específica. Quando encontra uma tweet correspondente, ele o coloca no seu Dropbox. Por fim, você verá como desabilitar o aplicativo ou como está seu desempenho.

1. Clique em **Navegar** no lado esquerdo da tela e selecione **Aplicativos Lógicos**.

2. Clique no novo aplicativo lógico que você acabou de criar para ver o status atual e as informações gerais.

3. Para editar seu novo aplicativo lógico, clique em **Gatilhos e Ações**.

5. Para desabilitar o aplicativo, clique em **Desabilitar** na barra de comandos.

Em menos de 5 minutos, você configurou um aplicativo lógico simples e o colocou em execução na nuvem. Para saber mais sobre como usar os recursos de aplicativos lógicos, consulte [Usar os recursos de aplicativos lógicos]. Para saber mais sobre as definições de aplicativos lógicos, consulte [Criar definições de aplicativos lógicos](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Use logic app features]: app-service-logic-use-logic-app-features.md
[Usar os recursos de aplicativos lógicos]: app-service-logic-use-logic-app-features.md
 

<!---HONumber=July15_HO3-->