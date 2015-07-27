<properties 
	pageTitle="Integração de aplicativos de API do BizTalk no Serviço de Aplicativo do Microsoft Azure | Azure" 
	description="Aprenda a criar e configurar os Aplicativos de API de integração do BizTalk; arquitetura de microsserviços" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="mandia"/>


# Integração de aplicativos de API do BizTalk no Serviço de Aplicativo do Microsoft Azure

> [AZURE.NOTE]Este tópico está sendo desativado. Consulte a [lista de conectores e aplicativos de API](app-service-logic-connectors-list.md) para ver todos os aplicativos de API e conectores internos disponíveis.


O Serviço de Aplicativo do Microsoft Azure (ou Serviço de Aplicativo, para abreviar) inclui muitos aplicativos de API do BizTalk que são vitais para ambientes de integração. Esses Aplicativos de API são baseados nos conceitos e ferramentas usados no BizTalk Server, mas agora estão disponíveis como parte do Serviço de Aplicativo do Azure.

Uma categoria desses Aplicativos de API é composta pelos Aplicativos de API de Integração do BizTalk. Usando esses aplicativos de API do BizTalk, você pode facilmente adicionar regras de negócio, transformar e validar mensagens XML, codificar arquivos simples e dados JSON e muito mais; exatamente como faria localmente com o BizTalk Server.

Esses Aplicativos da API de integração oferecem recursos de "Ação". Uma ação é o resultado, como após receber uma mensagem XML, validar a mensagem XML com um esquema XML.


## O que são os Aplicativos de API de Integração
Aplicativos de API de Integração do BizTalk aplicativos de API existentes predefinidos que pode fazer o processamento de dados e produzir uma saída. Por exemplo, alguns desses Aplicativos de API permitem que diferentes formatos de arquivo funcionem juntos e alguns aplicam lógica de negócios ou lógica de aplicativo. Os Aplicativos da API de Integração incluem:

