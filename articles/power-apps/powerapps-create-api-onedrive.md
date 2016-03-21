<properties
	pageTitle="Adicione a API do OneDrive ao PowerApps Enterprise | Microsoft Azure"
	description="Crie ou configure uma nova API do OneDrive no ambiente de serviço de aplicativo da sua organização"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Criar uma nova API do OneDrive no PowerApps Enterprise

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/create-api-onedrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-onedrive.md)

Adicione a API do OneDrive ao ambiente de serviço de aplicativo (locatário) da sua organização.

## Crie a API no portal do Azure

1. No [Portal do Azure](https://portal.azure.com/), entre com sua conta de trabalho. Por exemplo, entre com *Seunomedeusuário*@* SuaEmpresa*.com. Quando fizer isso, entrará automaticamente na assinatura de sua empresa.
 
2. Selecione **Procurar** na barra de tarefas: ![][14]

3. Na lista, você pode rolar a tela para localizar PowerApps ou digite *powerapps*: ![][15]

4. Em **PowerApps**, selecione **Gerenciar APIs**: ![Navegue até as APIs registradas][1]

5. Em **Gerenciar APIs**, selecione **Adicionar** para adicionar a nova API: ![Adicionar API][2]

6. Insira um **nome** descritivo para sua API.
	
7. Em **Fonte**, selecione as **APIs disponíveis** para selecionar as APIs criadas previamente e selecione **OneDrive**: ![Selecione a api do OneDrive][3]

8. Selecione **Configurações - Definir as configurações necessárias**: ![definir configurações da API do OneDrive][4]

9. Digite a *Chave do Aplicativo* e o *Segredo do Aplicativo* do seu aplicativo do OneDrive. Se não tiver um, consulte a seção "Registrar um aplicativo do Google Driver para uso com o PowerApps" neste tópico para criar a chave e os valores secretos necessários.

	> [AZURE.IMPORTANT] Salve a **URL de redirecionamento**. Talvez esse valor seja necessário neste tópico posteriormente.

10. Selecione **OK** para concluir as etapas.

Quando terminar, uma nova API do OneDrive será adicionada ao seu ambiente de serviço de aplicativo.

## Opcional: registre um aplicativo do Google Drive para uso com PowerApps

Se não tiver um aplicativo do OneDrive existente com a chave e valores do segredo, então use as seguintes etapas para criar o aplicativo e obter os valores necessários.

1. Vá para a [página de criação de aplicativo][5] no _Centro do desenvolvedor da conta da Microsoft_ e entre com sua _Conta da Microsoft_.

2. Insira seu **Nome do aplicativo** e selecione **Aceito**: ![Novo aplicativo do OneDrive][6]

3. Na página de configurações:

	1. Selecione **Configurações da API**.  
	2. Configure a URL de redirecionamento para a URL de redirecionamento recebida ao adicionar a nova API do OneDrive no Portal do Azure (neste tópico).  
	3. Selecione **Salvar**.  

	![Configurações da API do aplicativo do OneDrive][7]

É criado um novo aplicativo do OneDrive. Você pode usar esse aplicativo em sua configuração da API do OneDrive no portal do Azure.

## Consulte as APIs REST

Referência da [API REST do Onedrive](../connectors/create-api-onedrive.md).

## Resumo e próximas etapas
Neste tópico, foi adicionada a API do OneDrive ao seu PowersApps Enterprise. Em seguida, disponibilize aos usuários acesso à API para que ela possa ser adicionada aos seus aplicativos:

[Adicione uma conexão e forneça acesso aos usuários](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-onedrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-onedrive/add-api.PNG
[3]: ./media/powerapps-create-api-onedrive/select-onedrive-api.PNG
[4]: ./media/powerapps-create-api-onedrive/configure-onedrive-api.PNG
[5]: https://account.live.com/developers/applications/create
[6]: ./media/powerapps-create-api-onedrive/onedrive-new-app.PNG
[7]: ./media/powerapps-create-api-onedrive/onedrive-app-api-settings.PNG
[14]: ./media/powerapps-create-api-onedrive/browseall.png
[15]: ./media/powerapps-create-api-onedrive/allresources.png

<!---HONumber=AcomDC_0309_2016-->