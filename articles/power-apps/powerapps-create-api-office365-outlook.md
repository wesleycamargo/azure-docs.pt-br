<properties
	pageTitle="Adicione a API do Outlook do Office 365 ao PowerApps Enterprise | Microsoft Azure"
	description="Crie ou configure uma nova API do Outlook do Office 365 no ambiente de serviço de aplicativo da sua organização"
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

# Criar uma nova API de Outlook do Office 365 no PowerApps Enterprise

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/create-api-office365-outlook.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-outlook.md)

Adicione a API do Office 365 Outlook ao ambiente de serviço de aplicativo (locatário) da sua organização.

## Crie a API no portal do Azure

1. No [Portal do Azure](https://portal.azure.com/), entre com sua conta de trabalho. Por exemplo, entre com *Seunomedeusuário*@* SuaEmpresa*.com. Quando fizer isso, entrará automaticamente na assinatura de sua empresa.
 
2. Selecione **Procurar** na barra de tarefas: ![][14]

3. Na lista, você pode usar a barra de rolagem para localizar o PowerApps ou digitar *powerapps*: ![][15]

4. **PowerApps Services**, selecione **Gerenciar APIs**: ![Navegue até as APIs registradas][1]

5. Em **Gerenciar APIs**, selecione **Adicionar** para adicionar a nova API: ![Adicionar API][2]

6. Insira um **nome** descritivo para sua API.
	
7. Em **Fonte**, selecione as **APIs disponíveis** para selecionar as APIs criadas previamente e selecione ** Outlook do Office 365**: ![selecione a api do Outlook do Office 365][3]

8. Selecione **Configurações - Definir as configurações necessárias**: ![definir configurações de API do Outlook do Office 365][4]

9. Insira o valor da *Chave do aplicativo* e do *Segredo do aplicativo* do seu aplicativo do Active Diretory do Azure (AAD) do Office 365. Se não tiver uma, consulte a seção "Registrar um aplicativo do AAD para uso com o PowerApps" neste tópico para criar a chave e os valores secretos necessários.
 
	> [AZURE.IMPORTANT] Copie a **URL de Redirecionamento**. Talvez esse valor seja necessário neste tópico posteriormente.

10. Selecione **OK** para concluir as etapas.

Quando terminar, uma nova API do Office 365 Outlook será adicionada ao seu ambiente de serviço de aplicativo.


## Opcional: registre um aplicativo do AAD para uso com a API do PowerApps do Office 365

Se não tiver um aplicativo do AAD existente com a chave e valores do segredo, então use as seguintes etapas para criar o aplicativo e obter os valores necessários.

1. Abra o [Portal do Azure][5].

2. Selecione **Navegar** e, em seguida, selecione **Active Directory**.

	>[AZURE.NOTE] Isso abre o Active Directory no portal clássico do Azure.

3. Selecione o nome do locatário da sua instituição: ![Inicie o Active Directory do Azure][6]

4. Selecione a guia **Aplicativos** e selecione **Adicionar**: ![Aplicativos de locatário do AAD][7]

5. Em **Adicionar aplicativo**:

	1. Insira um **Nome** para seu aplicativo.  
	3. Deixe o tipo de aplicativo como **Web**.  
	3. Selecione **Avançar**.  

	![Adicionar aplicativo do AAD - informações do aplicativo][8]

6. Em **Propriedades do aplicativo**:

	1. Insira a **URL DE ENTRADA** do seu aplicativo. Uma vez que você se autenticará com o AAD para PowerApps, defina a URL de entrada para \__https://login.windows.net_.
2. Insira um **URI DA ID DO APLICATIVO** válido para seu aplicativo.  
	3. Selecione **OK**.  

	![Adicionar aplicativo do AAD - propriedades do aplicativo][9]

7. Após a conclusão bem-sucedida, você será redirecionado para o novo aplicativo do AAD. Selecione **Configurar**: ![Aplicativo Contoso do AAD][10]

8. Defina a **URL de resposta** na seção _OAuth 2_ para a URL de redirecionamento que você recebeu quando adicionou a nova API do Outlook do Office 365 no Portal do Azure (neste tópico): Em seguida, selecione **Adicionar aplicativo**: ![Configure o aplicativo Contoso do AAD][11]

9. Na janela **Permissões para outros aplicativos**, selecione **Office 365 Exchange Online** e selecione **OK**: ![Representante do aplicativo Contoso][12]

10. De volta na página de configuração, observe que o _Office 365 Exchange Online_ é adicionado na lista _Permissão para outros aplicativos_.

11. Selecione **Permissões delegadas** para o _Office 365 Exchange Online_ e selecione as seguintes permissões:

	- Ler e gravar contatos do usuário
	- Ler contatos do usuário
	- Ler e gravar calendários do usuário
	- Ler calendários do usuário
	- Enviar email como um usuário
	- ler e gravar e-mail do usuário
	- Leitura de e-mail do usuário

	![Permissões de representante do aplicativo Contoso][13]

Um novo aplicativo do Active Directory do Azure é criado. Você pode usar esse aplicativo em sua configuração da API do Outlook do Office 365 no portal do Azure.

Algumas informações úteis sobre os aplicativos do AAD em [Como e por que os aplicativos são adicionados ao Azure AD](../active-directory/active-directory-how-applications-are-added.md).

## Consulte as APIs REST

Referência da [API REST do office 365 Outlook](../connectors/create-api-office365-outlook.md).


## Resumo e próximas etapas
Neste tópico, você adicionou a API do Outlook do Office 365 ao PowersApps Enterprise. Em seguida, disponibilize aos usuários acesso à API para que ela possa ser adicionada aos seus aplicativos:

[Adicione uma conexão e forneça acesso aos usuários](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-office365-outlook/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-outlook/add-api.PNG
[3]: ./media/powerapps-create-api-office365-outlook/select-office365-outlook-api.PNG
[4]: ./media/powerapps-create-api-office365-outlook/configure-office365-outlook-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-outlook/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-configure.PNG
[12]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.PNG
[13]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_0309_2016-->