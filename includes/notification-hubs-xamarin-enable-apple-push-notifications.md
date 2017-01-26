

Para registrar o aplicativo a fim de obter notificações por push através do serviço APNS (Apple Push Notification Service), você deve criar um novo certificado push, uma ID para o aplicativo e um Perfil de Provisionamento para o projeto no portal do desenvolvedor da Apple. A ID do aplicativo inclui as definições de configuração que permitem ao aplicativo enviar e receber notificações por push. Essas configurações incluem o certificado de notificação por push necessário para se autenticar com o APNS (Apple Push Notification Service) durante o envio e recebimento de notificações por push. Para saber mais sobre esses conceitos, confira a documentação oficial do [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584) .

#### <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>Gerar o arquivo de solicitação de assinatura de certificado do certificado push
Essas etapas mostram como criar a solicitação de assinatura de certificado. Ele será usado para gerar um certificado push para utilização com o APNS.

1. Em seu Mac, execute a ferramenta Acesso do Conjunto de Chaves. Ela pode ser aberta da pasta **Utilitários** ou da pasta **Outros** no launch pad.
2. Clique em **Acesso do Conjunto de Chaves**, expanda **Assistente de Certificado** e clique em **Solicitar um Certificado de uma Autoridade de Certificação...**.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Selecione seu **Endereço de Email de Usuário** e seu **Nome Comum**, verifique se **Salvo em disco** está selecionado e, em seguida, clique em **Continuar**. Deixe o campo **Endereço de Email de CA** em branco, pois ele não é necessário.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e, em seguida, clique em **Salvar**.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      Isso salvará o arquivo CSR no local selecionado; o local padrão está situado na Área de Trabalho. Lembre-se do local escolhido para esse arquivo.

#### <a name="register-your-app-for-push-notifications"></a>Registrar seu aplicativo para notificações por push
Crie uma nova ID de aplicativo explícita para o aplicativo na Apple e configure-o para receber notificações por push.  

1. Navegue até o [Portal de Provisionamento do iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) no Apple Developer Center, faça logon com a Apple ID, clique em **Identificadores**, clique em **IDs de Aplicativos** e clique no sinal de **+** para registrar um novo aplicativo.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)
2. Atualize os três campos a seguir para o novo aplicativo e clique em **Continuar**:
   
   * **Nome**: digite um nome descritivo para o aplicativo no campo **Nome**, na seção **Descrição de ID do Aplicativo**.
   * **Identificador de Pacote**: na seção **ID do Aplicativo Explícita**, digite um **Identificador de Pacote** no formato `<Organization Identifier>.<Product Name>`, como mencionado no [Guia de Distribuição de Aplicativo](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Isso deve corresponder ao que também é usado no projeto do XCode, Xamarin ou Cordova para o aplicativo.
   * **Notificações por Push**: marque a opção **Notificações por Push** na seção **Serviços de Aplicativos**.
     
     ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
3. Na tela Confirmar a ID do aplicativo, verifique as configurações e clique em **Enviar**
4. Após enviar a ID do aplicativo, a tela **Registro concluído** é exibida. Clique em **Concluído**.
5. Na Central de Desenvolvedores, em IDs de Aplicativo, localize a ID do aplicativo que você acabou de criar e clique na respectiva linha. Para exibir os detalhes do aplicativo, clique na respectiva ID. Clique no botão **Editar** na parte inferior.
6. Role até a parte inferior da tela e clique no botão **Criar Certificado...** na seção **Certificado SSL por Push para Desenvolvimento**.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
       This will display the "Add iOS Certificate" assistant.
   
   > [!NOTE]
   > Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Use o mesmo tipo de certificado ao enviar notificações.
   > 
   > 
7. Clique em **Escolher Arquivo**, navegue até o local em que você salvou o CSR do certificado push. Em seguida, clique em **Gerar**.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
8. Depois que o certificado tiver sido criado pelo portal, clique no botão **Baixar** .
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
       This downloads the signing certificate and saves it to your computer in your Downloads folder.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > Por padrão, o arquivo baixado, um certificado de desenvolvimento, é denominado **aps_development.cer**.
   > 
   > 
9. Clique duas vezes no certificado de push baixado, **aps_development.cer**. Isso instalará o novo certificado no Conjunto de Chaves, conforme mostrado abaixo:
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > O nome em seu certificado pode ser diferente, mas ele será prefixado com **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple:**.
   > 
   > 
10. No Acesso ao Conjunto de Chaves, clique com o botão direito do mouse no novo certificado push criado na categoria **Certificados** . Clique em **Exportar**, nomeie o arquivo, selecione o formato **.p12** e clique em **Salvar**.
    
    Anote o nome do arquivo e o local do certificado .p12 exportado. Ele será usado para habilitar a autenticação com o APNS carregando-o no Portal Clássico do Azure.

#### <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de provisionamento para o aplicativo
1. De volta ao <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a>, selecione **Perfis de Provisionamento**, escolha **Tudo** e clique no botão **+** para criar um novo perfil. Isso iniciará o Assistente **Adicionar Perfil de Provisionamento do iOS**
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e clique em **Continuar**.
3. Em seguida, selecione a ID do aplicativo que você acabou de criar da lista suspensa **ID do Aplicativo** e clique em **Continuar**
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. Na tela **Selecionar certificados**, selecione o certificado de desenvolvimento usado por assinatura de código e clique em **Continuar**. Esse é um certificado de assinatura e não o certificado push que você acabou de criar.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. Em seguida, selecione os **Dispositivos** a serem usados no teste e clique em **Continuar**
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. Por fim, escolha um nome para o perfil em **Nome do Perfil** e clique em **Gerar**.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)



<!--HONumber=Jan17_HO3-->


