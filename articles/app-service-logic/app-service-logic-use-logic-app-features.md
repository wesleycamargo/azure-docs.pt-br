<properties 
	pageTitle="Usar recursos de aplicativo lógico" 
	description="Saiba como usar os recursos avançados de aplicativos lógicos." 
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
	ms.date="08/19/2015"
	ms.author="stepsic"/>
	
# Usar recursos de aplicativos lógicos

No [tópico anterior][Create a new logic app], você criou seu primeiro aplicativo lógico. Agora mostraremos como compilar um processo mais completo usando aplicativos lógicos de Serviços de Aplicativos. Este tópico apresenta novos conceitos de aplicativos lógicos a seguir:

- Lógica condicional, que executa uma ação somente quando uma determinada condição for atendida.
- Ações repetidas.
- Exibição de código para editar um aplicativo lógico existente.
- Opções para iniciar um fluxo de trabalho.

Antes de concluir este tópico, você deve concluir as etapas em [Criar um novo aplicativo lógico]. No [Portal do Azure], navegue até o aplicativo lógico e clique em **Gatilhos e Ações** em resumo para editar a definição de aplicativo lógico.

## Material de referência

Os documentos a seguir podem ser úteis:

- [Gerenciamento e APIs REST de tempo de execução](https://msdn.microsoft.com/library/azure/dn948513.aspx) - incluindo como chamar aplicativos de lógica diretamente
- [Referência de linguagem](https://msdn.microsoft.com/library/azure/dn948512.aspx) - uma lista abrangente de todas as expressões/funções com suporte
- [Tipos ação e gatilho](https://msdn.microsoft.com/library/azure/dn948511.aspx) - os diferentes tipos de ações e as entradas que aceitam
- [Visão geral do serviço de aplicativo](app-service-value-prop-what-is.md) - descrição de quais componentes podem ser escolhidos ao criar uma solução

## Adicionando lógica condicional e uma repetição

Embora o fluxo original funcione, há algumas áreas que poderiam ser melhoradas. Primeiro, a ação envia somente o principal tweet retornado. Logicamente, você desejaria receber todos os tweets com a palavra-chave. Para repetir uma ação para obter uma lista de itens, como os tweets retornados, você deve usar a propriedade `repeat`.

### Repetição
A repetição pega uma lista de itens e executa a ação para cada item da lista. As etapas a seguir atualizam a ação existente para usar repetições, o que faz mais sentido para obter uma lista de tweets.

1. Retorne ao fluxo de trabalho criado e clique no link **Definição** em **Essentials**. 

2. Para editar a ação do **Conector da Pasta de Recados**, clique no ícone de lápis.

3. Clique no ícone de engrenagem e selecione **Repetir em uma Lista**.
 
2. Ao lado da caixa **Repetir**, clique em `...` e selecione **Corpo**. Isso inserirá:

    	@body('twitterconnector')

	Na caixa de texto. Esta função gera uma lista de tweets.

3. Selecione todo o texto da caixa de texto **Conteúdo** e exclua-o. Em seguida, clique em `...` e selecione **Texto do Tweet**. Isso vai inserir a função **repeatItem()**, que retorna cada elemento na lista.

Finalmente, observe que as saídas de ações repetidas são especiais. Se você quisesse fazer referência aos resultados da operação de Dropbox, por exemplo, *não* poderia fazer o `@actions('dropboxconnector').outputs.body` normal; em vez disso, você faria: `@actions('dropboxconnector').outputs.repeatItems`. Isso retorna uma lista de todas as vezes que a operação foi executada, juntamente com as saídas de cada. Por exemplo, `@first(actions('dropboxconnector').outputs.repeatItems).outputs.body.FilePath` retornará o caminho do primeiro arquivo carregado.

### Condicional
Esse aplicativo lógico ainda resulta em muitos arquivos sendo carregados na Pasta de Recados. As etapas a seguir adicionam lógica extra para certificar-se de que você receba apenas um arquivo quando o tweet tiver um determinado número de retweets.

1. Clique no ícone de engrenagem na parte superior da ação e selecione **Adicionar uma condição a ser atendida**.

2. Na caixa de texto, digite o seguinte:

    	@greater(repeatItem().Retweet_Count , 5)
    
	A função **maior** compara dois valores e só permite que a ação seja executada quando o primeiro valor for maior que o segundo valor. Você acessa uma determinada propriedade como um ponto (.) seguido pelo nome da propriedade, como `.Retweet_Count` acima.

3. Clique na marca de seleção para salvar a ação da Pasta de Recados.

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
    
2. Role até a ação `twitterconnector`, localize o valor de consulta e substitua por `#@{parameters('topic')}`. Você também pode usar a função **concat** para unir duas ou mais cadeias, por exemplo: `@concat('#',parameters('topic'))` é idêntico ao acima.
 
3. Por fim, vá para a ação `dropboxconnector` e adicione o parâmetro de tópico da seguinte maneira:

    	/tweets/@{parameters('topic')}/@{repeatItem().TweetID}.txt

Os parâmetros são uma boa maneira de recuperar valores que você provavelmente vai alterar várias vezes. Eles são especialmente úteis quando você precisa substituir parâmetros em ambientes diferentes. Para obter mais informações sobre como substituir parâmetros com base no ambiente, consulte nossa [Documentação da API REST](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409).

Agora, quando você clicar em **Salvar**, a cada hora você obterá quaisquer novos tweets com mais de 5 retweets entregues em uma pasta chamada **tweets** na sua Pasta de Recados.

Para saber mais sobre as definições de aplicativos lógicos, consulte [Criar definições de aplicativos lógicos](app-service-logic-author-definitions.md).

## Iniciando um fluxo de trabalho do aplicativo lógico
Há várias opções diferentes para iniciar o fluxo de trabalho definido no aplicativo lógico. Um fluxo de trabalho sempre pode ser iniciado por demanda no [Portal do Azure].

### Gatilhos de recorrência
Um gatilho de recorrência é executado em um intervalo especificado por você. Quando o gatilho tem lógica condicional, ele determina se o fluxo de trabalho precisa ser executado. Um gatilho indica que ele deve ser executado retornando um código de status `200`. Quando ele não precisa ser executado, ele retorna o código de status `202`.

### Retorno de chamada usando APIs REST
Os serviços podem chamar um ponto de extremidade do aplicativo lógico para iniciar um fluxo de trabalho. É possível encontrar o ponto de extremidade a acessar navegando até a folha **Propriedades** do botão da barra de comandos **Configurações** no aplicativo lógico.

Você pode usar esse retorno de chamada para invocar um aplicativo lógico de dentro de seu aplicativo personalizado. Você precisa usar autenticação **Básica**. O nome de usuário de `default` é criado para você, e a senha é o campo **Chave de Acesso Primária** na folha **Propriedades**. Por exemplo:

        POST https://<< your endpoint >>/run?api-version=2015-02-01-preview
        Content-type: application/json
        Authorization: Basic << base-64 encoded string of default:<access key> >>
        {
            "name" : "nameOfTrigger",
            "outputs" : { "property" : "value" }
        }

Você pode passar saídas para o fluxo de trabalho e fazer referência a elas no fluxo de trabalho. Por exemplo, com o gatilho acima, se você incluir `@triggers().outputs.property`, obterá `value`.

Para obter mais informações, consulte a [documentação do REST](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409).

### Execução manual
Você pode definir um aplicativo lógico que não tenha um gatilho. Nesse caso, o fluxo de trabalho deve ser iniciado por demanda. Esse tipo de aplicativo lógico é mais adequado para um processo que só precisa ser executado de forma intermitente. Para criar um aplicativo lógico sem um gatilho, marque **Executar essa lógica manualmente** na caixa **Iniciar lógica** no designer.

Para iniciar o aplicativo lógico sob demanda, clique no botão **Executar agora** na barra de comandos.

<!-- Shared links -->
[Create a new logic app]: app-service-logic-create-a-logic-app.md
[Criar um novo aplicativo lógico]: app-service-logic-create-a-logic-app.md
[Portal do Azure]: https://portal.azure.com

<!---HONumber=August15_HO8-->