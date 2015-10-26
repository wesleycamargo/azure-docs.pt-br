<properties
	pageTitle="Usando o Conector do OneDrive em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
	description="Como criar e configurar o Conector do OneDrive ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
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
	ms.date="08/23/2015"
	ms.author="andalmia"/>

# Introdução ao Conector do OneDrive e à adição dele a seu Aplicativo Lógico
Conecte-se ao seu OneDrive para carregar, baixar e excluir arquivos. Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. Você pode adicionar o Conector do OneDrive a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico.

## Criando um Conector do OneDrive para seu aplicativo lógico ##
Para usar o conector do OneDrive, primeiro você precisa criar uma instância do aplicativo de API do conector do OneDrive. Isso pode ser feito diretamente do designer do aplicativo lógico ou de fora dele. Criar uma instância fora do designer pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace na home page do Portal do Azure.
2.	Em "Tudo", pesquise "Conector do OneDrive".
3.	Configure o Conector do OneDrive da seguinte maneira:

	![][1] - **Nome** - dê um nome para o Conector do OneDrive - **Plano do Serviço de Aplicativo** - selecione ou crie um plano de serviço de aplicativo - **Camada de preços** - escolha uma camada de preços para o conector - **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir - **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado - **Local** -escolha o local geográfico onde quer que o conector seja implantado

4. Clique em Criar. Será criado um novo Conector do OneDrive.
5. Após criar a instância do aplicativo de API, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o conector do OneDrive.

## Usando o Conector de OneDrive em seu aplicativo lógico ##
Depois que o aplicativo de API for criado, você poderá usar o Conector do OneDrive como uma ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Crie um novo aplicativo lógico e escolha o mesmo grupo de recursos que tem o conector do OneDrive. Siga as instruções para [Criar um novo aplicativo lógico].

2.	Abra "Gatilhos e Ações" no aplicativo lógico criado para abrir o Designer de Aplicativos Lógicos e configure seu fluxo.

3.	O conector do OneDrive será exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito.

	![][2]
4.	Você pode soltar o aplicativo de API do Conector do OneDrive no editor clicando em "Conector do OneDrive". Clique no botão Autorizar. Forneça suas credenciais de Microsoft (se não for conectado automaticamente). Clique em "Sim" para permitir o acesso.

	![][3] ![][4]

5.	Agora você pode usar o Conector do OneDrive no fluxo. Atualmente, os gatilhos não estão disponíveis no conector do OneDrive. As ações disponíveis são: Obter Arquivo, Carregar um Arquivo, Excluir Arquivos e Listar Arquivos.

	![][5]

6.	Vamos examinar uma experiência de "Carregar Arquivo". Você pode usar a ação do OneDrive "Carregar Arquivo" para carregar um arquivo para sua conta do OneDrive.

	![][6]

	Configure as propriedades de entrada para a ação "Carregar Arquivo" da seguinte maneira:

 - **Caminho do Arquivo** - especifique o caminho do arquivo a ser carregado
 - **Conteúdo** - Especifica o conteúdo do arquivo a ser carregado
 - **Codificação de Transferência de Conteúdo** - especifique nenhuma ou Base64
 - **Substituir** - especifique "true" para substituir o arquivo, se ele já existir. Essa é uma propriedade avançada

7. Depois que elas são configuradas, a ação "Carregar um Arquivo" é configurada e pode ser usada em seu fluxo. Da mesma forma, outras ações também podem ser configuradas.

8. Para usar o conector fora de um aplicativo lógico, as APIs REST expostas pelo conector podem ser utilizadas. Você exibir essas definições da API usando Procurar->Aplicativo de Api->Conector do OneDrive. Agora clique na lente Definição de API na seção Resumo para exibir todas as APIs expostas por esse conector.

	![][7]

9. Detalhes das APIs podem ser encontrados em [Definição da API do OneDrive].

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-onedrive/img1.PNG
[2]: ./media/app-service-logic-connector-onedrive/img2.PNG
[3]: ./media/app-service-logic-connector-onedrive/img3.PNG
[4]: ./media/app-service-logic-connector-onedrive/img4.PNG
[5]: ./media/app-service-logic-connector-onedrive/img5.PNG
[6]: ./media/app-service-logic-connector-onedrive/img6.PNG
[7]: ./media/app-service-logic-connector-onedrive/img7.PNG

<!-- Links -->
[Criar um novo aplicativo lógico]: app-service-logic-create-a-logic-app.md
[Definição da API do OneDrive]: https://msdn.microsoft.com/library/dn974227.aspx

<!---HONumber=Oct15_HO3-->