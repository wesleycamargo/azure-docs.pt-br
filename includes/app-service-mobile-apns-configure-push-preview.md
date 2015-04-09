Após registrar seu aplicativo com o APNS e configurar seu projeto, configure seu aplicativo móvel para integrá-lo ao APNS.

1. No conjunto de chaves de acesso, clique no novo certificado de início rápido do aplicativo em **Chaves** ou **Meus certificados**, clique em **Exportar**, nomeie o arquivo como QuickstartPusher, selecione o formato **. p12** e, em seguida, clique em **Salvar**.

   ![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

    Anote o nome do arquivo e o local do certificado exportado.

>[AZURE.NOTE] Este tutorial cria um arquivo QuickstartPusher.p12. O nome do arquivo e o local podem ser diferentes.

2. Faça logon no [Portal de Visualização do Azure], selecione **Procurar**, **Aplicativos Móveis** e, em seguida, clique em seu aplicativo. Clique nos serviços de notificação por Push.

3. No Serviço de notificação por Push da Apple, carregue seu certificado com o arquivo **.p12**, a senha associada a ele, e então selecione o modo desejado.

Seu aplicativo móvel do Serviço de Aplicativo agora está configurado para trabalhar com APNS.

<!-- URLs. -->
[Portal de Visualização do Azure]: https://portal.azure.com/

<!--HONumber=49-->