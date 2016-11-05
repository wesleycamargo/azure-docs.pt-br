
### Configurando o emulador do Android para teste
Quando você executa o aplicativo no emulador, certifique-se de usar um Android Virtual Device (AVD) que oferece suporte a APIs do Google.

> [!IMPORTANT]
> Para receber as notificações por push, você deve configurar uma conta do Google em seu Dispositivo Virtual para Android (no emulador, navegue até **Configurações** e clique em **Adicionar Conta**). Além disso, certifique-se de que o emulador esteja conectado à Internet.
> 
> 

1. A partir de **Ferramentas**, clique em **Abrir gerenciador de emulador do Android**, selecione seu dispositivo e, em seguida, clique em **Editar**.
   
       ![Gerenciador de Dispositivo Virtual do Android](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app7.png)
2. Selecione **Google APIs** em **Destino** e clique em **OK**.
   
       ![Editar o Dispositivo Virtual do Android](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app8.png)
3. Na barra de ferramentas superior, clique em **Executar**e, em seguida, selecione seu aplicativo. Isso iniciará o emulador e executará o aplicativo.
   
   O aplicativo recupera o *registrationId* do GCM e registrará no Hub de Notificação.

### Inserir um novo item gera uma notificação.
1. No aplicativo, digite um texto significativo, como *Uma nova tarefa de Serviços Móveis* e clique no botão **Incluir**.
2. Passe o dedo para baixo da parte superior da tela para abrir a Central de Notificação do dispositivo para ver a notificação.
   
    ![Notificação de exibição no Centro de notificação](./media/mobile-services-android-push-notifications-test/notification-area-received.png)

<!---HONumber=Oct15_HO3-->