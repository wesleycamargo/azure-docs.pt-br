
* Siga as etapas em [Instalando uma identidade de assinatura SSL de cliente no servidor](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW15) para exportar o certificado que você baixou na etapa anterior para um arquivo. p12.

* No Portal de Visualização do Azure, clique em **Procurar** > **Aplicativos Móveis** > seu aplicativo > **Serviços de notificação por push** > **Serviços de Notificação por Push da Apple** > **Carregar Certificado**. Carregue o arquivo. p12, verificando se o **modo** correto está selecionado (Área Restrita ou Produção, dependendo de o certificado SSL de cliente gerado ser de Desenvolvimento ou de Distribuição.) Agora, seu serviço móvel está configurado para funcionar com notificações por push no iOS!

<!---HONumber=July15_HO4-->