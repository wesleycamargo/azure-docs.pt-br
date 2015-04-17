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
	ms.topic="article"
	ms.date="03/20/2015"
	ms.author="stepsic"/>

#Criar um novo aplicativo lógico
Este tópico demonstra como, em apenas alguns minutos, você pode começar a usar os Aplicativos Lógicos dos Serviços de Aplicativos.  Vamos percorrer um fluxo de trabalho que permite fornecer um conjunto de tweets em que você está interessado a uma pasta do Dropbox.

Para usar esse cenário, você precisará de:

- Uma assinatura do Azure
- Uma conta do Twitter
- Uma conta do Dropbox

<!--- TODO: Add try it now information here -->

##Obtendo seus conectores

Primeiro, você precisa criar os dois conectores que usará:  **O Conector do Dropbox** e o **Conector do Twitter**.  Para criá-los:

1. Clique em **Marketplace** na tela inicial e pesquise pelo **Twitter**. 

2. Selecione o Conector do Twitter e clique no botão Criar.  Você obterá uma lâmina com todas as suas configurações.  Você pode deixar o nome como **Conector do Twitter**.

3. Digite um nome do plano em **Criar novo plano de serviço de aplicativo**.
	
	>[AZURE.NOTE]As etapas nesta seção pressupõem que você esteja criando um novo plano de serviço de aplicativo.  Se estiver usando um plano de serviço de aplicativo existente, clique em **Selecionar Existente**, selecione o plano existente e vá para a próxima seção.
 
4.  Selecione uma **Camada de Preços** para seu novo plano.
 
	>[AZURE.NOTE]Por padrão, somente planos recomendados para aplicativos lógicos serão exibidos.  Clique em **Exibir todos** para ver todos os planos disponíveis.  Quando executa um aplicativo lógico na camada Gratuito, você pode executar apenas de hora em hora e usar até 1000 ações por mês.

5. Crie um **Grupo de Recursos** para o fluxo. 

	Grupos de recursos atuam como contêineres para seus aplicativos.  Todos os recursos para seu aplicativo ficarão no mesmo grupo de recursos.

6. Se tiver mais de uma assinatura do Azure, escolha aquela que você usará.

7. Escolha o **Local** onde o aplicativo lógico será executado.

	![Create API app blade](./media/app-service-logic-create-a-logic-app/gallery.png)

8. Clique em **Criar**.  A etapa de provisionamento pode levar um ou dois minutos. 

9. Agora, repita o processo com o Dropbox.

##Iniciando o aplicativo lógico

Agora, você precisa criar um novo aplicativo lógico:

1. Clique no botão **+Novo** na parte inferior esquerda da tela, expanda **Web + Móvel** e clique em **Aplicativo Lógico**. 

 	Isso exibe a lâmina Criar aplicativo lógico, em que você fornece algumas configurações básicas para começar.

	![Create logic app blade](./media/app-service-logic-create-a-logic-app/createlogicapp.png)
	
2. Em **Nome**, digite um nome significativo para seu aplicativo lógico.

3. Escolha o **Plano de serviço de aplicativo** que você usou ao criar os conectores.  Isso deve escolher o local, a assinatura e o grupo de recursos automaticamente para você.

Isso cuida das configurações básicas, mas não clique em **Criar** ainda.  Em seguida, você adicionará gatilhos e ações ao fluxo de trabalho.

## Adicionando um gatilho

Gatilhos são o que faz seu aplicativo lógico ser executado.  Em seguida, você adicionará um gatilho de recorrência, que inicia o fluxo de trabalho em um cronograma predefinido.

1. Ainda na lâmina **Criar aplicativo lógico**, clique em **Gatilhos e ações**. 

	Isso exibirá um designer de tela cheia que exibe seu fluxo.  Ao lado direito, há uma lista de todos os serviços que podem ter gatilhos. 

2. Na seção **Interno**, clique em **Recorrência**.
	
	Isso adiciona uma caixa onde você pode especificar as configurações de recorrência.

	![Recurrence](./media/app-service-logic-create-a-logic-app/recurrence.png)


4.  Escolha uma **Frequência** e um **Intervalo** de recorrência (como a cada 1 hora) e clique na marca de seleção verde.

Agora, você adicionará uma ação ao fluxo.

## Adicionando uma ação do Twitter

