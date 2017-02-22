<!--author=SharS last changed: 02/22/16-->

### <a name="to-configure-and-register-the-device"></a>Para configurar e registrar o dispositivo
1. Acesse a interface do Windows PowerShell no console serial do dispositivo StorSimple. Consulte [Usar o PuTTY para se conectar ao console serial do dispositivo](#use-putty-to-connect-to-the-device-serial-console) para obter instruções. **Siga o procedimento corretamente ou você não conseguirá acessar o console.**
2. Na sessão que é aberta, pressione Enter uma vez para obter um prompt de comando. 
3. Você deverá escolher o idioma que deseja definir para seu dispositivo. Especifique o idioma e pressione Enter. 
   
    ![O StorSimple configura e registra o dispositivo 1](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice1-gov-include.png)
4. No menu do console serial apresentado, escolha a opção 1 para efetuar logon com acesso completo. 
   
    ![Dispositivo de registro do StorSimple 2](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice2-gov-include.png)
5. Conclua as etapas a seguir para definir as configurações de rede necessárias e mínimas para seu dispositivo.
   
   > [!IMPORTANT]
   > Essas etapas de configuração devem ser executadas no controlador ativo do dispositivo. O menu do console serial indica o estado do controlador na mensagem de faixa. Se você não estiver conectado ao controlador ativo, desconecte e conecte-se ao controlador ativo.
   > 
   > 
   
   1. No prompt de comando, digite sua senha. A senha do dispositivo padrão é **Senha1**.
   2. Digite o seguinte comando:
      
        `Invoke-HcsSetupWizard`
   3. Um assistente de instalação aparecerá para ajudá-lo a configurar as definições da rede para o dispositivo. Forneça as seguintes informações: 
      
      * Endereço IP da interface de rede DADOS 0
      * Máscara da sub-rede
      * Gateway
      * Endereço IP do servidor DNS Primário
      * Endereço IP do servidor NTP Primário
      
      > [!NOTE]
      > Você terá que aguardar alguns minutos para que a máscara de sub-rede e as configurações de DNS sejam aplicadas. 
      > 
      > 
   4. Opcionalmente, configure seu servidor proxy da Web.
      
      > [!IMPORTANT]
      > Embora a configuração do proxy da Web seja opcional, saiba que se você usar um proxy da Web, só poderá configurá-lo aqui. Para obter mais informações, visite [Configurar proxy da Web para seu dispositivo](../articles/storsimple/storsimple-configure-web-proxy.md). 
      > 
      > 
6. Pressione Ctrl + C para sair do assistente de instalação.
7. Instale as atualizações da seguinte maneira:
   
   1. Use o seguinte cmdlet para definir IPs em ambos os controladores:
      
      `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`
   2. No prompt de comando, execute `Get-HcsUpdateAvailability`. Você deve ser notificado de que há atualizações disponíveis.
   3. Execute `Start-HcsUpdate`. Você pode executar esse comando em qualquer nó. As atualizações serão aplicadas ao primeiro controlador, o controlador realizará failover e, em seguida, as atualizações serão aplicadas ao outro controlador.
      
      Você pode monitorar o progresso da atualização executando `Get-HcsUpdateStatus`.    
      
      A saída de exemplo a seguir mostra a atualização em andamento.
      
      ````
      Controller0>Get-HcsUpdateStatus
      RunInprogress       : True
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   : 
      ````
      
      A saída de exemplo a seguir indica que a atualização foi concluída.
      
      ````
      Controller1>Get-HcsUpdateStatus
      
      RunInprogress       : False
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      
      It may take up to 11 hours to apply all the updates, including the Windows Updates.
8. After all the updates are successfully installed, run the following cmdlet to confirm that the software updates were applied correctly:
   
     `Get-HcsSystem`
   
    You should see the following versions:
   
   * HcsSoftwareVersion: 6.3.9600.17491
   * CisAgentVersion: 1.0.9037.0
   * MdsAgentVersion: 26.0.4696.1433
9. Run the following cmdlet to confirm that the firmware update was applied correctly:
   
    `Start-HcsFirmwareCheck`.
   
     The firmware status should be **UpToDate**.
10. Run the following cmdlet to point the device to the Microsoft Azure Government portal (because it points to the public Azure classic portal by default). This will restart both controllers. We recommend that you use two PuTTY sessions to simultaneously connect to both controllers so that you can see when each controller is restarted.
    
     `Set-CloudPlatform -AzureGovt_US`
    
    You will see a confirmation message. Accept the default (**Y**).
11. Run the following cmdlet to resume setup:
    
     `Invoke-HcsSetupWizard`
    
     ![Resume setup wizard](./media/storsimple-configure-and-register-device-gov/HCS_ResumeSetup-gov-include.png)
    
    When you resume setup, the wizard will be the Update 1 version (which corresponds to version 17469). 
12. Accept the network settings. You will see a validation message after you accept each setting.
13. For security reasons, the device administrator password expires after the first session, and you will need to change it now. When prompted, provide a device administrator password. A valid device administrator password must be between 8 and 15 characters. The password must contain three of the following: lowercase, uppercase, numeric, and special characters.
    
    <br/>![StorSimple register device 5](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice5_gov-include.png)
14. The final step in the setup wizard registers your device with the StorSimple Manager service. For this, you will need the service registration key that you obtained in [Step 2: Get the service registration key](#step-2-get-the-service-registration-key). After you supply the registration key, you may need to wait for 2-3 minutes before the device is registered.
    
    > [!NOTE]
    > You can press Ctrl + C at any time to exit the setup wizard. If you have entered all the network settings (IP address for Data 0, Subnet mask, and Gateway), your entries will be retained.
    > 
    > 
    
    ![StorSimple registration progress](./media/storsimple-configure-and-register-device-gov/HCS_RegistrationProgress-gov-include.png)
15. After the device is registered, a Service Data Encryption key will appear. Copy this key and save it in a safe location. **This key will be required with the service registration key to register additional devices with the StorSimple Manager service.** Refer to [StorSimple security](../articles/storsimple/storsimple-security.md) for more information about this key.
    
    ![StorSimple register device 7](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > To copy the text from the serial console window, simply select the text. You should then be able to paste it in the clipboard or any text editor. 
    > 
    > DO NOT use Ctrl + C to copy the service data encryption key. Using Ctrl + C will cause you to exit the setup wizard. As a result, the device administrator password will not be changed and the device will revert to the default password.
    > 
    > 
16. Exit the serial console.
17. Return to the Azure Government Portal, and complete the following steps:
    
    1. Double-click your StorSimple Manager service to access the **Quick Start** page.
    2. Click **View connected devices**.
    3. On the **Devices** page, verify that the device has successfully connected to the service by looking up the status. The device status should be **Online**.
       
        ![StorSimple Devices page](./media/storsimple-configure-and-register-device-gov/HCS_DeviceOnline-gov-include.png) 
       
        If the device status is **Offline**, wait for a couple of minutes for the device to come online. 
       
        If the device is still offline after a few minutes, then you need to make sure that your firewall network was configured as described in [networking requirements for your StorSimple device](../articles/storsimple/storsimple-system-requirements.md). 
       
        Verify that port 9354 is open for outbound communication as this is used by the service bus for StorSimple Manager service-to-device communication.



<!--HONumber=Nov16_HO3-->


