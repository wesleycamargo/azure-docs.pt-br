Após registrar seu aplicativo com o APNS e configurar seu projeto, configure seu aplicativo móvel para integrá-lo ao APNS.

1. No Acesso de Conjunto de Chaves, clique com o botão direito no novo certificado do aplicativo de início rápido em **Chaves** ou **Meus Certificados**, clique em **Exportar**, atribua o nome QuickstartPusher ao seu arquivo, selecione o formato **.p12** e clique em **Salvar**.

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

    Anote o nome do arquivo e o local do certificado exportado.

>[AZURE.NOTE]Este tutorial cria um arquivo QuickstartPusher.p12. O nome do arquivo e o local podem ser diferentes.

2. Faça logon no [Portal de Visualização do Azure], selecione **Procurar**, **Aplicativos Móveis** e, em seguida, clique em seu aplicativo. Clique nos serviços de Notificação por Push.

3. No Apple Push Notification Service, carregue seu certificado com o arquivo **.p12** e a senha associada a ele e então selecione o modo desejado.

Seu aplicativo móvel do Serviço de Aplicativo agora está configurado para trabalhar com APNS.

<!-- URLs. -->
[Portal de Visualização do Azure]: https://portal.azure.com/

<!--HONumber=54-->