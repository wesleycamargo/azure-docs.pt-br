<properties 
	pageTitle="Usar recursos de aplicativo lógico | Microsoft Azure" 
	description="Saiba como usar os recursos avançados de aplicativos lógicos." 
	authors="stepsic-microsoft-com" 
	manager="erikre" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="stepsic"/>
	
# Usar recursos de aplicativos lógicos

No [tópico anterior](app-service-logic-create-a-logic-app.md), você criou seu primeiro aplicativo lógico. Agora mostraremos como compilar um processo mais completo usando aplicativos lógicos de Serviços de Aplicativos. Este tópico apresenta novos conceitos de aplicativos lógicos a seguir:

- Lógica condicional, que executa uma ação somente quando uma determinada condição for atendida.
- Exibição de código para editar um aplicativo lógico existente.
- Opções para iniciar um fluxo de trabalho.

Antes de concluir este tópico, você deve concluir as etapas em [Criar um novo aplicativo lógico](app-service-logic-create-a-logic-app.md). No [Portal do Azure], navegue até o aplicativo lógico e clique em **Gatilhos e Ações** em resumo para editar a definição de aplicativo lógico.

## Material de referência

Os documentos a seguir podem ser úteis:

- [Gerenciamento e APIs REST de tempo de execução](https://msdn.microsoft.com/library/azure/mt643787.aspx) - incluindo como chamar aplicativos de lógica diretamente
- [Referência de linguagem](https://msdn.microsoft.com/library/azure/mt643789.aspx) - uma lista abrangente de todas as expressões/funções com suporte
- [Tipos ação e gatilho](https://msdn.microsoft.com/library/azure/mt643939.aspx) - os diferentes tipos de ações e as entradas que aceitam
- [Visão geral do serviço de aplicativo](../app-service/app-service-value-prop-what-is.md) - descrição de quais componentes podem ser escolhidos ao criar uma solução

## Como adicionar lógica condicional

Embora o fluxo original funcione, há algumas áreas que poderiam ser melhoradas.


### Condicional
Esse aplicativo lógico pode resultar no recebimento de muitos emails. As etapas a seguir adicionam uma lógica para certificar-se de que você receba apenas um email quando o tweet vier de alguém com determinado número de seguidores.

1. Clique no sinal de adição e encontre a ação *Obter Usuário* do Twitter.

2. Transmita o campo **Tweet publicado por** do gatilho para obter informações sobre o usuário do Twitter.

	![Obter usuário](./media/app-service-logic-use-logic-app-features/getuser.png)

3. Clique no sinal de adição novamente, porém desta vez selecione **Adicionar Condição**

4. Na primeira caixa, clique no **...** abaixo de **Obter Usuário** para encontrar o campo **Contagem de seguidores**.

5. No menu suspenso, selecione **Maior que**

6. Na segunda caixa, digite o número de seguidores que você deseja que os usuários tenham.

	![Condicional](./media/app-service-logic-use-logic-app-features/conditional.png)

7.  Por fim, arraste e solte a caixa de email na caixa **Se Sim**. Isso significa que você só receberá emails quando a contagem de seguidores for atingida.

## Repetição em uma lista com forEach

O loop forEach especifica uma matriz para repetir uma ação. Se não for uma matriz, o fluxo falhará. Por exemplo, se tiver action1 que gera uma matriz de mensagens e desejar enviar cada mensagem, você poderá incluir essa instrução forEach nas propriedades da ação: forEach: "@action('action1').outputs.messages"
 

## Usando o modo de exibição de código para editar um aplicativo lógico

Além do designer, você pode editar diretamente o código que define um aplicativo lógico da seguinte maneira.

1. Clique no botão **Visualização do código** na barra de comandos.

	Isso abre um editor completo que mostra a definição que você acabou de editar.

	![Exibição de código](./media/app-service-logic-use-logic-app-features/codeview.png)

    Usando o editor de texto, você pode copiar e colar qualquer número de ações dentro do mesmo aplicativo lógico ou entre os aplicativos lógicos. Você também pode facilmente adicionar ou remover seções inteiras da definição e compartilhar definições com outras pessoas.

2. Depois de fazer as alterações na visualização de código, basta clicar em **Salvar**.

### Parâmetros
Há alguns recursos de aplicativos lógicos que podem ser usados apenas na exibição de código. Um exemplo são os parâmetros. Parâmetros facilitam a reutilização valores ao longo de seu aplicativo lógico. Por exemplo, se você tiver um endereço de email que deseje usar em várias ações, deve defini-lo como um parâmetro.

O seguinte atualiza seu aplicativo lógico existente para usar parâmetros para o termo da consulta.

1. Na exibição de código, localize o objeto `parameters : {}` e insira o objeto de tópico a seguir:

	    "topic" : {
		    "type" : "string",
		    "defaultValue" : "MicrosoftAzure"
	    }
    
2. Role até a ação `twitterconnector`, localize o valor da consulta e substitua por `#@{parameters('topic')}`. Você também pode usar a função **concat** para unir duas ou mais cadeias, por exemplo: `@concat('#',parameters('topic'))` é idêntico ao acima.
 
Os parâmetros são uma boa maneira de recuperar valores que você provavelmente vai alterar várias vezes. Eles são especialmente úteis quando você precisa substituir parâmetros em ambientes diferentes. Para obter mais informações sobre como substituir parâmetros com base no ambiente, consulte nossa [Documentação da API REST](https://msdn.microsoft.com/library/mt643787.aspx).

Agora, quando clicar em **Salvar**, a cada hora, você receberá todos os novos tweets com mais de cinco retweets entregues em uma pasta chamada **tweets** em seu Dropbox.

Para saber mais sobre as definições de aplicativos lógicos, consulte [Criar definições de aplicativos lógicos](app-service-logic-author-definitions.md).

## Iniciando um fluxo de trabalho do aplicativo lógico
Há várias opções diferentes para iniciar o fluxo de trabalho definido no aplicativo lógico. Um fluxo de trabalho sempre pode ser iniciado por demanda no [Portal do Azure].

### Gatilhos de recorrência
Um gatilho de recorrência é executado em um intervalo especificado por você. Quando o gatilho tem lógica condicional, ele determina se o fluxo de trabalho precisa ser executado. Um gatilho indica que ele deve ser executado retornando um código de status `200`. Quando não precisar ser executado, ele retornará um código de status `202`.

### Retorno de chamada usando APIs REST
Os serviços podem chamar um ponto de extremidade do aplicativo lógico para iniciar um fluxo de trabalho. Veja [Aplicativos Lógicos como pontos de extremidade que podem ser chamados](app-service-logic-connector-http.md) para obter mais informações. Para iniciar esta variante de aplicativo lógico sob demanda, clique no botão **Executar agora** na barra de comandos.

<!-- Shared links -->
[Portal do Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0803_2016-->