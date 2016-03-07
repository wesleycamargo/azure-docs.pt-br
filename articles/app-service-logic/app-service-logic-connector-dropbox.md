<properties
	pageTitle="Usando o Conector do Dropbox em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
	description="Como criar e configurar o conector do Dropbox ou aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
	authors="anuragdalmia"
	manager="erikre"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="sameerch"/>

# Comece a usar o Conector do Dropbox e adicione-o a seu Aplicativo Lógico
>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview de aplicativos lógicos. Para a versão do esquema 2015-08-01-preview, clique em [API do Dropbox](../connectors/create-api-dropbox.md).

Conecte-se à conta do Dropbox para carregar ou baixar arquivos. Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados. Você pode adicionar o Conector do Dropbox a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico.

## Gatilhos e Ações

Um Gatilho inicia uma nova instância com base em um evento específico, como a chegada de uma nova mensagem. Uma Ação é o resultado, como ocorre após receber uma nova mensagem e depois carregar o arquivo no Dropbox.

O conector do Dropbox pode ser usado como uma ação em um aplicativo lógico e dá suporte a dados nos formatos JSON e XML. O Conector do Dropbox tem os seguintes gatilhos e ações disponíveis:

Gatilhos | Ações
--- | ---
Nenhum | <ul><li>Excluir Arquivo</li><li>Obter Arquivo</li><li>Carregar Arquivo</li><li>Listar Arquivos</li></ul>


## Criar um conector do Dropbox para o aplicativo lógico
Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Pesquise "Conector do Dropbox", selecione-o e selecione **Criar**.
3. Digite o Nome, o Plano do Serviço de Aplicativo e outras propriedades: 
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

>[AZURE.NOTE] Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

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

<!---HONumber=AcomDC_0224_2016-->