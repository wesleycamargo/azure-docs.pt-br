<properties
	pageTitle="Usando o Conector do Dropbox no aplicativo lógico no Serviço de Aplicativo do Azure"
	description="Introdução ao Conector do Dropbox em um aplicativo lógico no Serviço de Aplicativo"
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
	ms.date="08/09/2015"
	ms.author="sameerch"/>

# Conector do Dropbox

O Conector do Dropbox permite que você carregue ou baixe arquivos de sua conta do Dropbox.

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo.

## Gatilhos e Ações

Um Gatilho inicia uma nova instância com base em um evento específico, como a chegada de uma nova mensagem. Uma Ação é o resultado, como ocorre após receber uma nova mensagem e depois carregar o arquivo no Dropbox.

O conector do Dropbox pode ser usado como uma ação em um aplicativo lógico e dá suporte a dados nos formatos JSON e XML. O conector do Chatter tem os seguintes Gatilhos e Ações disponíveis:

Gatilhos | Ações
--- | ---
Nenhum | <ul><li>Excluir Arquivo</li><li>Obter Arquivo</li><li>Carregar Arquivo</li><li>Listar Arquivos</li></ul>


## Criar um conector do Dropbox para o aplicativo lógico
Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Pesquise "Conector do Dropbox", selecione-o e selecione **Criar**.
3. Insira o Nome, o Plano do Serviço de Aplicativo e outras propriedades: 
	![][1]
	- **Local** - escolha a região geográfica onde você quer que o conector seja implantado
	- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
	- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
	- **Plano do Serviço de Aplicativo** - selecione ou crie um plano de hospedagem na Web
	- **Camada de preços** - escolha uma camada de preços para o conector
	- **Nome** - dê um nome ao Conector do Dropbox  
4. Selecione **Criar**.


## Usando o Conector do Dropbox em seu aplicativo lógico
Depois que o aplicativo de API for criado, você poderá usar o Conector do Dropbox como uma ação para seu aplicativo lógico. Para fazer isso:

1.	No aplicativo lógico, abra **Gatilhos e Ações** para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo: 
	![][3]
2.	O conector do Dropbox está listado na galeria: 
	![][4]
3.	Selecione o conector do Dropbox para adicionar automaticamente no designer. Selecione **Autorizar**, insira as suas credenciais e selecione **Permitir**: 
	![][5]
	![][6] 
	![][7]

Agora você pode usar o conector do Dropbox no fluxo. Você pode usar a ação do Dropbox "Carregar Arquivo" para carregar um arquivo para sua conta do Dropbox: 
	![][8] 
	![][9]

Configure as propriedades de entrada para a ação "Carregar Arquivo" da seguinte maneira:

- **Caminho do Arquivo** - especifique o caminho do arquivo de destino do Dropbox a ser carregado. Exemplo: Fotos/imagem.png
- **Conteúdo** - especifica o conteúdo do arquivo a ser carregado. Geralmente, isso virá de uma etapa anterior em seu Aplicativo lógico.
- **Codificação de Transferência de Conteúdo** - especifique nenhuma ou base64.
- **Substituir** - especifique "true" para substituir o arquivo, se ele já existir.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Crie aplicativos de API usando APIs REST. Consulte [Referência a aplicativos de API e conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-dropbox/img1.PNG
[2]: ./media/app-service-logic-connector-dropbox/img2.PNG
[3]: ./media/app-service-logic-connector-dropbox/img3.png
[4]: ./media/app-service-logic-connector-dropbox/img4.png
[5]: ./media/app-service-logic-connector-dropbox/img5.PNG
[6]: ./media/app-service-logic-connector-dropbox/img6.PNG
[7]: ./media/app-service-logic-connector-dropbox/img7.PNG
[8]: ./media/app-service-logic-connector-dropbox/img8.PNG
[9]: ./media/app-service-logic-connector-dropbox/img9.PNG

<!---HONumber=August15_HO7-->