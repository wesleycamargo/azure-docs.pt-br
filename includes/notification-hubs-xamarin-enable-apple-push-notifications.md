
Para registrar o aplicativo a fim de obter notificações por push através do serviço APNS (Apple Push Notification Service), você deve criar um certificado push, uma ID para o aplicativo e um Perfil de Provisionamento para o projeto no portal do desenvolvedor da Apple.

A ID do aplicativo inclui as definições de configuração que permitem ao aplicativo enviar e receber notificações por push. Essas configurações incluem o certificado de notificação por push que é necessário para autenticação com APNS quando o aplicativo está enviando e recebendo notificações por push.

Para saber mais sobre esses conceitos, confira a documentação oficial do [Apple Push Notification Service](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) .

## <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>Gerar o arquivo de solicitação de assinatura de certificado do certificado push
Essas etapas vão orientá-lo durante o processo de criar a solicitação de assinatura de certificado, que gera um certificado por push que é usado com o APNS.

1. Em seu Mac, execute a ferramenta Acesso do Conjunto de Chaves. Ela pode ser aberta da pasta **Utilitários** ou da pasta **Outros** no launch pad.

2. Selecione **Acesso do Conjunto de Chaves**, expanda **Assistente de Certificado** e selecione **Solicitar um Certificado de uma Autoridade de Certificação...**.

      ![Solicitar um certificado](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Selecione seu **Endereço de Email do Usuário** e **Nome Comum**.

4. Verifique se **Salvo em disco** está marcado e selecione **Continuar**. Deixe o campo **Endereço de Email de CA** em branco, pois ele não é necessário.

      ![Informações de certificado](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Digite um nome para o arquivo CSR (solicitação de assinatura de certificado ) em **Salvar como**.
5. Selecione o local em **Onde**e selecione **Salvar**.

      ![Salvar a solicitação de assinatura de certificado](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

      Isso salva o arquivo CSR no local selecionado. (O local padrão é a área de trabalho.) Lembre-se do local escolhido para esse arquivo.

## <a name="register-your-app-for-push-notifications"></a>Registrar seu aplicativo para notificações por push
Crie uma nova ID de aplicativo explícita para o aplicativo na Apple e configure-o para receber notificações por push.  

1. Navegue até o [Portal de Provisionamento do iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) no Apple Developer Center e entre com a ID da Apple.

2. Selecione **Identificadores** e **IDs de aplicativo**.

3. Selecione o sinal **+** para registrar um novo aplicativo.

      ![Registrar um novo aplicativo](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

4. Atualize os três campos a seguir para o novo aplicativo e selecione **Continuar**:

   * **Nome**: na seção **Descrição da ID do aplicativo**, digite um nome descritivo para o aplicativo.

   * **Identificador de Pacote**: na seção **ID do Aplicativo Explícita**, digite um **Identificador de Pacote** no formato `<Organization Identifier>.<Product Name>`, como descrito no [Guia de Distribuição de Aplicativo](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Isso deve corresponder ao que é usado no projeto do XCode, Xamarin ou Cordova para o aplicativo.

   * **Notificações por Push**: selecione a opção **Notificações por Push** na seção **Serviços de Aplicativos**.

     ![Registrar ID do aplicativo](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

5. Na **tela Confirmar a ID do aplicativo**, examine as configurações. Depois de verificá-los, selecione **Registrar**.

6. Após enviar a nova ID do Aplicativo, a tela **Registro concluído** é exibida. Selecione **Concluído**.

7. No Centro de Desenvolvedores, em **IDs de aplicativo**, localize a ID do aplicativo que você criou e selecione a respectiva linha. Selecionar a linha de ID do aplicativo exibe os detalhes do aplicativo. Selecione o botão **Editar** na parte inferior.

8. Role até a parte inferior da tela e selecione o botão **Criar Certificado...** na seção **Certificado SSL para Desenvolvimento**.

      ![Certificado SSL por Push para desenvolvimento](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

 Isso exibirá o assistente "Adicionar Certificado de iOS".

   > [!NOTE]
   > Este tutorial usa um certificado de desenvolvimento. Use o mesmo processo ao registrar um certificado de produção. Use o mesmo tipo de certificado ao enviar notificações.
   >

9. Selecione **Escolher Arquivo** e navegue até o local em que você salvou o CSR do certificado push. Em seguida, selecione **Gerar**.

      ![Gerar certificado](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

10. Depois que o certificado for criado pelo portal, selecione o botão **Baixar**.

      ![Certificado pronto](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

       Isso baixará o certificado de assinatura e salvará esse certificado no seu computador, na pasta Downloads.

      ![Baixar pasta](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

   > [!NOTE]
   > Por padrão, o arquivo baixado é um certificado de desenvolvimento denominado **aps_development.cer**.
   >
   >
11. Clique duas vezes no certificado de push baixado, **aps_development.cer**. Isso instalará o novo certificado no conjunto de chaves, conforme mostrado na seguinte captura de tela:

       ![Acesso de conjunto de chaves](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

   > [!NOTE]
   > O nome no certificado pode ser diferente, mas ele será prefixado com **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple**.
   >
   >
12. No **Acesso ao Conjunto de Chaves**, clique com o botão direito do mouse no novo certificado push criado na categoria **Certificados**. Selecione **Exportar**, nomeie o arquivo, selecione o formato **.p12** e selecione **Salvar**.

    Anote o nome do arquivo e o local do certificado .p12 exportado. Você o utiliza para habilitar a autenticação com APNS carregando-o no portal clássico do Azure. Se a opção de formato **.p12** não estiver disponível, talvez seja necessário reiniciar o Acesso ao Conjunto de Chaves.

## <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de provisionamento para o aplicativo
1. No <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a>, selecione **Perfis de Provisionamento**, selecione **Tudo** e selecione o botão **+** para criar um perfil. Isso inicia a ferramenta de criação de perfil **Adicionar Provisionamento do iOS**.

      ![Perfis de provisionamento](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Em **Desenvolvimento**, selecione **Desenvolvimento de Aplicativos do iOS** como o tipo de perfil de provisionamento e selecione **Continuar**.

3. Na lista suspensa **ID do aplicativo**, selecione a ID de aplicativo que você criou e selecione **Continuar**.

      ![Selecione a ID do aplicativo](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. Na tela **Selecionar certificados**, selecione o certificado de desenvolvimento que você usa para assinatura de código e selecione **Continuar**. Esse é um certificado de assinatura e não o certificado push que você criou.

       ![Signing certificate](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. Selecione os **Dispositivos** a serem usados para teste e selecione **Continuar**.

     ![Adicionar perfil de provisionamento](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. Por fim, em **Nome do Perfil**, escolha um nome para o perfil e selecione **Gerar**.

      ![Nomear o perfil](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
