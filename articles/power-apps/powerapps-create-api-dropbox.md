<properties
	pageTitle="Adicione a API do Dropbox ao PowerApps Enterprise | Microsoft Azure"
	description="Crie ou configure uma nova API do Dropbox no ambiente de serviço de aplicativo da sua organização"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="litran"/>

# Criar uma nova API do Dropbox no PowerApps Enterprise

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/create-api-dropbox.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-dropbox.md)

Adicione a API do Dropbox ao ambiente de serviço de aplicativo (locatário) da sua organização.

## Crie a API no portal do Azure

1. No [Portal do Azure](https://portal.azure.com/), entre com sua conta de trabalho. Por exemplo, entre com *Seunomedeusuário*@* SuaEmpresa*.com. Quando fizer isso, entrará automaticamente na assinatura de sua empresa.
 
2. Selecione **Procurar** na barra de tarefas: ![][12]

3. Na lista, você pode rolar a tela para localizar PowerApps ou digite *powerapps*: ![][13]

4. Em **PowerApps**, selecione **Gerenciar APIs**: ![Navegue até as APIs registradas][4]

5. Em **Gerenciar APIs**, selecione **Adicionar** para adicionar a nova API: ![Adicionar API][5]

6. Insira um **nome** descritivo para sua API.
	
7. Em **Fonte**, selecione **as APIs disponíveis** para selecionar as APIs criadas previamente e, em seguida, selecione **Dropbox**: ![selecione api do dropbox][6]

8. Selecione **Configurações - Definir as configurações necessárias**: ![defina as configurações do API do dropbox][7]

9. Insira os valores da **Chave do Aplicativo** e do **Segredo do Aplicativo** do seu aplicativo Dropbox. Se você não tiver um, consulte a seção "Registrar um aplicativo do Dropbox para uso com o PowerApps" neste tópico para criar a chave e os valores secretos necessários.

	> [AZURE.IMPORTANT] Copie a **URL de Redirecionamento**. Talvez esse valor seja necessário neste tópico posteriormente.

10. Selecione **OK** para concluir as etapas.


Quando terminar, uma nova API do Dropbox será adicionada ao seu ambiente de serviço de aplicativo.


## Opcional: registre um aplicativo do Dropbox para uso com PowerApps

Se não tiver um aplicativo do Dropbox existente com a chave e valores do segredo, então use as seguintes etapas para criar o aplicativo e obter os valores necessários.

1. Vá para [Dropbox][1] e entre com sua conta.

2. Vá para o site do desenvolvedor do Dropbox e selecione **Meus aplicativos**: ![Site de desenvolvedor do Dropbox][8]

3. Selecione **Criar**: ![aplicativo de criação de Dropbox][9]

4. Em **Criar um novo aplicativo na plataforma Dropbox**:

	1. Em **Escolher API**, selecione **API do Dropbox**.  
	2. Em **Escolher o tipo de acesso necessário**, escolha **Dropbox Completo...**.  
	3. Digite um nome para seu aplicativo  

	![aplicativo de criação de Dropbox página 1][10]

5. Na página de configurações do aplicativo:

	1. Em **OAuth 2**, defina a **URL de Redirecionamento** para a URL de redirecionamento que você recebeu ao adicionar a nova API do Dropbox no Portal do Azure (neste tópico). Selecione **Adicionar**.  
	2. Selecione o link **Mostrar** para revelar o **segredo do aplicativo**:  

	![aplicativo de criação de Dropbox página 2][11]

É criado um novo aplicativo Dropbox. Você pode usar esse aplicativo em sua configuração da API do Dropbox no portal do Azure.

## Consulte as APIs REST

Referência da [API REST do Dropbox](../connectors/create-api-dropbox.md).


## Resumo e próximas etapas
Neste tópico, você adicionou a API do Dropbox ao seu PowersApps Enterprise. Em seguida, disponibilize aos usuários acesso à API para que ela possa ser adicionada aos seus aplicativos:

[Adicione uma conexão e forneça acesso aos usuários](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[4]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[5]: ./media/powerapps-create-api-dropbox/add-api.PNG
[6]: ./media/powerapps-create-api-dropbox/select-dropbox-api.PNG
[7]: ./media/powerapps-create-api-dropbox/configure-dropbox-api.PNG
[8]: ./media/powerapps-create-api-dropbox/dropbox-developer-site.PNG
[9]: ./media/powerapps-create-api-dropbox/dropbox-create-app.PNG
[10]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page1.PNG
[11]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page2.PNG


[12]: ./media/powerapps-create-api-dropbox/browseall.png
[13]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_0309_2016-->