As ações são o que seu fluxo de trabalho faz.  Você pode ter qualquer quantidade de ações e pode organizá-los para que as informações de uma ação sejam passadas para a ação seguinte.

1. No painel direito, localize o **Conector do Twitter** e clique nele. 


2. Após ele ser carregado, clique no botão **Autorizar**, entre sua conta do Twitter e clique em **Autorizar aplicativo**. 

	Isso concede ao conector acesso à sua conta do Twitter.  Uma lista de possíveis operações fornecidas pelo conector do Twitter é exibida. 

	![Actions](./media/app-service-logic-create-a-logic-app/actions.png)

3. Clique em **Pesquisar tweets**, em **Especificar uma consulta**, digite algo como `#MicrosoftAzure` e clique na marca de seleção verde.

	![Twitter search](./media/app-service-logic-create-a-logic-app/twittersearch.png)

O Conector do Twitter agora faz parte do fluxo de trabalho.

## Adicionar uma ação do Dropbox e criar o aplicativo

A etapa final é adicionar uma ação que carrega um tweet em um arquivo do Dropbox. 

1. No painel direito, clique em **Conector do Dropbox**. 
  
2. Após o provisionamento ser concluído, clique no botão **Autorizar**, entre em sua conta do Dropbox e clique em **Permitir**.

	![Authorize Dropbox connector](./media/app-service-logic-create-a-logic-app/authorize.png)
	
	Isso concede ao conector acesso à sua conta do Dropbox.  Uma lista de possíveis operações fornecidas pelo conector do Dropbox é exibida. 
 
4. Clique em **Carregar arquivo**.  

	Isso exibe as configurações de Conector do Dropbox, que devem ser definidas para transmitir os dados da pesquisa do Twitter ao Dropbox.

	![Dropbox](./media/app-service-logic-create-a-logic-app/dropbox.png)

3. No campo **FilePath**, digite `/tweet.txt`
  
4. No campo **Conteúdo**, clique no botão `...` e na opção **Texto do tweet**. 
 
	Isso insere o valor `@first(body('twitterconnector')).TweetText` na caixa de texto.  Esse valor gerado contém as seguintes partes:

	Parte de conteúdo                               | Descrição
	------------------------------------------ | ------------
	 `@`                                       | Indica que você está inserindo uma função, e não um valor real.
	`actions('twitterconnector').outputs.body` | Obtém os tweets que foram retornados pela consulta do Conector do Twitter.
	`first()`                                  | A ação pesquisar tweets retorna uma lista, mas você deseja carregar apenas um arquivo
	`.TweetText`                               | Seleciona a propriedade de mensagem do tweet.
	
5. Clique na marca de seleção verde para salvar as configurações do conector.

5. Agora que o design está pronto, clique em **Exibição de código** na parte superior esquerda do designer e observe que este é o código JSON que define o fluxo de trabalho você acabou de criar no designer.  Falaremos mais sobre este código no [próximo tópico][Usar recursos de aplicativos lógicos].

6. Clique no botão **OK** na parte inferior da tela e clique no botão **Criar**. 

	Isso cria um novo aplicativo lógico.

## Gerenciando seu aplicativo lógico após a criação

Agora sua aplicativo lógico está em execução.  Sempre que o fluxo de trabalho agendado é executado, ele procura tweets com a  hashtag específica.  Quando encontra uma tweet correspondente, ele o coloca no seu Dropbox.  Por fim, você verá como desabilitar o aplicativo ou como está seu desempenho. 

1. Clique em **Navegar** no lado esquerdo da tela e selecione **Aplicativos Lógicos**. 
 
2. Clique no novo aplicativo lógico que você acabou de criar para ver o status atual e informações gerais. 

3. Para editar seu novo aplicativo lógico, clique em **Gatilhos e Ações**. 
 
5. Para desabilitar o aplicativo, clique em **Desabilitar** na barra de comandos.

Em menos de 5 minutos, você configurou um aplicativo lógico simples e o colocou em execução na nuvem.  Para saber mais sobre como usar os recursos de aplicativos lógicos, consulte [Usar os recursos de aplicativos lógicos].

<!-- Shared links -->
[Portal do Azure]: https://portal.azure.com
[Usar recursos de aplicativo lógico]: app-service-logic-use-logic-app-features.md

<!--HONumber=49-->