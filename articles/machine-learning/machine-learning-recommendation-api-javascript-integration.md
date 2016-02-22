<properties 
	pageTitle="Recomendações do Aprendizado de Máquina: Integração do JavaScript | Microsoft Azure" 
	description="Recomendações do Aprendizado da Máquina do Azure - Integração do JavaScript - documentação" 
	services="machine-learning" 
	documentationCenter="" 
	authors="AharonGumnik" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="02/10/2016" 
	ms.author="luisca"/>

# Recomendações do Aprendizado da Máquina do Azure - Integração do JavaScript

Este documento descreve como integrar seu site usando o JavaScript. O JavaScript permite enviar eventos de Aquisição de Dados e usar as recomendações assim que você cria um modelo de recomendação. Todas as operações realizadas via JS também podem ser feitas do lado do servidor.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##1. Visão geral
Integrando seu site com o AM do Azure As recomendações consistem em duas fases:

1.	Enviar Eventos para as Recomendações do AM do Azure. Isso permitirá criar um modelo de recomendação.
2.	Use as recomendações. Depois do modelo ser criado, você pode usar as recomendações. (Este documento não explica como criar um modelo; leia o guia de início rápido para obter mais informações sobre isso).


<ins>Fase I</ins>

Na primeira fase, você insere em suas páginas html uma pequena biblioteca JavaScript que permite que a página envie eventos conforme eles ocorrem na página html para os servidores de Recomendações do AM do Azure (via Data Market):

![Desenho1][1]

<ins>Fase II</ins>

Na segunda fase, quando você quiser mostrar as recomendações na página, selecione uma das opções a seguir:

1. Seu servidor (na fase de renderização da página) chama o Servidor de Recomendações do AM do Azure (via Data Market) para obter recomendações. Os resultados incluem uma lista de id de itens. Seu servidor precisa enriquecer os resultados com os Metadados dos itens (por exemplo, imagens, descrição) e enviar a página criada para o navegador.

![Desenho2][2]

2. A outra opção é usar o pequeno arquivo JavaScript da fase 1 para obter uma lista simples de itens recomendados. Os dados recebidos aqui serão mais enxutos que na primeira opção.

![Desenho3][3]

##2. Pré-requisitos

1. Crie um novo modelo usando as APIs. Consulte o Guia de início rápido sobre como fazer isso.
2. Codifique o & lt; dataMarketUser & gt;: & lt; dataMarketKey & gt; com a base64. (Isso será usado para a autenticação básica para ativar o código JS para chamar as APIs).


##3. Enviar eventos de Aquisição de Dados usando o JavaScript
As etapas a seguir facilitam o envio de eventos:

1.	Inclua a biblioteca JQuery em seu código. Você pode baixá-la do nuget na URL a seguir.

		http://www.nuget.org/packages/jQuery/1.8.2
2.	Inclua a biblioteca JavaScript de Recomendações da seguinte URL: http://1drv.ms/1Aoa1Zp

3.	Inicialize a biblioteca de Recomendações do AM do Azure com os parâmetros apropriados.

		<script>
			AzureMLRecommendationsStart("<base64encoding of username:key>",
			"<model_id>");
		</script>

4.	Envie o evento apropriado. Consulte a seção detalhada abaixo sobre todos os tipos de eventos (exemplo de evento de clique)

		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { 		
        	        	AzureMLRecommendationsEvent = [];
	                }
			AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });
		</script>


###3.1. Limitações e Suporte ao Navegador
Isso é uma implementação de referência e é fornecida como está. Deve oferecer suporte a todos os principais navegadores.

###3.2. Tipo de Eventos
Há cinco tipos de evento que a biblioteca suporta: Clicar, Clique de Recomendação, Adicionar ao Carrinho de Compras, Remover do Carrinho de Compras e Comprar. Há um outro evento que é usado para definir o contexto do usuário chamado Logon.

####3.2.1. Evento Clicar
Esse evento deve ser usado sempre que um usuário clicou em um item. Normalmente, quando o usuário clica em um item, uma nova página é aberta com os detalhes do item; nessa página, o evento deve ser inicializado.

Parâmetros: 
- evento (cadeia de caracteres, obrigatório) – "click" 
- item (cadeia de caracteres, obrigatório) – Identificador exclusivo do item 
- itemName (cadeia de caracteres, opcional) – o nome do item 
- itemDescription (cadeia de caracteres, opcional) – a descrição do item 
- itemCategory (cadeia de caracteres, opcional) – a categoria do item
		
		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
		</script>

Ou com dados opcionais:

		<script>
			if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
		</script>


####3.2.2. Evento do Clique de Recomendação
Esse evento deve ser usado sempre que um usuário clicou em um item que foi recebido das Recomendações do AM do Azure como um item recomendado. Normalmente, quando o usuário clica em um item, uma nova página é aberta com os detalhes do item; nessa página, o evento deve ser inicializado.

