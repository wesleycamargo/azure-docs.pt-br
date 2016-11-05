---
title: Usar o SDK do .NET para acessar as APIs de serviço do Azure Mobile Engagement
description: Descreve como usar o SDK do Mobile Engagement para .NET para acessar as APIs de serviço do Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo

---
# Usar o SDK do .NET para acessar as APIs de serviço do Azure Mobile Engagement
O Azure Mobile Engagement expõe um conjunto de APIs para gerenciar Dispositivos, Campanhas de Envio/Alcance, etc. Para interagir com essas APIs, também oferecemos um [arquivo Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que você pode usar com as ferramentas para gerar SDKs para seu idioma preferencial. Recomendamos usar a ferramenta [AutoRest](https://github.com/Azure/AutoRest) para gerar o SDK do nosso arquivo de Swagger.

Criamos um SDK do .NET de forma semelhante que permite que você interaja com essas APIs usando um wrapper C#. Você não precisará fazer a negociação do token de autenticação e a atualização por conta própria.

Este exemplo percorre o conjunto de etapas a seguir para usar o SDK do .NET:

1. Em primeiro lugar, você precisa configurar a autenticação para suas APIs usando o Azure Active Directory, conforme descrito [aqui](mobile-engagement-api-authentication.md#authentication). No final destas etapas, você deve ter um uma **SubscriptionId**, uma **TenantId**, uma **ApplicationId** e um **segredo** válidos.
2. Usaremos um aplicativo simples do Console do Windows para demonstrar como trabalhar com o SDK do .NET com o cenário de criação de uma campanha de anúncios. Portanto, abra o Visual Studio e crie um **Aplicativo de Console**.
3. Em seguida, será preciso baixar o SDK do .NET que está disponível como uma **Biblioteca de Gerenciamento do Microsoft Azure Engagement** na galeria do Nuget [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/). Se você estiver instalando o Nuget do Visual Studio, precisará marcar a opção **Incluir pré-lançamento** ao procurar o pacote:
   
    ![][1]
4. No arquivo `Program.cs`, adicione os seguintes namespaces:
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;
5. Em seguida, você precisa definir as seguintes constantes que serão usadas para autenticação e interação com o aplicativo do Mobile Engagement no qual você está criando a campanha de anúncios:
   
        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";
   
        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";
   
        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";
6. Defina a variável `EngagementManagementClient` que usaremos para chamar os métodos do SDK do Mobile Engagement:
   
        static EngagementManagementClient engagementClient; 
7. Adicione o seguinte ao seu método `Main`:
   
        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();
   
                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }
8. Defina o método a seguir que se encarrega de inicializar o `EngagementManagementClient` primeiro autenticando e, em seguida, se associando ao aplicativo do Mobile Engagement no qual você planeja criar a campanha de anúncios:
   
        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
   
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;
   
            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }
   
   > [!IMPORTANT]
   > Observe que você precisa usar o **nome de recurso do aplicativo** conforme definido no portal de gerenciamento do Azure para o parâmetro AppName.
   > 
   > 
9. Por fim, defina o método CreateCampaign que se encarregará de usar o EngagementClient inicializado anteriormente para criar uma campanha simples **AnyTime** e **Notification-only** com um título e a mensagem:
   
        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/pt-BR/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );
   
            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/pt-BR/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }
10. Execute o aplicativo de console e você verá o seguinte sobre a criação bem-sucedida da campanha:
    
    **A Id da campanha “159” foi criada com êxito e está em estado de “rascunho”**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png

<!---HONumber=AcomDC_0824_2016-->