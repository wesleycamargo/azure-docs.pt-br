<properties
	pageTitle="Adicione a API do SharePoint Online no PowerApps Enterprise | Microsoft Azure"
	description="Crie ou configure uma nova API do SharePoint Online no ambiente de serviço de aplicativo da sua organização"
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

# Crie uma nova API do SharePoint Online no PowerApps Enterprise

Adicione A API do SharePoint Online ao ambiente de serviço de aplicativo (locatário) da sua organização.

## Crie a API no portal do Azure

1. No [Portal do Azure](https://portal.azure.com/), entre com sua conta de trabalho. Por exemplo, entre com *Seunomedeusuário*@* SuaEmpresa*.com. Quando fizer isso, entrará automaticamente na assinatura de sua empresa.
 
2. Selecione **Procurar** na barra de tarefas: ![][14]

3. Na lista, você pode rolar a tela para localizar PowerApps ou digite *powerapps*: ![][15]

4. Em **PowerApps**, selecione **Gerenciar APIs**: ![Navegue até as APIs registradas][1]

5. Em **Gerenciar APIs**, selecione **Adicionar** para adicionar a nova API: ![Adicionar API][2]

6. Insira um **nome** descritivo para sua API.
	
7. Em **Fonte**, selecione as **APIs disponíveis** para selecionar as APIs criadas previamente e selecione **SharePoint Online**: ![selecione a API do SharePoint Online][3]

8. Selecione **Configurações - definir as configurações necessárias**: ![defina as configurações de API Online do SharePoint][4]

9. Digite a *ID do cliente* e a *chave do aplicativo* do seu aplicativo do Active Directory do Azure (AAD) do SharePoint Online. Se você não tiver uma, consulte a seção “Registrar um aplicativo do AAD para uso com o PowerApps” neste tópico para criar a ID e os valores secretos necessários.

	> [AZURE.IMPORTANT] Copie a **URL de Redirecionamento**. Talvez esse valor seja necessário neste tópico posteriormente.

10. Selecione **OK** para concluir as etapas.

Quando terminar, uma nova API do SharePoint Online será adicionada ao seu ambiente de serviço de aplicativo.


## Registre um aplicativo do AAD para uso com a API do PowerApps do SharePoint Online

1. Abra o [Portal do Azure][5].

2. Selecione **Navegar** e, em seguida, selecione **Active Directory**:

	> [AZURE.NOTE] Isso abre o Active Directory no portal clássico do Azure.

3. Selecione o nome do locatário da sua instituição: ![Iniciar o Active Directory do Azure][6]

4. Selecione a guia **Aplicativos** e selecione **Adicionar**: ![Aplicativos de locatário do AAD][7]

5. Em **Adicionar aplicativo**:

	a) Insira um **Nome** para seu aplicativo. b) Deixe o tipo de aplicativo como **Web**. c) Selecione **Avançar**.


	![Adicionar aplicativo do AAD - informações do aplicativo][8]

6. Em **Propriedades do aplicativo**:

	a) Digite a **URL DE LOGON** do seu aplicativo. Uma vez que você pretende autenticar com o AAD para PowerApps, defina a URL de logon para \__https://login.windows.net_. b) Insira uma **URI da ID do aplicativo** válida para o seu aplicativo. c) Selecione **OK**.

	![Adicionar aplicativo do AAD - propriedades do aplicativo][9]

7. Após a conclusão bem-sucedida, você será redirecionado para o novo aplicativo do AAD. Selecione **Configurar**: ![Aplicativo Contoso do AAD][10]

8. Defina a **URL de resposta** na seção _OAuth 2_ para a URL de redirecionamento que você recebeu quando adicionou a nova API do SharePont Online no Portal do Azure (neste tópico). Selecione **Adicionar aplicativo**: ![Configure o aplicativo Contoso do AAD][11]

9. Na janela **Permissões para outros aplicativos**, selecione **Office 365 Exchange Online** e, então, selecione **OK**: ![Representante do aplicativo Contoso][12]

10. De volta na página de configuração, observe que o _Office 365 Exchange Online_ é adicionado na lista _Permissão para outros aplicativos_.

11. Selecione **Permissões delegadas** para o _Office 365 Exchange Online_ e selecione as seguintes permissões:

	- Ler e gravar itens em todos os conjuntos de sites
	- Ler e gravar itens e relacioná-los em todos os conjuntos de sites

	![Permissões de representante do aplicativo Contoso][13]

Um novo aplicativo do Active Directory do Azure é criado. Você pode usar esse aplicativo em sua configuração da API do SharePoint Online no portal do Azure.

## Resumo e próximas etapas
Neste tópico, você adicionou a API do SharePoint Online para o seu PowersApps Enterprise. Em seguida, forneça aos usuários acesso à API para que ela possa ser adicionada aos seus aplicativos:

[Adicione uma conexão e forneça acesso aos usuários](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-sharepointonline/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-sharepointonline/add-api.PNG
[3]: ./media/powerapps-create-api-sharepointonline/select-sharepointonline-api.PNG
[4]: ./media/powerapps-create-api-sharepointonline/configure-sharepointonline-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-sharepointonline/launch-aad.PNG
[7]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-configure.PNG
[12]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline.PNG
[13]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline-permissions.PNG
[14]: ./media/powerapps-create-api-sharepointonline/browseall.png
[15]: ./media/powerapps-create-api-sharepointonline/allresources.png

<!---HONumber=AcomDC_0309_2016-->