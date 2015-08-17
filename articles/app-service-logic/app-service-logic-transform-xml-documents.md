<properties 
	pageTitle="Transformação do BizTalk" 
	description="Aprenda a transformar documentos XML de um esquema para outro." 
	authors="anuragdalmia" 
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
	ms.date="06/30/2015"
	ms.author="anuragdalmia"/>

#Transformação do BizTalk


## Visão geral
O Aplicativo de API de Transformação do BizTalk converte dados de um formato para outro. Por exemplo, você pode usar endereços de cobrança e remessas de ordens de compra e inseri-los em um documento de nota fiscal. Ou talvez você tenha uma mensagem de entrada contendo a data atual no formato *YearMonthDay*. Você deseja reformatar a data para que fique no formato *MonthDayYear*.

Você pode fazer isso usando o Aplicativo de API de Transformação no Serviço de Aplicativo do Microsoft Azure. Uma transformação ou mapa consiste em um esquema XML de origem (a entrada) e um esquema XML de destino (a saída). Você pode usar funções internas diferentes para ajudar a manipular e controlar os dados, incluindo manipulações de cadeia de caracteres, atribuições condicionais, expressões aritméticas, formatadores do tempo de data e até mesmo construções em loop.

Mapas são criados no Visual Studio usando o [SDK de Serviços BizTalk do Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=39087). Quando tiver terminado de criar e testar o mapa, carregue o mapa (.trfm) no Aplicativo de API de Transformação do BizTalk.

Recursos adicionais incluem:

- A transformação criada em um mapa pode ser simples, como copiar um nome e endereço de um documento para outro. Ou você pode criar transformações mais complexas usando as operações de mapa prontas para uso.
- Várias operações ou funções de mapeamento estão disponíveis, incluindo cadeias de caracteres, funções de data e hora, e assim por diante.
- Pode fazer uma cópia de dados direta entre os esquemas. No Mapeador do BizTalk, isso é tão simples quanto desenhar uma linha que conecta os elementos no esquema de origem aos seus correspondentes no esquema de destino.
- Ao criar um mapa, você pode exibir uma representação gráfica do mapa, incluindo ver todas as relações e os links que você criar.
- Use o recurso **Testar Mapa** para adicionar uma mensagem XML de exemplo. Com um simples clique, você pode testar o mapa que você criou e ver a saída gerada.
- Carregue os mapas de Serviço BizTalk do Azure existentes (.trfm) e use todos os benefícios do Aplicativo de API de Transformação.
- Ao criar o mapa, você não precisa adicionar um esquema. Quando o mapa estiver pronto, adicione-o ao aplicativo de API de transformação e você estará pronto para começar. 
- Inclui suporte para o formato XML.


## Baixar esquemas de Aplicativos da API de conector
Você pode baixar os esquemas XML para conectores, como SQL, SAP e SharePoint, da página de resumo do Aplicativo de API. Por exemplo, se você quiser baixar esquemas XML para um Aplicativo de API do Conector do SAP específico, procure o Aplicativo de API e abra a página de resumo. Selecione **Baixar Esquemas** e um arquivo zip com todos os esquemas correspondentes às ações SAP é baixado para seu computador. Você pode usar os esquemas para criar um mapa (.trfm) no Visual Studio.


## Criar e adicionar o mapa
Transformações ou mapas são criados no Visual Studio usando o [SDK de Serviços BizTalk do Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=39087), que é baixado gratuitamente.

Para obter ajuda para criar um mapa, consulte [Criar um Mapa no Visual Studio](http://aka.ms/createamapinvs). Depois de o mapa ser criado e estar pronto para produção, você pode adicionar o mapa (arquivo .trfm) para o Aplicativo de API de Transformação do BizTalk criado no Portal de Gerenciamento do Azure.

Se o mapa mudar ou for modificado depois de ser carregado, você pode carregar o mapa atualizado e substituir o mapa existente no Aplicativo de API de Transformação.

1.	Selecione **Procurar** no Portal de Gerenciamento do Azure (à esquerda da tela) e selecione **Aplicativos de API**. Se **Aplicativos de API** não for exibido, selecione **Tudo** e selecione **Aplicativos de API** na lista disponível:

	![][7]

2.	A lista de todos os **Aplicativos de API** criados na sua assinatura do Azure é mostrada:

	![][8]

3.	Selecione o Aplicativo de API de Transformação do BizTalk criado na seção anterior.

4.	A folha de configuração para o Aplicativo de API é aberta. Você pode ver **Mapas** na seção Componentes:

	![][9]

5.	Selecione **Mapas** para abrir a nova folha com a lista de mapas.

6.	Selecione o ícone **Adicionar Mapa** no canto superior para abrir a folha **Adicionar Mapa**:

	![][10]

7.	Selecione o ícone Arquivo e procure um arquivo de mapa (.trfm) no computador local.

8.  Selecione **OK** e um novo mapa é criado. Ele é mostrado na lista de mapas.


## Usar um Aplicativo de API de Transformação do BizTalk em um aplicativo lógico
Depois que o mapa tiver sido criado e testado, ele estará pronto para consumo.

1. No aplicativo lógico, a Transformação do BizTalk está disponível na galeria à direita. Selecione **Serviço de Transformação do BizTalk** da galeria. A Transformação é adicionada ao fluxo:

	![][11]

2. Selecione a ação **Transformar**. Os parâmetros de entrada são exibidos:

	![][12]

3. Insira os seguintes parâmetros para concluir a configuração da ação de **Transformar**:
		 
	- Inserir XML
		- Digite o conteúdo XML válido que esteja de acordo com o esquema de origem de um mapa no Aplicativo de API de Transformação. Isso pode ser uma saída de uma ação anterior na lógica de aplicativo, como 'Chamar RFC – SAP' ou 'Inserir Na Tabela – SQL'.
		
	- Nome do mapa (opcional)
		- Insira um nome válido que já tenha sido carregado em seu Aplicativo de API de Transformação. Se nenhum mapeamento tiver sido inserido, o mapa é selecionado automaticamente com base no esquema de origem com o qual o XML de entrada está em conformidade.

	![][13]

4. A saída da ação 'Produzir XML' pode ser usada em ações subsequentes em seus aplicativos lógicos.

<!--Image references-->
[1]: ./media/app-service-logic-transform-xml-documents/Create_Everything.png
[2]: ./media/app-service-logic-transform-xml-documents/Create_Marketplace.png
[4]: ./media/app-service-logic-transform-xml-documents/Search_TransformAPIApp.png
[5]: ./media/app-service-logic-transform-xml-documents/Transform_APIApp_Landing_Page.png
[6]: ./media/app-service-logic-transform-xml-documents/New_TransformAPIApp_Blade.png
[7]: ./media/app-service-logic-transform-xml-documents/Browse_APIApps.png
[8]: ./media/app-service-logic-transform-xml-documents/Select_APIApp_List.png
[9]: ./media/app-service-logic-transform-xml-documents/Configure_Transform_APIApp.png
[10]: ./media/app-service-logic-transform-xml-documents/Add_Map.png
[11]: ./media/app-service-logic-transform-xml-documents/Transform_action_flow.png
[12]: ./media/app-service-logic-transform-xml-documents/Transform_Inputs.png
[13]: ./media/app-service-logic-transform-xml-documents/Transform_configured.png
[14]: ./media/app-service-logic-transform-xml-documents/Download_Schemas.png



 

<!---HONumber=August15_HO6-->