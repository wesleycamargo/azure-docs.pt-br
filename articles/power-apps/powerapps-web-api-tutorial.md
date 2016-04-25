<properties
	pageTitle="Tutorial: Criar uma API personalizada usando um aplicativo Web ASP.NET no PowerApps e em Fluxos Lógicos | Microsoft Azure"
	description="Tutorial do aplicativo Web ASP.NET para criar uma API personalizada no PowerApps e em Fluxos Lógicos"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="04/12/2016"
   ms.author="mandia"/>

# Tutorial: Criar uma API Web personalizada protegida pelo AAD para PowerApps e Fluxos Lógicos

Este tutorial mostra como criar uma API Web ASP.NET, hospedá-la nos Aplicativos Web do Azure, habilitar a autenticação AAD (por meio da autenticação fácil) e registrar a API Web no PowerApps e em Fluxos Lógicos.

## Para começar, você precisa do seguinte:

* Uma assinatura do Azure
* Uma conta do PowerApps
* Visual Studio 2013 ou superior

## Etapa 1: Criar uma API Web e implantá-la no Azure
1. Abra o Visual Studio e crie um novo aplicativo Web do ASP.NET em C#:
![](./media/powerapps-web-api-tutorial/newwebapp.png "Novo Aplicativo Web")

2. Na próxima tela, selecione o modelo de API Web e **Sem Autenticação**:
![](./media/powerapps-web-api-tutorial/noauth.png "Sem Autorização")

	>[AZURE.IMPORTANT] Certifique-se de definir a autenticação como “Sem Autenticação”.

3. Quando o projeto é criado, você precisa criar a API Web para seus recursos. Neste tutorial, não entramos em detalhes sobre a criação de uma API Web.

4. Em seguida, gere um arquivo do Swagger para a API Web. É possível fazer isso com facilidade abrindo o __Console do Gerenciador de Pacotes__ e instalando o __Swashbuckle__:
![](./media/powerapps-web-api-tutorial/swashbuckle-console.png "Console do Swashbuckle")

5. Assim que for instalado e habilitado, procure os seguintes documentos do Swagger e pontos de extremidade da interface do usuário, respectivamente: **<a-URL-raiz>/swagger/docs/v1**

 	**<a-URL-raiz>/swagger**

6. Quando estiver familiarizado com a API Web, publique-a no Azure. Para publicar no Visual Studio, vá para **COMPILAÇÃO** e selecione **PUBLICAR**.

7. Extraia o JSON do Swagger navegando até ***https://\<URL-do-aplicativo-Web-do-Azure>/swagger/docs/v1***.

	> [AZURE.IMPORTANT] Um documento do Swagger com ID(s) de operação duplicada(s) é inválido. Se você estiver usando o modelo do C# de exemplo, a ID da operação “Values\_Get” será repetida duas vezes. Altere uma instância para “Value\_Get”.


É possível baixar o Swagger utilizado neste tutorial [aqui][6]. Não se esqueça de substituir e remover os comentários antes de usá-lo. Os comentários começam com `//`.

## Etapa 2: Configurar a autenticação AAD

Neste tutorial, pressupomos que você saiba como criar um aplicativo AAD no Azure. Para saber mais sobre como criar um aplicativo AAD, leia o [tutorial do Azure Resource Manager](powerapps-azure-resource-manager-tutorial.md). Precisamos de dois aplicativos AAD para este tutorial.

1. O primeiro aplicativo AAD é usado para proteger a API Web. Nomeie-o **webAPI**.
2. O segundo aplicativo AAD é usado para proteger o registro da API personalizada e adquirir acesso delegado à API Web protegida pelo primeiro aplicativo. Nomeie-a **webAPI-customAPI**.
3. Para **webAPI**, use a seguinte configuração:  

  1. URL de entrada: ***https://login.windows.net***
  2. URI da ID do aplicativo: ***https://\<a-URL-raiz>*** (normalmente, a URL do site implantado no Azure)
  3. URL de resposta: ***https://\<a-URL-raiz>/.auth/login/aad/callback***  
  
	>[AZURE.IMPORTANT] Você precisará da ID do Cliente desse aplicativo mais tarde, portanto, anote-a.

4. Para **webAPI-customAPI**, use a seguinte configuração:
  
  1. URL de logon: **https://login.windows.net**
  2. URI da ID do aplicativo: ***pode ser qualquer URL exclusiva***
  3. URL de resposta: ***https://msmanaged-na.consent.azure-apim.net/redirect***
  4. Adicione permissões para ter acesso delegado à API Web.
  5. Você também precisará da ID do Cliente desse aplicativo mais tarde, portanto, anote-a.
  6. Gere uma chave e a armazene em um lugar seguro. Precisaremos dessa chave mais tarde.

>[AZURE.IMPORTANT] Ambos os aplicativos devem estar no mesmo diretório.

## Etapa 3: Configurar a autenticação fácil no aplicativo Web

1. Entre no [portal do Azure](https://portal.azure.com) e vá para o aplicativo Web implantado na **Etapa 1** (neste tópico).
2. Em **Configurações**, selecione **“Autenticação/Autorização”**.
3. Ative a **Autenticação do Serviço de Aplicativo** e selecione **Azure Active Directory**. Na próxima folha, selecione **Expressa**.  
4. Clique em **Selecionar Aplicativo AD Existente** e selecione o primeiro aplicativo AAD criado como parte da Etapa 2. Nesse caso, selecione **webAPI**.

Isso deve configurar a autenticação AAD para o aplicativo Web.

## Etapa 4: Configurar a API Personalizada 

1. Precisamos modificar nosso Swagger um pouco para inserir o objeto `securityDefintions` e a autenticação AAD usada para o aplicativo Web. Adicione as seguintes linhas de código: 

	```javascript
  "host": "<your-root-url>",
  "schemes": [
    "https"						//Change scheme to https 
  ],
  "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
  },
	```

2. Vá para o [portal da Web][1] do PowerApps e adicione uma API personalizada. [Use Custom APIs in Logic Flows and PowerApps](powerapps-register-custom-api.md) (Usar APIs Personalizadas em Fluxos Lógicos e PowerApps) lista as etapas.

3. Assim de carregar o Swagger, o assistente detectará automaticamente que a autenticação AAD está sendo usada para a API Web.

4. Configure a autenticação AAD para a API personalizada:

  1. ID do Cliente: ***ID do Cliente de webAPI-CustomAPI*** de 4.5 na **Etapa 2** (neste tópico)
  2. Segredo: ***Chave de webPI-CustomAPI*** de 4.6 na **Etapa 2** (neste tópico)
  3. URL de logon: ***https://login.windows.net***
  4. URI do Recurso: ***ID do Cliente de webAPI*** de 3.4 na **Etapa 2** (neste tópico)

5. Selecione **Criar** e tentar criar uma conexão na API Personalizada. Se tudo estiver configurado corretamente, você poderá entrar com êxito.

Para obter uma experiência mais detalhada sobre como criar PowerApps e Fluxos Lógicos, veja o seguinte:

- [Connect to Office 365, Twitter, and Microsoft Translator][5] (Conectar-se ao Office 365, Twitter e Microsoft Translator)
- [Show data from Office 365][4] (Mostrar dados do Office 365)
- [Create an app from data][3] (Criar um aplicativo com base em dados)
- [Get started with logic flows][2] (Introdução aos fluxos lógicos)

Em caso de dúvidas ou comentários, envie um email para [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).

<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: http://pwrappssamples.blob.core.windows.net/samples/webAPI.json

<!---HONumber=AcomDC_0413_2016-->