---
title: Registrar-se para autenticação Microsoft | Microsoft Docs
description: Saiba como registrar-se para autenticação da Microsoft em seu aplicativo de serviços móveis do Azure.
author: ggailey777
services: mobile-services
documentationcenter: Mobile
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Registrar seu aplicativo para usar a conta da Microsoft para autenticação
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[!INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

## Visão geral
Este tópico mostra como registrar seu aplicativo móvel para poder usar a conta da Microsoft como um provedor de identidade com os Serviços Móveis do Azure. As mesmas etapas servem tanto para a autenticação orientada a serviços quanto para a direcionada a clientes usando o Live SDK.

## Registrar seu aplicativo da Windows Store no Centro de Desenvolvimento do Windows
Os aplicativos da Windows Store devem ser registrados primeiro no Centro de Desenvolvimento do Windows. Com o registro, seu aplicativo do Windows poderá usar comportamentos de logon único.

> [!NOTE]
> Windows Phone 8, Windows Phone 8.1 Silverlight e aplicativos que não são Windows podem ignorar esta seção.
> 
> 

1. Se você ainda não registrou seu aplicativo, navegue até a [Central de Desenvolvimento do Windows](https://dev.windows.com/dashboard/Application/New), faça logon com sua conta da Microsoft, digite um nome para seu aplicativo e clique em **Reservar um nome do aplicativo**.
2. Abra seu projeto de aplicativo do Windows no Visual Studio. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto de aplicativo da Windows Store e clique em **Loja** > **Associar aplicativo à Loja...**.
   
      ![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-store-association.png)
3. No assistente, clique em **Entrar** e entre com sua conta da Microsoft, selecione o nome do aplicativo que você reservou e clique em **Avançar** > **Associar**.
4. (Opcional) Para um aplicativo do Windows 8.1 universal, repita as etapas 4 e 5 para o projeto da Windows Phone Store.
5. De volta à página do Centro de Desenvolvimento do Windows do seu novo aplicativo, clique em **Serviços** > **Notificações por push**.
6. Na página **Notificações por push**, clique em **site dos Live Services** em **WNS (Serviços de Notificação por Push do Windows) e Serviços Móveis do Microsoft Azure**.
   
    Isso exibe a página de configurações da conta da Microsoft de seu aplicativo.
7. Anote o valor de **SID de pacote**. É possível salvar esse SID no portal do Azure para habilitar o logon único e as notificações por push para seu aplicativo do Windows.

Em seguida, você vai configurar a autenticação da conta da Microsoft para aplicativos do Windows, começando com a etapa 4 na próxima seção.

## Configurar o registro de conta da Microsoft e conectar-se aos Serviços Móveis
Se já tiver registrado seu aplicativo do Windows na seção anterior, ignore esta etapa e vá para a etapa 2.

1. Para um aplicativo que não seja da Windows Store, navegue até a página [Meus Aplicativos](http://go.microsoft.com/fwlink/p/?LinkId=262039) na conta da Central de desenvolvedores da Microsoft, faça logon com sua conta da Microsoft (se necessário), clique em **Criar aplicativo**, digite um **Nome de aplicativo** e clique em **Aceito**.
   
       Assim você reserva o nome do aplicativo com a conta da Microsoft e exibe a página da conta da Microsoft para o seu aplicativo.
2. Na página da conta da Microsoft do seu aplicativo, clique em **Configurações da API**, habilite **Aplicativo cliente móvel ou de área de trabalho** e defina a URL de serviço móvel como o **Domínio de destino**; em seguida, forneça um dos seguintes formatos de URL em **URL de Redirecionamento** e clique em **Salvar**:
   
   * **Back-end do .NET**: `https://<mobile_service>.azure-mobile.net/signin-microsoft`
   * **Back-end do JavaScript**: `https://<mobile_service>.azure-mobile.net/login/microsoftaccount`
     
     > [!NOTE]
     > Certifique-se de que você use o formato de caminho da URL de redirecionamento correto para seu tipo de back-end de Serviços Móveis. Quando isso estiver incorreto, a autenticação não terá êxito. O **Domínio raiz** deve ser preenchido automaticamente. &nbsp;
     > 
     > 
     
     ![Configurações de API da conta da Microsoft](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png)
3. Clique em **Configurações da API** e anote os valores da **ID do cliente**, do **Segredo do cliente** e do **SID do pacote**.
   
       ![Configurações de aplicativo da conta da Microsoft](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png)

    > [AZURE.NOTE] O segredo do cliente é uma credencial de segurança importante. Não compartilhe o segredo do cliente com ninguém nem o distribua com seu aplicativo. Somente os registros de aplicativo Windows Store verão um campo de SID do pacote.

1. No [Portal clássico do Azure], clique na guia **Identidade** do serviço móvel, insira a ID do cliente, o segredo do cliente e o SID do pacote obtido de seu provedor de identidade e clique em **Salvar**.
   
   > [!NOTE]
   > Você não precisa fornecer um valor de SID de pacote para um Windows Phone 8, um Windows Phone Store 8.1 Silverlight ou um aplicativo não Windows.
   > 
   > 

O serviço móvel e o seu aplicativo agora estão configurados para trabalhar com a conta da MIcrosoft.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Portal clássico do Azure]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0727_2016-->