Após registrar seu aplicativo com o APNS e configurar seu projeto, configure seu serviço móvel para integrá-lo ao APNS.

1.  Em Acesso do Conjunto de Chaves, clique com botão direito do mouse no novo certificado, clique em **Exportar**, nomeie seu arquivo QuickstartPusher, selecione o formato **. p12** e clique em **Salvar**.

    ![][1]

Anote o nome do arquivo e o local do certificado exportado.

> [WACOM.NOTE] Este tutorial cria um arquivo QuickstartPusher.p12. O nome do arquivo e o local podem ser diferentes.

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

    ![][2]

2.  Clique na guia **Push** e clique em **Carregar**.

    ![][3]

    Isso exibirá a caixa de diálogo Carregar Certificado.

3.  Clique em **Arquivo**, selecione o arquivo de certificado exportado QuickstartPusher.p12, digite a **Senha**, verifique se o **Modo** correto está selecionado, clique no ícone de verificação e clique em **Salvar**.

    ![][4]

    > [WACOM.NOTE] Este tutorial usa certificados de desenvolvimento.

Agora, seu serviço móvel está configurado para funcionar com o APNS.



  [1]: ./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-apns-configure-push/mobile-services-selection.png
  [3]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png
  [4]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png
