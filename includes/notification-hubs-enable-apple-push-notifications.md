---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2fc4f26f187301ea7a7a1e3051038f75da728547
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125211"
---
## <a name="generate-the-certificate-signing-request-file"></a>Gerar o arquivo de solicitação de assinatura de certificado

O APNS (Serviço de Notificação por Push da Apple) usa certificados para autenticar suas notificações por push. Siga estas instruções para criar o certificado de push necessário para enviar e receber notificações. Para obter mais informações sobre esses conceitos, consulte a documentação oficial do [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Gere o arquivo CSR (Solicitação de Assinatura de Certificado), usado pela Apple para gerar um certificado de push assinado.

1. Em seu Mac, execute a ferramenta de **Acesso do conjunto de chaves**. Ela pode ser aberta da pasta **Utilitários** ou da pasta **Outros** no launch pad.
2. Clique em **Acesso do Conjunto de Chaves**, expanda **Assistente de Certificado** e clique em **Solicitar um Certificado de uma Autoridade de Certificação...**.

    ![Usar o Acesso de conjunto de chaves para solicitar um novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Selecione seu **Endereço de Email de Usuário** e seu **Nome Comum**, verifique se **Salvo em disco** está selecionado e, em seguida, clique em **Continuar**. Deixe o campo **Endereço de Email de CA** em branco, pois ele não é necessário.

    ![Informações de certificado necessárias](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e, em seguida, clique em **Salvar**.

    ![Escolha um nome de arquivo para o certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Essa ação salvará o arquivo CSR no local selecionado; o local padrão está situado na Área de Trabalho. Lembre-se do local escolhido para o arquivo.

Em seguida, você registra seu aplicativo na Apple, habilita as notificações por push e carrega o CSR exportado para criar um certificado de push.

## <a name="register-your-app-for-push-notifications"></a>Registrar seu aplicativo para notificações por push

Para poder enviar notificações por push para um aplicativo iOS, você deverá registrar seu aplicativo na Apple e também registrar-se em notificações por push.  

1. Se você ainda não registrou seu aplicativo, navegue até o [Portal de Provisionamento do iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) no Apple Developer Center, inicie sessão com a sua Apple ID, clique em **Identificadores**, em seguida, clique em **IDs de Aplicativo** e, finalmente, clique no sinal de **+** para registrar um novo aplicativo.

    ![Página de IDs do aplicativo do Portal de Provisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Atualize os três campos a seguir para o novo aplicativo e clique em **Continuar**:

   * **Nome**: Digite um nome descritivo para seu aplicativo na **nome** campo o **descrição de ID do aplicativo** seção.
   * **Identificador de pacote**: Sob o **ID do aplicativo explícita** , digite um **identificador de pacote** no formulário `<Organization Identifier>.<Product Name>` conforme mencionado no [guia de distribuição de aplicativo](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). O *Identificador de Organização* e *Nome do Produto* usados deverão corresponder ao identificador da organização e o nome do produto usados quando você criar seu projeto XCode. Na captura de tela abaixo, *NotificationHubs* é usado como um identificador de organização e *GetStarted* é usado como o nome do produto. Garantir que esse valor corresponda ao valor que você usará em seu projeto XCode permitirá que você use o perfil de publicação correto com XCode.
   * **Notificações por push**: Verifique as **notificações por Push** opção a **serviços de aplicativos** seção.

     ![Formulário para registrar uma nova ID de aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     Essa ação gerará sua ID do Aplicativo e solicitará que você confirme as informações. Clique em **Registrar** para confirmar a nova ID do Aplicativo.

     Depois de clicar em **Registrar**, você verá a tela **Registro concluído**, conforme mostrado na imagem a seguir. Clique em **Concluído**.

     ![Registro da ID do aplicativo concluído mostrando direitos](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

3. Na Central de Desenvolvedores, em IDs de Aplicativo, localize a ID do aplicativo que você criou e clique na respectiva linha.

    ![Lista de IDs do aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Para exibir os detalhes do aplicativo, clique em sua respectiva ID. Clique no botão **Editar** na parte inferior.

    ![Página Editar ID do aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. Role até a parte inferior da tela e clique no botão **Criar Certificado...** na seção **Certificado SSL por Push para Desenvolvimento**.

    ![Botão Criar um certificado para aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    O assistente **Adicionar Certificado de iOS** é exibido.

    > [!NOTE]
    > Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Use o mesmo tipo de certificado ao enviar notificações.

5. Clique em **Escolher Arquivo**, navegue até o local em que salvou o arquivo CSR criado na primeira tarefa e clique em **Gerar**.

    ![Página de upload do certificado gerado CSR](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. Depois que o certificado for criado pelo portal, clique no botão **Baixar** e, em seguida, clique em **Concluído**.

    ![Página de download do certificado gerado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Isso baixa e salva o certificado no seu computador, na pasta **Downloads**.

    ![Localize o arquivo do certificado na pasta de Downloads](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Por padrão, o arquivo baixado, um certificado de desenvolvimento, é denominado **aps_development.cer**.

7. Clique duas vezes no certificado de push baixado, **aps_development.cer**.

    Essa ação instala o novo certificado no conjunto de chaves, conforme mostrado na seguinte imagem:

    ![Lista de certificados de acesso ao conjunto de chaves mostrando o novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > O nome em seu certificado pode ser diferente, mas ele será prefixado com **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple**.

8. No Acesso ao Conjunto de Chaves, clique com o botão direito do mouse no novo certificado push criado na categoria **Certificados** . Clique em **Exportar**, nomeie o arquivo, selecione o formato **.p12** e clique em **Salvar**.

    ![Exportar o certificado como formato p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Anote o nome do arquivo e o local do certificado .p12 exportado. Ele é usado para habilitar a autenticação com APNS.

    > [!NOTE]
    > Este tutorial cria um arquivo QuickStart.p12. O nome do arquivo e o local podem ser diferentes.

## <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de provisionamento para o aplicativo

1. De volta ao [Portal de Provisionamento do iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecione **Perfis de Provisionamento**, escolha **Tudo** e clique no botão **+** (mais) para criar um novo perfil. É exibido o assistente **Adicionar Perfil de Provisionamento do iOS**:

    ![Lista de perfil de provisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento, e clique em **Continuar**.

3. Em seguida, selecione a ID do aplicativo que você criou na lista suspensa **ID do Aplicativo** e clique em **Continuar**

    ![Selecionar a ID do aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. Na tela **Selecionar certificados**, selecione seu certificado de desenvolvimento normal usado por assinatura de código e clique em **Continuar**. Esse certificado não é o certificado push que você criou.

    ![Selecionar o certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. Em seguida, selecione os **Dispositivos** a serem usados no teste e clique em **Continuar**

    ![Selecionar os dispositivos](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. Por fim, escolha um nome para o perfil em **Nome do Perfil** e clique em **Gerar**.

    ![Escolher um nome do perfil de provisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

7. Quando o novo perfil de provisionamento for criado, clique para baixá-lo e instalá-lo em seu computador de desenvolvimento do Xcode. Em seguida, clique em **Concluído**.

    ![Baixar o perfil de provisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
