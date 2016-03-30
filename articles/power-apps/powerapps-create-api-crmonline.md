<properties
	pageTitle="Adicione a API do Dynamics CRM Online no PowerApps Enterprise | Microsoft Azure"
	description="Crie ou configure uma nova API do Dynamics CRM Online no ambiente de serviço de aplicativo da sua organização"
	services=""
    suite="powerapps"
	documentationCenter=""
	authors="schabungbam"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/02/2016"
   ms.author="sameerch"/>

# Criar uma nova API do Dynamics CRM Online no PowerApps Enterprise

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/create-api-crmonline.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-crmonline.md)

Adicione a API do Dynamics CRM Online ao ambiente de serviço de aplicativo (locatário) da sua organização.

## Crie a API no portal do Azure

1. No [Portal do Azure](https://portal.azure.com), entre com sua conta de trabalho. Por exemplo, entre com *Seunomedeusuário*@*SuaEmpresa*.com. Quando fizer isso, entrará automaticamente na assinatura de sua empresa.

2. Selecione **Procurar** na barra de tarefas:  
![][1]

3. Na lista, você pode rolar a tela para localizar PowerApps ou digite *powerapps*:  
![][2]

4. Em **PowerApps**, selecione **Gerenciar APIs**:  
![Navegue até as APIs registradas][3]

5. Em **Gerenciar APIs**, selecione **Adicionar** para adicionar a nova API:  
![Adicionar API][4]

6. Insira um **nome** descritivo para sua API.

7. Em **Fonte**, selecione as **APIs disponíveis** para selecionar as APIs criadas previamente e selecione **Dynamics CRM Online**:  
![Selecione a API online do Dynamics CRM][5]

8. Selecione **Configurações - definir as configurações necessárias**:  
![Defina as configurações de API online do Dynamics CRM][6]

9. Digite a **ID do cliente** e a **chave do aplicativo** do seu aplicativo do Dynamics CRM Online do Active Directory do Azure (AAD). Se você não tiver uma, consulte a seção “Registrar um aplicativo do AAD para uso com o PowerApps” neste tópico para criar a ID e os valores secretos necessários.

	> [AZURE.IMPORTANT] Copie a **URL de Redirecionamento**. Talvez esse valor seja necessário neste tópico posteriormente.

10. Selecione **OK** para concluir as etapas.

Quando terminar, uma nova API do Dynamics CRM Online será adicionada ao seu ambiente de serviço de aplicativo.

## Registre um aplicativo do AAD para uso com a API online do PowerApps Dynamics CRM

1. Abra o [Portal do Azure](https://portal.azure.com).

2. Selecione **Navegar** e, em seguida, selecione **Active Directory**:

	> [AZURE.NOTE] Isso abre o Active Directory no portal clássico do Azure.

3. Selecione o nome do locatário da sua instituição:  
![Iniciar o Active Directory do Azure][7]

4. Selecione a guia **Aplicativos** e selecione **Adicionar**:  
![Aplicativos de locatário do AAD][8]

5. Em **Adicionar aplicativo**:

	1. Insira um **Nome** para seu aplicativo.  
	2. Deixe o tipo de aplicativo como **Web**.  
	3. Selecione **Avançar**.

	![Adicionar aplicativo do AAD - informações do aplicativo][9]

6. Em **Propriedades do aplicativo**:

	1. Insira a **URL DE ENTRADA** do seu aplicativo. Uma vez que você se autenticará com o AAD para PowerApps, defina a URL de entrada para \__https://login.windows.net_.
	2. Insira um **URI DA ID DO APLICATIVO** válido para seu aplicativo.  
	3. Selecione **OK**.  

	![Adicionar aplicativo do AAD - propriedades do aplicativo][10]

7. Após a conclusão bem-sucedida, você será redirecionado para o novo aplicativo do AAD. Selecione **Configurar**:  
![Aplicativo Contoso do AAD][11]

8. Defina a **URL de resposta** na seção _OAuth 2_ para a URL de redirecionamento que você recebeu quando adicionou a nova API do Dynamics CRM Online no Portal do Azure (neste tópico):  
![Configure o aplicativo Contoso do AAD][12]

9. Selecione **Salvar**.

Um novo aplicativo do Active Directory do Azure é criado. Você pode usar esse aplicativo em sua configuração da API do Dynamics CRM Online no portal do Azure.

## Consulte as APIs REST

Referência da [API REST do Dynamics CRM Online](../connectors/create-api-crmonline.md).


## Resumo e próximas etapas
Neste tópico, você adicionou a API do Dynamics CRM Online para a sua empresa PowersApps. Em seguida, forneça aos usuários acesso à API para que ela possa ser adicionada aos seus aplicativos:

[Adicione uma conexão e forneça acesso aos usuários](powerapps-manage-api-connection-user-access.md)

<!-- References -->

[1]: ./media/powerapps-create-api-crmonline/browseall.png
[2]: ./media/powerapps-create-api-crmonline/allresources.png
[3]: ./media/powerapps-create-api-crmonline/browse-to-registered-apis.PNG
[4]: ./media/powerapps-create-api-crmonline/add-api.PNG
[5]: ./media/powerapps-create-api-crmonline/select-crmonline-api.PNG
[6]: ./media/powerapps-create-api-crmonline/configure-crmonline-settings.PNG
[7]: ./media/powerapps-create-api-crmonline/launch-aad.PNG
[8]: ./media/powerapps-create-api-crmonline/aad-tenant-applications.PNG
[9]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-appinfo.PNG
[10]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-app-properties.PNG
[11]: ./media/powerapps-create-api-crmonline/contoso-aad-app.PNG
[12]: ./media/powerapps-create-api-crmonline/contoso-aad-app-configure.PNG

<!----HONumber=AcomDC_0309_2016-->