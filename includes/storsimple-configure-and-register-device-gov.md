<!--author=SharS last changed: 9/17/15-->

### Para configurar e registrar o dispositivo

1. Acesse a interface do Windows PowerShell no console serial do dispositivo StorSimple. Consulte [Usar o PuTTY para se conectar ao console serial do dispositivo](#use-putty-to-connect-to-the-device-serial-console) para obter instruções. **Siga o procedimento corretamente ou você não conseguirá acessar o console.**

2. Na sessão que é aberta, pressione Enter uma vez para obter um prompt de comando.

3. Você deverá escolher o idioma que deseja definir para seu dispositivo. Especifique o idioma e pressione Enter.

    ![O StorSimple configura e registra o dispositivo 1](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice1-gov-include.png)

4. No menu do console serial apresentado, escolha a opção 1 para efetuar logon com acesso completo.

    ![Dispositivo de registro do StorSimple 2](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice2-gov-include.png)
  
5. Conclua as etapas a seguir para definir as configurações de rede necessárias e mínimas para seu dispositivo.

    > [AZURE.IMPORTANT]Essas etapas de configuração devem ser executadas no controlador ativo do dispositivo. O menu do console serial indica o estado do controlador na mensagem de faixa. Se você não estiver conectado ao controlador ativo, desconecte e conecte-se ao controlador ativo.

      1. No prompt de comando, digite sua senha. A senha do dispositivo padrão é **Senha1**.

      2. Digite o seguinte comando:

           `Invoke-HcsSetupWizard`

      3. Um assistente de instalação aparecerá para ajudá-lo a configurar as definições da rede para o dispositivo. Forneça as seguintes informações:

       - Endereço IP da interface de rede DADOS 0
       - Máscara da sub-rede
       - Gateway
       - Endereço IP do servidor DNS Primário
       - Endereço IP do servidor NTP Primário
 
        > [AZURE.NOTE]Você terá que aguardar alguns minutos para que a máscara de sub-rede e as configurações de DNS sejam aplicadas.

      4. Opcionalmente, configure seu servidor proxy da Web.

      >[AZURE.IMPORTANT]Embora a configuração do proxy da Web seja opcional, saiba que se você usar um proxy da Web, só poderá configurá-lo aqui. Para obter mais informações, visite [Configurar proxy da Web para seu dispositivo](storsimple-configure-web-proxy.md).

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

      Pode levar até 11 horas para que todas as atualizações sejam aplicadas, incluindo as atualizações do Windows.

8. Depois que todas as atualizações forem instaladas com êxito, execute o seguinte cmdlet para confirmar se as atualizações de software foram aplicadas corretamente:

     `Get-HcsSystem`

    Você deve ver as seguintes versões: - HcsSoftwareVersion: 6.3.9600.17491 - CisAgentVersion: 1.0.9037.0 - MdsAgentVersion: 26.0.4696.1433
 
9. Execute o seguinte cmdlet para confirmar se a atualização do firmware foi aplicada corretamente:

    `Start-HcsFirmwareCheck`.

     O status do firmware deve ser **UpToDate**.

10. Execute o seguinte cmdlet para apontar o dispositivo para o portal do Microsoft Azure Governamental (porque ele aponta para o Portal de Gerenciamento do Azure público por padrão). Isso reiniciará os dois controladores. É recomendável que você use duas sessões PuTTY para se conectar simultaneamente a ambos os controladores para poder ver quando cada controlador for reiniciado.

     `Set-CloudPlatform -AzureGovt_US`

    Você verá uma mensagem de confirmação. Aceite o padrão (**Y**).

11. Execute o seguinte cmdlet para retomar a instalação:

     `Invoke-HcsSetupWizard`

     ![Retomar o assistente de instalação](./media/storsimple-configure-and-register-device-gov/HCS_ResumeSetup-gov-include.png)

    Quando você retomar a instalação, o assistente será a versão de Atualização 1 (que corresponde à versão 17469).

12. Aceite as configurações de rede. Você verá uma mensagem de validação depois que aceitar cada configuração.
 
13. Por motivos de segurança, a senha de administrador do dispositivo expira após a primeira sessão, e você precisará alterá-la agora. Quando solicitado, forneça uma senha de administrador do dispositivo. Uma senha de administrador do dispositivo válida deve ter entre 8 e 15 caracteres. A senha deve conter três das seguintes opções: caracteres com letras minúsculas e maiúsculas, numéricos e especiais.

	<br/>![Dispositivo de registro do StorSimple 5](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice5_gov-include.png)

14. A etapa final do assistente de instalação registra seu dispositivo no serviço Gerenciador StorSimple. Para isso, será necessária a chave de registro do serviço obtida na [Etapa 2: obter a chave de registro](storsimple-get-service-registration-key-gov.md). Depois de fornecer a chave de registro, talvez seja necessário aguardar de 2 a 3 minutos antes do dispositivo ser registrado.

      >[AZURE.NOTE]Você pode pressionar Ctrl + C a qualquer momento para sair do assistente de instalação. Se você tiver inserido todas as configurações de rede (endereço IP para Dados 0, Máscara de sub-rede e Gateway), as entradas serão mantidas.

	![Progresso do registro do StorSimple](./media/storsimple-configure-and-register-device-gov/HCS_RegistrationProgress-gov-include.png)

15. Depois do dispositivo ser registrado, uma chave de Criptografia de Dados do Serviço será exibida. Copie essa chave e salve-a em um local seguro. **Essa chave será necessária com a chave de registro do serviço para registrar dispositivos adicionais no serviço Gerenciador StorSimple.** Consulte a [segurança do StorSimple](../articles/storsimple/storsimple-security.md) para obter mais informações sobre essa chave.
	
	![Dispositivo de registro do StorSimple 7](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice7_gov-include.png)

      >[AZURE.IMPORTANT]Para copiar o texto da janela do console serial, basta selecionar o texto. Em seguida, você deverá poder colá-lo na área de transferência ou em qualquer editor de texto. >> NÃO use Ctrl + C para copiar a chave de criptografia de dados de serviço. Usar Ctrl + C fará com que você saia do assistente de instalação. Como resultado, a senha de administrador do dispositivo não será alterada, e o dispositivo voltará para a senha padrão.

16. Saia do console serial.

17. Volte para o Portal Governamental e conclua as seguintes etapas:
  1. Clique duas vezes no serviço Gerenciador StorSimple para acessar a página**Início Rápido**.
  2. Clique em **Exibir dispositivos conectados**.
  3. Na página **Dispositivos**, verifique se o dispositivo conectou com êxito o serviço pesquisando o status. O status do dispositivo deve ser **Online**.
   
    	![StorSimple Devices page](./media/storsimple-configure-and-register-device-gov/HCS_DeviceOnline-gov-include.png) 
  
        If the device status is **Offline**, wait for a couple of minutes for the device to come online. 
      
        If the device is still offline after a few minutes, then you need to make sure that your firewall network was configured as described in the [network requirements for your StorSimple device](https://msdn.microsoft.com/library/dn772371.aspx). If you do not have HTTP 1.1 support, check port 9354 to make sure that it is open for outbound communication. This port is used for communication between the StorSimple Manager service and your StorSimple device.
     
        

<!---HONumber=Oct15_HO3-->