Aplicativos de API | Descrição
--- | ---
<ul><li>Aplicativo de API X12</li><li>Conector AS2</li><li>Aplicativo de API EDIFACT</li><li>Aplicativo de API de Gerenciamento de Parceiro Comercial</li> | Esses Aplicativos da API oferecem recursos Business-to-Business. [Conectores entre empresas](app-service-logic-b2b-connectors.md) fornecem mais detalhes sobre esses Aplicativos de API.
Codificador de arquivo simples do BizTalk | Um aplicativo de API de **Ação**. Faz com que os dados de arquivo simples (como Excel, csv) e dados XML funcionem juntos (interoperabilidade). Pode converter uma instância de arquivo simples para XML e vice-versa.
Codificador JSON do BizTalk | Um aplicativo de API de **Ação**. Faz com que dados JSON e XML funcionem juntos (interoperabilidade). Pode converter uma instância JSON para XML e vice-versa.
Regras do BizTalk | Um aplicativo de API de **Ação**. Você pode implementar e aplicar lógica ou "regras" de negócios para controlar uma operação de negócios. As regras são dinâmicas e podem mudar a qualquer momento. As Regras do BizTalk permitem que os usuários habilitem a lógica de negócios sem escrever nenhum código, e podem ser facilmente atualizadas sem causar impacto aos aplicativos.
Transformação do BizTalk | Um aplicativo de API de **Ação**. Converte dados de um formato para outro. Você pode usar diferentes funções integradas para manipular cadeias de caracteres, concluir expressões aritméticas, formatar a data e a hora, e assim por diante. 
Validador de XML do BizTalk | Um aplicativo de API de **Ação**. Valida os dados XML com relação a esquemas XML predefinidos. Você pode usar esquemas com base em um instâncias de arquivo simples, instâncias JSON ou conectores existentes. 
Extrator XPath do BizTalk | Um aplicativo de API de **Ação**. Pesquisa e extrair dados de conteúdo XML com base em um XPath específico.
Aguarde | Atrasar a execução por uma duração que você insere ou até uma hora específica. Quando adicionado a uma lógica de aplicativo, pode ser usado para atrasar a execução do aplicativo inteiro.


	> [AZURE.NOTE] If the input xml has a simple node with an attribute (like "<authorid= ”1”>abc</author>"), then the JSON output of the library is { “author”: { “@id” : “1”, “#text”: “abc”}}. To handle the “Id” attribute, a new “#text” key is added for the text content of the node. To handle this kind of node, add a constant key. This is by design in the Newtonsoft.Json library. When you insert this into SQL, use “JSONOutput.Author.#text”; do not use “JsonOutput.Author”.

Usando esses Aplicativos de API, você pode concluir diferentes tarefas de dados ou mensagens. Por exemplo, usando o Aplicativo de API de Regras do BizTalk, você pode receber um pedido e aplicar um desconto quando uma quantidade específica for pedida. Ou você pode cobrar uma taxa de imposto específica dependendo do código postal.

Você pode, com facilidade, criar tantos Aplicativos de API quanto desejar. Também é possível reutilizar Aplicativos de API em vários cenários ou fluxos de trabalho.

Por exemplo, digamos que você tenha uma política de negócios que aplique um desconto de 10% quando um cliente pedir 100 itens de seu produto. Em seu aplicativo, você pode adicionar o Aplicativo de API do BizTalk que verifica a quantidade pedida e, se estiver acima de 100, aplicar 10% de desconto.

Você também pode expandir essa política de negócios. Digamos que você tenha uma meta de aumentar as vendas na Carolina do Norte. Além de 10% de desconto em pedidos de 100 ou mais, você também oferece entrega gratuita se o pedido for proveniente da Carolina do Norte. Você pode adicionar facilmente essa condição de Carolina do Norte à sua regra existente do BizTalk.

Você pode fazer isso usando esses Aplicativos de API e sem escrever nenhum código. Vamos começar.


## Criar os aplicativos de API
Aplicativos da API de integração podem ser criados usando o Portal do Azure ou usando APIs REST.


### Criar Aplicativos de API usando APIs REST
Consulte [APIs REST](http://go.microsoft.com/fwlink/p/?LinkId=529766).


### Criar Aplicativos de API de Integração no Portal do Azure
No portal do Azure, você pode criar Aplicativos de API de Integração do BizTalk durante a criação de aplicativos lógicos, aplicativos Web ou aplicativos móveis. Ou você pode criar um usando sua própria folha. Ambos os modos são fáceis, assim, depende das suas necessidades ou preferências. Alguns usuários preferem criar primeiro todos os Aplicativos de API de Integração com suas propriedades específicas. Então criar os aplicativos lógicos, Web ou móveis e adicionar os Aplicativos de API de integração criados.

As seguintes etapas criam Aplicativos de API usando a folha de Aplicativos de API de Integração do BizTalk:

1. No quadro inicial (a Home page) do portal do Azure, selecione **Marketplace**. **Aplicativos de API** lista todos os aplicativos de API e conectores existentes. Você também pode **Pesquisar** um Aplicativo de API do BizTalk específico.
2. Selecione o Aplicativo de API. Na folha nova, selecione **Criar**. 
3. Insira as propriedades: 

	Propriedade | Descrição
--- | ---
Nome | Digite um nome para seu Aplicativo de API. Por exemplo, você pode chamá-lo de *RulesDiscountTaxCode* ou *APIAppValidateXML*.
Plano do Serviço de Aplicativo | Lista seu plano de pagamento. Você pode alterá-lo se precisar de mais ou menos recursos.
Camada de preços | Propriedade somente leitura que lista a categoria de preço em sua assinatura do Azure. 
Grupo de recursos | Crie um grupo novo ou use um existente. Todos os Aplicativos de API e conectores para aplicativos lógicos, Web e móveis devem estar no mesmo grupo de recursos. <br/><br/>[Usando grupos de recursos](../resource-group-overview.md) explica essa propriedade. 
Assinatura | Propriedade somente leitura que lista sua assinatura atual.
Local | A localização geográfica que hospeda o serviço do Azure. 
Adicionar ao quadro inicial | Selecione essa opção para adicionar a os Aplicativos de API de integração para ao quadro inicial (a home page).


## Configurar os aplicativos de API do BizTalk
No Portal de Gerenciamento do Azure, abra seu aplicativo de API do BizTalk. Na seção **Componentes**, você pode adicionar os componentes extras necessários para concluir o Aplicativo de API:

	API App | Tasks
--- | ---
Codificador de arquivo simples do BizTalk | Insira um arquivo simples, como Excel ou csv, você deseje converter para XML. Ou insira um arquivo XML que deseje converter para um arquivo simples.
Codificador JSON do BizTalk | Insira um arquivo JSON que deseje converter para XML. Ou insira um arquivo XML que deseje converter para JSON. 
Regras do BizTalk | Adicione seus vocabulários e crie suas regras IF THEN. Consulte [Usar Regras do BizTalk](app-service-logic-use-biztalk-rules.md). 
Transformação do BizTalk | Adicione um mapa e insira um esquema XML de entrada e um esquema XML de saída. Use as funções internas para manipular a mensagem ou os dados de entrada de acordo com o esquema XML de saída. Consulte [Documentos XML de transformação](app-service-logic-transform-xml-documents.md). 
Validador de XML do BizTalk | Insira o XML para validar com relação um esquema XML predefinido. Você pode usar esquemas com base em um instâncias de arquivo simples, instâncias JSON ou conectores existentes. 
Extrator XPath do BizTalk | Pesquisa e extrair dados de conteúdo XML com base em um XPath específico.
Aguarde | Insira uma duração de tempo ou uma hora específica para executar os aplicativos Web, móveis ou lógicos.


## Monitorar seus aplicativos de API
No Portal de Gerenciamento do Azure, abra seu aplicativo de API do BizTalk. Na seção **Operações**, você pode exibir diferentes operações de gerenciamento. Por exemplo, você pode:

- Exibir eventos de informações e erro
- Exibir a contagem de threads e uso de memória do processo de trabalho (w3wp)
- Exibir logs\\ de servidor Web e do Aplicativo

Mais em [Monitorar seus Aplicativos Lógicos](app-service-logic-monitor-your-logic-apps.md).


## Adicionar os Aplicativos de API do BizTalk ao seu aplicativo 
O Serviço de Aplicativo do Microsoft Azure expõe os diferentes tipos de aplicativos que podem usar esses Aplicativos de API de Integração. Você pode criar um novo ou adicionar seus Aplicativos de API do BizTalk existentes a aplicativos lógicos, móveis ou Web.

Dentro de seu aplicativo, simplesmente selecione seus Aplicativos de API do BizTalk na Galeria para automaticamente adicioná-lo a seu aplicativo.

As etapas a seguir adicionam Aplicativos de API do BizTalk a aplicativos Web, móveis ou lógicos:

1. No quadro inicial do portal do Azure (home page), vá até **Marketplace** e procure aplicativos lógicos, móveis ou Web. 

	Se você estiver criando um novo aplicativo, pesquise aplicativos lógicos, móveis ou Web. Selecione o aplicativo e, na nova folha, selecione **Criar**. [Criar um aplicativo lógico](app-service-logic-create-a-logic-app.md) lista as etapas.

2. Abra seu aplicativo e selecione **Gatilhos e Ações**.

3. Na **Galeria**, selecione o Aplicativo de API do BizTalk, o que o adiciona automaticamente ao seu aplicativo.

4. Selecione **OK** para salvar suas alterações.


## Mais recursos de Aplicativos da API de Integração
[Criar um aplicativo lógico EAI usando VETR](app-service-logic-create-EAI-logic-app-using-VETR.md)<br/> [Transformar documentos XML](app-service-logic-transform-xml-documents.md)<br/> [Usar regras do BizTalk](app-service-logic-use-biztalk-rules.md)<br/> [O que são conectores e aplicativos de API do BizTalk](app-service-logic-what-are-biztalk-api-apps.md)


## Leia sobre aplicativos lógicos e aplicativos Web
[O que são aplicativos lógicos?](app-service-logic-what-are-logic-apps.md)<br/> [Sites e aplicativos Web no Serviço de Aplicativo do Azure](../app-service-web/app-service-web-overview.md)


## Mais conectores

[Lista de conectores e aplicativos de API](app-service-logic-connectors-list.md)<br/><br/> [O que são conectores e aplicativos de API do BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=July15_HO3-->