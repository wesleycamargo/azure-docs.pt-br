<properties
	pageTitle="Adicione a API de pesquisa do Bing ao PowerApps Enterprise | Microsoft Azure"
	description="Crie ou configure uma nova API de pesquisa do Bing no ambiente de serviço de aplicativo da sua organização"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="LinhTran"
	manager="gautamt"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# Crie uma nova API de pesquisa do Bing no ambiente de serviço de aplicativo da sua organização

## Crie a API no portal do Azure

1. No [portal do Azure](https://portal.azure.com/), conecte-se com sua conta de trabalho. Por exemplo, entre com *Seunomedeusuário*@* SuaEmpresa*.com. Ao fazer isso, você entrará automaticamente na assinatura da sua empresa.
 
2. Selecione **Procurar** na barra de tarefas: ![][4]

3. Na lista, você pode usar a barra de rolagem para localizar o PowerApps ou digitar *powerapps*: ![][5]

4. Em **PowerApps**, selecione **Gerenciar APIs**: ![Navegue até as APIs registradas][2]

2. Em **Gerenciar APIs**, selecione **Adicionar** para adicionar a nova API: ![Adicionar API][3]

3. Insira um **nome** descritivo para sua API.

4. Em **Fonte**, selecione **API disponível** para selecionar as APIs criadas previamente e selecione **pesquisa do Bing**:

	a) Selecione **Configurações - definir as configurações necessárias**.
	
	b) Insira a *chave de conta*. Se você não tiver uma chave de pesquisa do Bing, crie uma [oferta da pesquisa do Bing][1] gratuita para obter uma chave.

	c) Selecione **OK**.

5. Selecione **OK** para concluir as etapas.

Quando terminar, uma nova API de pesquisa do Bing será adicionada ao seu ambiente de serviço de aplicativo.


## Resumo e próximas etapas
Neste tópico, você adicionou a API de pesquisa do Bing para o seu PowersApps Enterprise. Em seguida, forneça aos usuários acesso à API para que ela possa ser adicionada aos seus aplicativos:

[Adicione uma conexão e forneça acesso aos usuários](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://datamarket.azure.com/dataset/bing/search
[2]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[3]: ./media/powerapps-create-api-dropbox/add-api.PNG
[4]: ./media/powerapps-create-api-dropbox/browseall.png
[5]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_1203_2015-->