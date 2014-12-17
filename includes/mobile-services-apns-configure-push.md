Após registrar seu aplicativo com o APNS e configurar seu projeto, configure seu serviço móvel para integrá-lo ao APNS.

1. No conjunto de chaves de acesso, clique com o botão direito do mouse no novo certificado do aplicativo de início rápido em **Chaves** ou **Meus certificados**, clique em **Exportar**, nomeie o arquivo como QuickstartPusher, selecione o formato **.p12** e clique em **Salvar**.

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

  Anote o nome do arquivo e o local do certificado exportado.

>[WACOM.NOTE]Este tutorial cria um arquivo QuickstartPusher.p12. O nome do arquivo e o local podem ser diferentes.

2. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e no aplicativo.

   	![](./media/mobile-services-apns-configure-push/mobile-services-selection.png)

3. Clique na guia **Enviar por Push** e clique em **Carregar**.

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png)

	Isso exibirá a caixa de diálogo Carregar Certificado.

4. Clique em **Arquivo**, selecione o arquivo de certificado exportado do QuickstartPusher.p12, digite a **Senha**, verifique se o **Modo** correto está selecionado (Dev/Sandbox ou Prod/Production), clique no ícone de verificação e clique em **Salvar**.

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png)

    > [WACOM.NOTE] Este tutorial usa certificados de desenvolvimento.

Agora, seu serviço móvel está configurado para funcionar com o APNS.

<!-- URLs. -->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
