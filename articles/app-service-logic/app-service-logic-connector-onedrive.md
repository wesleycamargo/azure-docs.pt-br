<properties 
	pageTitle="Conector do OneDrive"
	description="Introdução ao Conector do OneDrive"
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
	ms.date="04/09/2015"
	ms.author="andalmia"/>

# Usando o Conector de OneDrive em seu aplicativo lógico

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. 

O Conector do OneDrive permite que você carregue\baixe\exclua arquivos de sua conta do OneDrive.

## Criando um Conector do OneDrive para seu aplicativo lógico ##
Para usar o conector do OneDrive, primeiro você precisa criar uma instância do aplicativo de API do conector do OneDrive. Isso pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace usando a opção +NOVO na parte inferior esquerda do Portal do Azure.
2.	Navegue até "Web e Dispositivos Móveis > Azure Marketplace" e pesquise "Conector do OneDrive".
3.	Configure o Conector do OneDrive da seguinte maneira:
 
	![][1] 
	- **Nome** - dê um nome ao Conector do OneDrive
	- **Plano de Serviço de Aplicativo** - selecione ou crie um Plano de Serviço de Aplicativo
	- **Camada de preços** - escolha uma camada de preços para o conector
	- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
	- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
	- **Local** - escolha a região geográfica onde você quer que o conector seja implantado

4. Clique em Criar. Será criado um novo Conector do OneDrive.
5. Após criar a instância do aplicativo de API, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o conector do OneDrive.

## Usando o Conector de OneDrive em seu aplicativo lógico ##
Depois que o aplicativo de API for criado, você poderá usar o Conector do OneDrive como uma ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Crie um novo aplicativo lógico e escolha o mesmo grupo de recursos que tem o conector do OneDrive. Siga as instruções para [Criar um novo aplicativo lógico].  	
	
2.	Abra "Gatilhos e Ações" no aplicativo lógico criado para abrir o Designer de Aplicativos Lógicos e configure seu fluxo.  	
	
3.	O conector do OneDrive será exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito.
 
	![][2]
4.	Você pode soltar o aplicativo de API do Conector do OneDrive no editor clicando em "Conector do OneDrive". Clique no botão Autorizar. Forneça suas credenciais de Microsoft (se não for conectado automaticamente). Clique em "Sim" para permitir o acesso.
 
	![][3]
	![][4]
	
5.	Agora você pode usar o Conector do OneDrive no fluxo. Atualmente, os gatilhos não estão disponíveis no conector do OneDrive. As ações disponíveis são: Obter Arquivo, Carregar um Arquivo, Excluir Arquivos e Listar Arquivos.
 
	![][5]

6.	Vamos examinar uma experiência de "Carregar Arquivo". Você pode usar a ação do OneDrive "Carregar Arquivo" para carregar um arquivo para sua conta do OneDrive.
 
	![][6]

	Configure as propriedades de entrada para a ação "Carregar Arquivo" da seguinte maneira:

 - **Caminho do Arquivo** - especifique o caminho do arquivo a ser carregado
 - **Conteúdo** - especifica o conteúdo do arquivo a ser carregado
 - **Codificação de Transferência de Conteúdo** - especifique nenhuma ou Base64
 - **Substituir** - especifique "true" para substituir o arquivo, se ele já existir Essa é uma propriedade avançada

7. Depois que elas são configuradas, a ação "Carregar um Arquivo" é configurada e pode ser usada em seu fluxo. Da mesma forma, outras ações também podem ser configuradas.

8. Para usar o conector fora de um aplicativo lógico, as APIs REST expostas pelo conector podem ser utilizadas. Você exibir essas definições da API usando Procurar->Aplicativo de Api->Conector do OneDrive. Agora clique na lente Definição de API na seção Resumo para exibir todas as APIs expostas por esse conector.

	![][7]

9. Detalhes das APIs podem ser encontrados em [Definição de API do OneDrive].

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
[Definição de API do OneDrive]: https://msdn.microsoft.com/pt-br/library/dn974227.aspx


<!--HONumber=52--> 