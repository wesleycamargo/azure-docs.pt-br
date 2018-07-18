---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/11/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 10ccb80dd74606d2ad40ab5d7993aed8cd71725e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36329664"
---
## <a name="generate-the-certificate-signing-request-file"></a>Gerar o arquivo de solicitação de assinatura de certificado
O APNS (Serviço de Notificação por Push da Apple) usa certificados para autenticar suas notificações por push. Siga estas instruções para criar o certificado de push necessário para enviar e receber notificações. Para obter mais informações sobre esses conceitos, consulte a documentação oficial do [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Gere o arquivo CSR (Solicitação de Assinatura de Certificado), usado pela Apple para gerar um certificado de push assinado.

1. Em seu Mac, execute a ferramenta Acesso do Conjunto de Chaves. Ela pode ser aberta da pasta **Utilitários** ou da pasta **Outros** no launch pad.
2. Clique em **Acesso do Conjunto de Chaves**, expanda **Assistente de Certificado** e clique em **Solicitar um Certificado de uma Autoridade de Certificação...**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Selecione seu **Endereço de Email de Usuário** e seu **Nome Comum**, verifique se **Salvo em disco** está selecionado e, em seguida, clique em **Continuar**. Deixe o campo **Endereço de Email de CA** em branco, pois ele não é necessário.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e, em seguida, clique em **Salvar**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      Essa ação salvará o arquivo CSR no local selecionado; o local padrão está situado na Área de Trabalho. Lembre-se do local escolhido para o arquivo.

Em seguida, você registra seu aplicativo na Apple, habilita as notificações por push e carrega o CSR exportado para criar um certificado de push.

## <a name="register-your-app-for-push-notifications"></a>Registrar seu aplicativo para notificações por push
Para poder enviar notificações por push para um aplicativo iOS, você deverá registrar seu aplicativo na Apple e também registrar-se em notificações por push.  

1. Se você ainda não registrou seu aplicativo, navegue até o <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a> no Apple Developer Center, inicie sessão com a sua Apple ID, clique em **Identificadores**, em seguida, clique em **IDs de Aplicativo** e, finalmente, clique no sinal de **+** para registrar um novo aplicativo.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)
      
2. Atualize os três campos a seguir para o novo aplicativo e clique em **Continuar**:
   
   * **Nome**: digite um nome descritivo para o aplicativo no campo **Nome**, na seção **Descrição de ID do Aplicativo**.
   * **Identificador de Pacote**: na seção **ID do Aplicativo Explícita**, digite um **Identificador de Pacote** no formato `<Organization Identifier>.<Product Name>`, como mencionado no [Guia de Distribuição de Aplicativo](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). O *Identificador de Organização* e *Nome do Produto* usados deverão corresponder ao identificador da organização e o nome do produto usados quando você criar seu projeto XCode. Na captura de tela abaixo, *NotificationHubs* é usado como um identificador de organização e *GetStarted* é usado como o nome do produto. Garantir que esse valor corresponda ao valor que você usará em seu projeto XCode permitirá que você use o perfil de publicação correto com XCode. 
   * **Notificações por Push**: marque a opção **Notificações por Push** na seção **Serviços de Aplicativos**.
     
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
     
      Essa ação gerará sua ID do Aplicativo e solicitará que você confirme as informações. Clique em **Registrar** para confirmar a nova ID do Aplicativo.
     
      Depois de clicar em **Registrar**, você verá a tela **Registro concluído**, conforme mostrado na imagem a seguir. Clique em **Concluído**.
      
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


1. Na Central de Desenvolvedores, em IDs de Aplicativo, localize a ID do aplicativo que você criou e clique na respectiva linha.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)
   
      Para exibir os detalhes do aplicativo, clique em sua respectiva ID. Clique no botão **Editar** na parte inferior.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)
      
2. Role até a parte inferior da tela e clique no botão **Criar Certificado...** na seção **Certificado SSL por Push para Desenvolvimento**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
      O assistente "Adicionar Certificado de iOS" é exibido.
   
   > [!NOTE]
   > Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Use o mesmo tipo de certificado ao enviar notificações.
   > 
   > 
3. Clique em **Escolher Arquivo**, navegue até o local em que salvou o arquivo CSR criado na primeira tarefa e clique em **Gerar**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
4. Depois que o certificado for criado pelo portal, clique no botão **Baixar** e, em seguida, clique em **Concluído**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
      Ele baixa e salva o certificado no seu computador na pasta Downloads.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > Por padrão, o arquivo baixado, um certificado de desenvolvimento, é denominado **aps_development.cer**.
   > 
   > 
5. Clique duas vezes no certificado de push baixado, **aps_development.cer**.
   
      Essa ação instala o novo certificado no conjunto de chaves, conforme mostrado na seguinte imagem:
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > O nome em seu certificado pode ser diferente, mas ele será prefixado com **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple**.   
6. No Acesso ao Conjunto de Chaves, clique com o botão direito do mouse no novo certificado push criado na categoria **Certificados** . Clique em **Exportar**, nomeie o arquivo, selecione o formato **.p12** e clique em **Salvar**.
   
    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)
   
    Anote o nome do arquivo e o local do certificado .p12 exportado. Ele é usado para habilitar a autenticação com APNS.
   
   > [!NOTE]
   > Este tutorial cria um arquivo QuickStart.p12. O nome do arquivo e o local podem ser diferentes.
   
## <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de provisionamento para o aplicativo
1. De volta ao <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a>, selecione **Perfis de Provisionamento**, escolha **Tudo** e clique no botão **+** para criar um novo perfil. É exibido o Assistente **Adicionar Perfil de Provisionamento do iOS**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento, e clique em **Continuar**. 
3. Em seguida, selecione a ID do aplicativo que você criou na lista suspensa **ID do Aplicativo** e clique em **Continuar**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. Na tela **Selecionar certificados**, selecione seu certificado de desenvolvimento normal usado por assinatura de código e clique em **Continuar**. Esse certificado não é o certificado push que você criou.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. Em seguida, selecione os **Dispositivos** a serem usados no teste e clique em **Continuar**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. Por fim, escolha um nome para o perfil em **Nome do Perfil** e clique em **Gerar**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
7. Quando o novo perfil de provisionamento for criado, clique para baixá-lo e instalá-lo em seu computador de desenvolvimento do Xcode. Em seguida, clique em **Concluído**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
