
## <a id="register"></a>Registrar aplicativo para notificações por push

* [Registrar uma ID para seu aplicativo](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW991). Marque a caixa de seleção opcional **Notificações por Push** nos **Serviços de Aplicativos** ao registrar o aplicativo.

> [AZURE.NOTE]Crie uma ID de aplicativo explícita (não uma ID de aplicativo de caractere curinga) e, para **ID do Pacote**, use a **ID do Pacote** exata em seu projeto de início rápido do Xcode. Também é essencial que você marque a opção **Notificações por Push** nos **Serviços de Aplicativos** ao registrar a ID do aplicativo. As notificações por push não funcionarão se você não marcar essa caixa.

* Em seguida, [habilite as notificações por push para o aplicativo](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW6). Você pode criar um certificado SSL de "Desenvolvimento" ou "Distribuição" (lembre-se de selecionar a opção correspondente – "Área restrita" ou "Produção" – no portal de Serviços Móveis posteriormente.)

* Em seguida, [verifique se a ID do aplicativo habilita notificações por push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW8).

* Por fim, [atualize os perfis de provisionamento no projeto de início rápido do Xcode](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW10) e [verifique se o perfil de provisionamento foi criado ou gerado novamente para habilitar notificações por push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW12).

<!---HONumber=62-->