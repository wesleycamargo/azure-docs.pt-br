<properties 
	pageTitle="Conector do Dropbox"
	description="Introdução ao conector do Dropbox"
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
	ms.date="03/31/2015"
	ms.author="adgoda"/>

# Usando o conector do Dropbox em seu aplicativo lógico #

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo.

O Conector do Dropbox permite que você carregue ou baixe arquivos de sua conta do Dropbox.

## Criando um conector do Dropbox para seu aplicativo lógico ##
Para usar o conector do Dropbox, primeiro você precisa criar uma instância do aplicativo de API do conector do Dropbox. Isso pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace usando a opção +NOVO na parte inferior esquerda do Portal do Azure.
2.	Navegue até "Web e Dispositivos Móveis --> Azure Marketplace" e pesquise "Conector do dropbox".
3.	Configure o Conector do Dropbox da seguinte maneira:
 
	![][1] - **Local** - escolha o local geográfico onde quer que o conector seja implantado - **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado - **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir - **Plano de Serviço de Aplicativo** - selecione ou crie um plano de hospedagem na Web - **Camada de preços** - escolha uma camada de preços para o conector - **Nome** - dê um nome para o conector do Dropbox
4. Clique em Criar. Será criado um novo Conector do Dropbox.
5. Após criar a instância do aplicativo de API, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o conector do Dropbox.

## Usando o Conector do Dropbox em seu aplicativo lógico ##
Depois que o aplicativo de API for criado, você poderá usar o Conector do Dropbox como uma ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Crie um novo aplicativo lógico e escolha o mesmo grupo de recursos que tem o conector do Dropbox.
 	
	![][2]
2.	Abrir "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo. 
 	
	![][3]
3.	O conector do Dropbox será exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito.
 
	![][4]
4.	Você pode soltar o aplicativo de API do Conector do Dropbox no editor clicando em "Conector do Dropbox". Clique no botão Autorizar. Forneça suas credenciais do Dropbox. Clique em "Permitir"
 
	![][5]
	![][6]
	![][7]
6.	Agora você pode usar o conector do Dropbox no fluxo. Você pode usar a ação do Dropbox "Carregar Arquivo" para carregar um arquivo para sua conta do Dropbox.
 
	![][8]
	![][9]

Configure as propriedades de entrada para a ação "Carregar Arquivo" da seguinte maneira:

- **Caminho do Arquivo** - especifique o caminho do arquivo de destino do Dropbox a ser carregado. Exemplo: Fotos/imagem.png
- **Conteúdo** - especifica o conteúdo do arquivo a ser carregado. Geralmente, isso virá de uma etapa anterior em seu Aplicativo lógico.
- **Codificação de Transferência de Conteúdo** - especifique nenhuma ou base64.
- **Substituir** - especifique "true" para substituir o arquivo, se ele já existir.


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
 

<!----HONumber=62-->