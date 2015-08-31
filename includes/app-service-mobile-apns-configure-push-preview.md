
1.  Em seu Mac, inicie **Acesso ao Conjunto de Chaves**. Abra **Categoria** > **Meus Certificados**. Localize o certificado SSL para exportar (que você baixou anteriormente) e divulgue seu conteúdo. Selecione apenas o certificado sem selecionar a chave privada, e [exporte-o](https://support.apple.com/kb/PH20122?locale=en_US).

2. No portal do Azure, clique em **Procurar Tudo** > **Aplicativos Móveis** > seu back-end > **Configurações** > **Aplicativo Móvel** > **Push** > **Definir configurações necessárias** > **+ Hub de notificação** e forneça um nome e namespace ao hub de notificação e, em seguida, clique no botão **OK**.

  ![][1]

3. Na folha **Criar Hub de Notificação**, clique no botão **Criar**.
     
    Antes de ir para a próxima etapa, clique em **Notificações**, para garantir que a instalação do hub de notificação seja concluída. 
4. No Portal do Azure, clique em **Procurar Tudo** > **Aplicativos Móveis** > seu back-end > **Configurações** > **Aplicativo Móvel** > **Push** > **Apple Push Notification Service** > **Carregar Certificado**. Carregue o arquivo .p12, selecionando o **Modo** correto (dependendo se o certificado SSL de cliente gerado antes era Desenvolvimento ou Distribuição.) Agora, seu serviço móvel está configurado para funcionar com notificações por push no iOS!

[1]: ./media/app-service-mobile-apns-configure-push-preview/mobile-push-notification-hub.png

<!---HONumber=August15_HO8-->