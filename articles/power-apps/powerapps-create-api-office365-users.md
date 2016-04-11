<properties
	pageTitle="Adicione a API dos Usuários do Office 365 ao PowerApps Enterprise | Microsoft Azure"
	description="Crie ou configure uma nova API dos Usuários do Office 365 no ambiente de serviço de aplicativo da sua organização"
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

# Criar uma nova API de usuários do Office 365 no PowerApps Enterprise

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/connectors-create-api-office365-users.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-users.md)

Adicione a API de Usuários do Office 365 ao ambiente de serviço de aplicativo (locatário) da sua organização.

## Crie a API no portal do Azure

1. No [Portal do Azure](https://portal.azure.com/), entre com sua conta de trabalho. Por exemplo, entre com *Seunomedeusuário*@*SuaEmpresa*.com. Quando fizer isso, entrará automaticamente na assinatura de sua empresa.
 
2. Selecione **Procurar** na barra de tarefas:  
![][14]

3. Na lista, você pode usar a barra de rolagem para localizar PowerApps ou digitar *powerapps*:  
![][15]  

4. Em **PowerApps**, selecione **Gerenciar APIs**:    
![Navegue até as APIs registradas][1]

5. Em **Gerenciar APIs**, selecione **Adicionar** para adicionar a nova API:  
![Adicionar API][2]

6. Insira um **nome** descritivo para sua API.  
	
7. Em **Fonte**, selecione as **APIs disponíveis** para selecionar as APIs criadas previamente e selecione ** Usuários do Office 365**:  
![Selecione api dos Usuários do Office 365][3]

8. Selecione **Configurações - Definir as configurações necessárias**:  
![definir configurações de API dos Usuários do Office 365][4]

9. Digite a *ID do cliente* e a *Chave do Cliente* do seu aplicativo do Active Directory do Azure (AAD) do Office 365. Se você não tiver uma, consulte a seção “Registrar um aplicativo do AAD para uso com o PowerApps” neste tópico para criar a ID e os valores secretos necessários.  

	> [AZURE.IMPORTANT] Copie a **URL de Redirecionamento**. Talvez esse valor seja necessário neste tópico posteriormente.

10. Selecione **OK** para concluir as etapas.

Quando terminar, uma nova API dos Usuários do Office 365 será adicionada ao seu ambiente de serviço de aplicativo.

## Opcional: registre um aplicativo do AAD para uso com a API dos Usuários do PowerApps do Office 365

Se não tiver um aplicativo do AAD existente com a chave e valores do segredo, então use as seguintes etapas para criar o aplicativo e obter os valores necessários.

1. Abra [ Portal do Azure][5].

2. Selecione **Navegar** e, em seguida, selecione **Active Directory**:  

	> [AZURE.NOTE] Isso abre o Active Directory no portal clássico do Azure.

3. Selecione o nome do locatário da sua instituição:  
![Inicie o Active Directory do Azure][6]

4. Selecione a guia **Aplicativos** e selecione **Adicionar**:  
![Aplicativos de locatário do AAD][7]

5. Em **Adicionar aplicativo**:

	1. Insira um **Nome** para seu aplicativo.  
	2. Deixe o tipo de aplicativo como **Web**.  
	3. Selecione **Avançar**.  

	![Adicionar aplicativo do AAD - informações do aplicativo][8]

6. Em **Propriedades do aplicativo**:  

	1. Insira a **URL DE ENTRADA** do seu aplicativo. Uma vez que você se autenticará com o AAD para PowerApps, defina a URL de entrada para \__https://login.windows.net_.  
	2. Insira um **URI DA ID DO APLICATIVO** válido para seu aplicativo.  
	3. Selecione **OK**.  

	![Adicionar aplicativo do AAD - propriedades do aplicativo][9]

7. Após a conclusão bem-sucedida, você será redirecionado para o novo aplicativo do AAD. Selecione **Configurar**:  
![Aplicativo Contoso do AAD][10]

8. Defina a **URL de resposta** na seção _OAuth 2_ para a URL de redirecionamento que você recebeu quando adicionou a nova API dos Usuários do Office 365 no Portal do Azure (neste tópico): Selecione **Adicionar aplicativo**:  
![Configure o aplicativo Contoso do AAD][11]

9. Na janela **Permissões para outros aplicativos**, selecione **API Unificada do Office 365 (Visualização)** e, em seguida, selecione **OK**.

10. De volta na página de configuração, observe que a _API Unificada do Office 365 (Visualização)_ é adicionada na lista _Permissão para outros aplicativos_.

11. Selecione **Permissões Delegadas** para _API Unificada do Office 365 (Visualização)_ e selecione a permissão **Ler os perfis básicos de todos os usuários**.

Um novo aplicativo do Active Directory do Azure é criado. Você pode usar esse aplicativo em sua configuração da API dos Usuários do Office 365 no portal do Azure.

Algumas informações úteis sobre os aplicativos do AAD em [Como e por que os aplicativos são adicionados ao Azure AD](../active-directory/active-directory-how-applications-are-added.md).

## Consulte as APIs REST

Referência da [API REST dos Usuários do Office 365](../connectors/connectors-create-api-office365-users.md).

## Resumo e próximas etapas
Neste tópico, você adicionou a API dos Usuários do Office 365 ao PowersApps Enterprise. Em seguida, disponibilize aos usuários acesso à API para que ela possa ser adicionada aos seus aplicativos:

[Adicione uma conexão e forneça acesso aos usuários](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-office365-users/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-users/add-api.PNG
[3]: ./media/powerapps-create-api-office365-users/select-office365-users-api.PNG
[4]: ./media/powerapps-create-api-office365-users/configure-office365-users-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-users/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0330_2016-->