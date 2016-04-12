<properties
	pageTitle="Adicione a API do Twitter ao PowerApps Enterprise | Microsoft Azure"
	description="Crie ou configure uma nova API do Twitter no ambiente de serviço de aplicativo da sua organização"
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
   ms.date="03/29/2016"
   ms.author="litran"/>

# Criar uma nova API do Twitter no PowerApps Enterprise

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/connectors-create-api-twitter.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-twitter.md)

Adicione a API do Twitter ao ambiente de serviço de aplicativo (locatário) da sua organização.

## Crie a API no portal do Azure

1. No [Portal do Azure](https://portal.azure.com/), entre com sua conta de trabalho. Por exemplo, entre com *Seunomedeusuário*@* SuaEmpresa*.com. Quando fizer isso, entrará automaticamente na assinatura de sua empresa. 

2. Selecione **Procurar** na barra de tarefas: ![][14]

3. Na lista, você pode rolar a tela para localizar PowerApps ou digite *powerapps*: ![][15]

4. Em **PowerApps**, selecione **Gerenciar APIs**: ![Navegue até as APIs registradas][1]

5. Em **Gerenciar APIs**, selecione **Adicionar** para adicionar a nova API: ![Adicionar API][2]

6. Insira um **nome** descritivo para sua API.
	
7. Em **Fonte**, selecione as **APIs disponíveis** para selecionar as APIs criadas previamente e selecione **Twitter**: ![selecione a API do Twitter][3]

8. Selecione **Configurações - definir as configurações necessárias**: ![defina as configurações de API do Twitter][4]

9. Insira a *chave do consumidor* e o *segredo do consumidor* de seu aplicativo do Twitter. Se você não tiver um, consulte a seção “Registrar um aplicativo do Twitter para uso com o PowerApps” neste tópico para criar a chave e os valores secretos necessários.

	> [AZURE.IMPORTANT] Copie a **URL de Redirecionamento**. Talvez esse valor seja necessário neste tópico posteriormente.

10. Selecione **OK** para concluir as etapas.

Quando terminar, uma nova API do Twitter será adicionada ao seu ambiente de serviço de aplicativo.


## Opcional: registre um aplicativo do Twitter para uso com PowerApps

Se você não tiver um aplicativo do Twitter existente com a chave e valores do segredo, então use as seguintes etapas para criar o aplicativo e obter os valores necessários.

1. Acesse [https://apps.twitter.com/](https://apps.twitter.com) e entre com sua conta do twitter.

2. Clique em **Criar novo aplicativo**: ![Página de aplicativos do Twitter][6]

3. Em **Criar um aplicativo**:
   
	1. Insira um valor para **Nome**.  
	2. Insira um valor para **Descrição**.  
	3. Insira um valor para **Site**.  
	4. Configure a **URL de retorno de chamada** para a URL de redirecionamento recebida ao adicionar a nova API do Twitter no Portal do Azure (neste tópico).  
	5. Aceitar o contrato de desenvolvedor e selecione **Criar seu aplicativo do Twitter**.  

	![Criação de aplicativo do Twitter][7]

4. Após a criação bem-sucedida do aplicativo, você será redirecionado para a página do aplicativo.

Um novo aplicativo do Twitter é criado. Você pode usar esse aplicativo em sua configuração da API do Twitter no portal do Azure.

## Consulte as APIs REST

Referência da [API REST do Twitter](../connectors/connectors-create-api-twitter.md).


## Resumo e próximas etapas
Neste tópico, você adicionou a API do Twitter para o seu PowersApps Enterprise. Em seguida, forneça aos usuários acesso à API para que ela possa ser adicionada aos seus aplicativos:

[Adicione uma conexão e forneça acesso aos usuários](powerapps-manage-api-connection-user-access.md)


<!--References-->

[1]: ./media/powerapps-create-api-twitter/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-twitter/add-api.PNG
[3]: ./media/powerapps-create-api-twitter/select-twitter-api.PNG
[4]: ./media/powerapps-create-api-twitter/configure-twitter-api.PNG
[6]: ./media/powerapps-create-api-twitter/twitter-apps-page.PNG
[7]: ./media/powerapps-create-api-twitter/twitter-app-create.PNG
[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!-----------HONumber=AcomDC_0330_2016-->