<properties
	pageTitle="Adicione a API de pesquisa do Bing ao PowerApps Enterprise | Microsoft Azure"
	description="Crie ou configure uma nova API de pesquisa do Bing no ambiente de serviço de aplicativo da sua organização"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="LinhTran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Criar uma nova API de pesquisa do Bing no PowerApps Enterprise

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/create-api-bingsearch.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-bingsearch.md)

Adicione a API de Pesquisa do Bing ao ambiente de serviço de aplicativo (locatário) da sua organização.

## Crie a API no portal do Azure

1. No [Portal do Azure](https://portal.azure.com/), entre com sua conta de trabalho. Por exemplo, entre com *Seunomedeusuário*@*SuaEmpresa*.com. Quando fizer isso, entrará automaticamente na assinatura de sua empresa.
 
2. Selecione **Procurar** na barra de tarefas: ![][4]

3. Na lista, você pode rolar a tela para localizar PowerApps ou digite *powerapps*: ![][5]

4. Em **PowerApps**, selecione **Gerenciar APIs**: ![Navegue até as APIs registradas][2]

2. Em **Gerenciar APIs**, selecione **Adicionar** para adicionar a nova API: ![Adicionar API][3]

3. Insira um **nome** descritivo para sua API.

4. Em **Fonte**, selecione **API disponível** para selecionar as APIs criadas previamente e selecione **pesquisa do Bing**:

	1. Selecione **Configurações - definir as configurações necessárias**.
	2. Insira a *chave de conta*. Se você não tiver uma chave de pesquisa do Bing, crie uma [oferta da pesquisa do Bing][1] gratuita para obter uma chave.
	3. Selecione **OK**. 

5. Selecione **OK** para concluir as etapas.

Quando terminar, uma nova API de pesquisa do Bing será adicionada ao seu ambiente de serviço de aplicativo.

## Consulte as APIs REST

Referência de [API de REST de pesquisa do Bing](../connectors/create-api-bingsearch.md).


## Resumo e próximas etapas
Neste tópico, você adicionou a API de pesquisa do Bing para o seu PowersApps Enterprise. Em seguida, forneça aos usuários acesso à API para que ela possa ser adicionada aos seus aplicativos:

[Adicione uma conexão e forneça acesso aos usuários](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://datamarket.azure.com/dataset/bing/search
[2]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[3]: ./media/powerapps-create-api-dropbox/add-api.PNG
[4]: ./media/powerapps-create-api-dropbox/browseall.png
[5]: ./media/powerapps-create-api-dropbox/allresources.png

<!----HONumber=AcomDC_0309_2016-->