Parâmetros:
- evento (cadeia de caracteres, obrigatório) – "recommendationclick"
- item (cadeia de caracteres, obrigatório) – Identificador exclusivo do item
- itemName (cadeia de caracteres, opcional) – o nome do item
- itemDescription (cadeia de caracteres, opcional) – a descrição do item
- itemCategory (cadeia de caracteres, opcional) – a categoria do item
- propaga (matriz da cadeia de caracteres, opcional) – as propagações que geraram a consulta de recomendação.
- recoList (matriz da cadeia de caracteres. opcional) – o resultado da solicitação de recomendação que gerou o item clicado.
		
		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
		</script>

Ou com dados opcionais:

		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"] 				});
		</script>


####3.2.3. Evento Adicionar ao Carrinho de Compras
Esse evento deve ser usado quando o usuário adiciona um item ao carrinho de compras. 
Parâmetros: 
* evento (cadeia de caracteres, obrigatório) – "addshopcart" 
* item (cadeia de caracteres, obrigatório) – Identificador exclusivo do item 
* itemName (cadeia de caracteres, opcional) – o nome do item 
* itemDescription (cadeia de caracteres, opcional) – a descrição do item 
* itemCategory (cadeia de caracteres, opcional) – a categoria do item
		
		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
		</script>

####3.2.4. Evento Remover do Carrinho de Compras
Esse evento deve ser usado quando o usuário remove um item do carrinho de compras.

Parâmetros: 
* evento (cadeia de caracteres, obrigatório) – "removeshopcart" 
* item (cadeia de caracteres, obrigatório) – Identificador exclusivo do item 
* itemName (cadeia de caracteres, opcional) – o nome do item 
* itemDescription (cadeia de caracteres, opcional) – a descrição do item 
* itemCategory (cadeia de caracteres, opcional) – a categoria do item
		
		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
		</script>

####3.2.5. Evento Comprar
Esse evento deve ser usado quando o usuário comprou o carrinho de compras.

Parâmetros:
* evento (cadeia de caracteres) – "purchase"
* itens ( Comprado) – A matriz contém uma entrada para cada item comprado.<br><br>
Formato da compra:
	* item (cadeia de caracteres) - Identificador exclusivo do item.
	* count (int ou cadeia de caracteres) – o número de itens comprados.
	* preço (float ou cadeia de caracteres) – campo opcional – o preço do item.

O exemplo abaixo mostra a compra de três itens (33, 34, 35), dois com todos os campos preenchidos (item, contagem, preço) e outro (item 34) sem um preço.

		<script>
			if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
		</script>

####3.2.6. Evento Logon do Usuário
A biblioteca Evento de Recomendações do AM do Azure cria e usa um cookie para identificar os eventos que vieram do mesmo navegador. Para melhorar os resultados do modelo, as Recomendações do AM do Azure permitem definir uma identificação exclusiva do usuário que substituirá o uso do cookie.

Esse evento deve ser usado após o logon do usuário em seu site.

Parâmetros:
* evento (cadeia de caracteres) – "userlogin" 
* usuário (cadeia de caracteres) – a identificação exclusiva do usuário.
		<script>
			se (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = ; }
			AzureMLRecommendationsEvent.push({event: "userlogin", user: “ABCD10AA” });
		</script>

##4. Usar Recomendações via JavaScript
O código que usa a recomendação é inicializado por um evento JavaScript pela página da Web do cliente. A resposta de recomendação inclui as Ids dos itens recomendados, seus nomes e suas classificações. É melhor usar essa opção apenas para exibir uma lista dos itens recomendados – uma manipulação mais complexa (como adicionar os metadados do item) deve ser feita na integração no lado do servidor.

###4.1 Usar as Recomendações
Para usar recomendações, você precisa incluir as bibliotecas JavaScript necessárias em sua página e chamar AzureMLRecommendationsStart. Consulte a seção 2.

Para usar as recomendações para um ou mais itens, você precisa chamar um método denominado: AzureMLRecommendationsGetI2IRecommendation.

Parâmetros:
* itens (matriz de cadeias de caracteres) – um ou mais itens para os quais obter recomendações. Se você usar uma compilação Fbt, então, você pode definir aqui somente um item.
* numberOfResults (int) – o número de resultados necessários.
* includeMetadata (booleano, opcional) – se definido para 'true', indica que o campo dos metadados deve ser preenchido no resultado.
* Função de processamento – uma função que lidará com as recomendações retornadas. Os dados são retornados como uma matriz de:
	* Item – a id exclusivo do item
	* nome – o nome de item (se existir no catálogo)
	* classificação – a classificação da recomendação
	* metadados – uma cadeia de caracteres que representa os metadados do item

Exemplo: O código a seguir solicita oito recomendações para o item "64f6eb0d-947a-4c18-a16c-888da9e228ba" (e não especificando includeMetadata – informa implicitamente que nenhum metadado é necessário), então, concatena os resultados em um buffer.

		<script>
 			var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
 				var buff = "";
 				for (var ii = 0; ii < reco.length; ii++) {
   					buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
 				}
 				alert(buff);
			});
		</script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png
 

<!---HONumber=AcomDC_0211_2016-->