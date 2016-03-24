<properties
	pageTitle="Adicione a API do SharePoint Server no PowerApps Enterprise | Microsoft Azure"
	description="Crie ou configure uma nova API do SharePoint Server no ambiente de serviço de aplicativo da sua organização"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
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

# Crie uma nova API do SharePoint Server no PowerApps Enterprise

Adicione A API do SharePoint Server ao ambiente de serviço de aplicativo (locatário) da sua organização.

## Crie a API no portal do Azure

1. No [Portal do Azure](https://portal.azure.com/), entre com sua conta de trabalho. Por exemplo, entre com *Seunomedeusuário*@* SuaEmpresa*.com. Quando fizer isso, entrará automaticamente na assinatura de sua empresa.
 
2. Selecione **Procurar** na barra de tarefas: ![][14]

3. Na lista, você pode rolar a tela para localizar PowerApps ou digite *powerapps*: ![][15]

4. Em **PowerApps**, selecione **Gerenciar APIs**: ![Navegue até as APIs registradas][5]

5. Em **Gerenciar APIs**, selecione **Adicionar** para adicionar a nova API: ![Adicionar API][6]

6. Insira um **nome** descritivo para sua API.
7. Em **Fonte**, selecione as **APIs disponíveis** para selecionar as APIs criadas previamente e selecione **SharePoint Online**. 
8. Selecione **Configurações - definir as configurações necessárias**.
9. Insira a *Id do cliente* e a *chave do aplicativo* para o Active Directory do Azure (aplicativo AAD) do SharePoint Server, a *URL do SharePoint* e a *ID de recurso* do aplicativo de proxy AAD. Siga as etapas descritas na seção a seguir para configurar a conectividade com seu SharePoint Server local.  

	> [AZURE.IMPORTANT] Copie a **URL de Redirecionamento**. Talvez esse valor seja necessário neste tópico posteriormente.
	
10. Selecione **OK** para concluir as etapas.

Quando terminar, uma nova API de servidor do SharePoint Server é adicionada ao seu ambiente de serviço de aplicativo.


## Configure a conectividade com um SharePoint Server local

O SharePoint Server usa o Active Directory para autenticação do usuário. As APIs nos ambientes de aplicativo de serviço são autenticadas usando o Active Directory do Azure (AAD). Você precisa trocar o token de usuário do AAD e convertê-lo para o token do AD. Esse token do AD pode, então, ser usado para se conectar ao serviço local.

O [Proxy de aplicativo do azure (AAD Proxy)](../active-directory/active-directory-application-proxy-publish.md) é usado para esse requisito. Ele é um serviço do Azure no GA e protege o acesso remoto e SSO para aplicativos locais da web. As etapas para habilitar o Proxy do AAD estão bem documentadas na MSDN. Em um nível alto, as etapas abrangem:

1. [Habilitar os serviços de proxy do aplicativo](../active-directory/active-directory-application-proxy-enable.md) – Isso inclui:  

	- Habilitar o Proxy de aplicativo no Azure AD
	- Instalar e registrar o conector de Proxy de aplicativo do Azure

2. [Publicar aplicativos com proxy de aplicativo](../active-directory/active-directory-application-proxy-publish.md) – Isso inclui:

	- Publicar um aplicativo de proxy de aplicativo usando o assistente. Observe a URL externa do site de intranet do sharepoint quando o aplicativo de proxy tiver sido criado.
	- Atribuir usuários e grupo ao aplicativo.
	- Insira a configuração avançada, como o SPN (nome da entidade de serviço), que é usada pelo conector de proxy do aplicativo para buscar o token Kerberos local.

Depois que o aplicativo de proxy for criado, você precisará criar outro aplicativo do AAD que delegue ao aplicativo de proxy. Isso é necessário para obter o token de acesso e atualizar o token necessário para o fluxo de autorização. Você pode criar um novo aplicativo do AAD seguindo [estas instruções](../active-directory/active-directory-integrating-applications.md).

## Resumo e próximas etapas
Neste tópico, você adicionou a API do Outlook do Office 365 ao PowersApps Enterprise. Em seguida, disponibilize aos usuários acesso à API para que ela possa ser adicionada aos seus aplicativos:

[Adicione uma conexão e forneça acesso aos usuários](powerapps-manage-api-connection-user-access.md)


<!--References-->
[2]: https://msdn.microsoft.com/library/azure/dn768219.aspx
[3]: https://msdn.microsoft.com/library/azure/dn768214.aspx
[4]: https://msdn.microsoft.com/library/azure/dn768220.aspx
[5]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[6]: ./media/powerapps-create-api-dropbox/add-api.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_0309_2016-->