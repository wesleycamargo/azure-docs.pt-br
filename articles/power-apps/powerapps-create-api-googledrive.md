<properties
	pageTitle="Adicione a API do Google Drive ao PowerApps Enterprise | Microsoft Azure"
	description="Crie ou configure uma nova API do Google Drive no ambiente de serviço de aplicativo da sua organização"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/29/2016"
   ms.author="litran"/>

# Criar uma nova API do Google Drive no PowerApps Enterprise

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/connectors-create-api-googledrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-googledrive.md)

Adicione a API do Google Drive ao ambiente de serviço de aplicativo (locatário) da sua organização.

## Crie a API no portal do Azure

1. No [Portal do Azure](https://portal.azure.com/), entre com sua conta de trabalho. Por exemplo, entre com *Seunomedeusuário*@*SuaEmpresa*.com. Quando fizer isso, entrará automaticamente na assinatura de sua empresa.
 
2. Selecione **Procurar** na barra de tarefas: ![][15]

3. Na lista, você pode rolar a tela para localizar PowerApps ou digite *powerapps*: ![][16]

4. Em **PowerApps**, selecione **Gerenciar APIs**: ![Navegue até as APIs registradas][1]

5. Em **Gerenciar APIs**, selecione **Adicionar** para adicionar a nova API: ![Adicionar API][2]

6. Insira um **nome** descritivo para sua API.
	
7. Em **Fonte**, selecione as **APIs disponíveis** para selecionar as APIs criadas previamente e selecione **Google Drive**: ![Selecionar api do google drive][3]

8. Selecione **Configurações - Definir as configurações necessárias**: ![definir configurações da API do google drive][4]

9. Digite a *Chave do Aplicativo* e o *Segredo do Aplicativo* do seu aplicativo do Google Drive. Se não tiver um, consulte a seção "Registrar um aplicativo do Google Driver para uso com o PowerApps" neste tópico para criar a chave e os valores secretos necessários.

	> [AZURE.IMPORTANT] Salve a **URL de Redirecionamento**. Talvez esse valor seja necessário neste tópico posteriormente.

10. Selecione **OK** para concluir as etapas.

Quando terminar, uma nova API do Google Drive será adicionada ao seu ambiente de serviço de aplicativo.


## Opcional: registre um aplicativo do Google Drive para uso com PowerApps

Se não tiver um aplicativo do Google Drive existente com a chave e valores do segredo, então use as seguintes etapas para criar o aplicativo e obter os valores necessários.

1. Entre no [Console de Desenvolvedor do Google][5]\: ![Console de desenvolvedor do google][6]

2. Selecione **Criar um projeto vazio**.

3. Insira um nome para seu aplicativo, aceite os termos e condições e selecione **Criar**: ![criar novo projeto do google drive][7]

4. Após a criação bem-sucedida do novo projeto, selecione **Usar APIs do Google**: ![Usar apis do google][8]

5. Na página de visão geral, selecione **API da Unidade**: ![visualização geral da API do Google Drive][9]

6. Selecione **Habilitar API**: ![Habilitar API do Google Drive][10]

7. Após habilitar a API de unidade, selecione **Credenciais**, e selecione **ID do cliente OAuth 2.0**: ![Adicionar credenciais][12]

8. Selecione **Configurar tela de consentimento**.

9. Na **Tela de consentimento deo OAuth**, insira um **Nome do Produto** e selecione **Salvar**: ![Configurar tela de consentimento][13]

10. Clique na página criar id de cliente:

	1. Em **Tipo de aplicativo**, selecione **Aplicativo Web**.  
	2.  Insira um nome para o cliente.  
	3. Configure a URL de redirecionamento para a URL de redirecionamento recebida ao adicionar a nova API do Google Drive no Portal do Azure (neste tópico).  
	4. Selecione **Criar**.  

	![Criar id de cliente][14]

11. A id do cliente e o segredo do cliente do aplicativo registrado serão exibidos.

É criado um novo aplicativo do Google Drive. Você pode usar esse aplicativo em sua configuração da API do Google Drive no portal do Azure.

## Consulte as APIs REST

Referência da [API REST do Google Drive](../connectors/connectors-create-api-googledrive.md).

## Resumo e próximas etapas
Neste tópico, foi adicionada a API do Google Drive ao seu PowersApps Enterprise. Em seguida, disponibilize aos usuários acesso à API para que ela possa ser adicionada aos seus aplicativos:

[Adicione uma conexão e forneça acesso aos usuários](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-googledrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-googledrive/add-api.PNG
[3]: ./media/powerapps-create-api-googledrive/select-googledrive-api.PNG
[4]: ./media/powerapps-create-api-googledrive/configure-googledrive-api.PNG
[5]: https://console.developers.google.com/
[6]: ./media/powerapps-create-api-googledrive/google-developers-console.PNG
[7]: ./media/powerapps-create-api-googledrive/googledrive-create-project.PNG
[8]: ./media/powerapps-create-api-googledrive/use-google-apis.PNG
[9]: ./media/powerapps-create-api-googledrive/googledrive-api-overview.PNG
[10]: ./media/powerapps-create-api-googledrive/enable-googledrive-api.PNG
[11]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials.PNG
[12]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials-add.PNG
[13]: ./media/powerapps-create-api-googledrive/configure-consent-screen.PNG
[14]: ./media/powerapps-create-api-googledrive/create-client-id.PNG
[15]: ./media/powerapps-create-api-googledrive/browseall.png
[16]: ./media/powerapps-create-api-googledrive/allresources.png

<!-----------HONumber=AcomDC_0330_2016--->