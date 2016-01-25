<!--author=alkohli last changed: 01/12/15-->

#### Para baixar os hotfixes

Execute as etapas a seguir para baixar a atualização do software do Catálogo do Microsoft Update.

1. Inicie o Internet Explorer e acesse [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).

2. Se você for um usuário iniciante, será solicitada a instalação de um Catálogo do Microsoft Update. Clique em **Instalar**.
    
   	![Instalar o catálogo](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. Você verá uma tela de pesquisa do catálogo. Digite **3121901** na caixa de pesquisa e clique **Pesquisar**.

    ![Pesquisar o catálogo](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. Você verá a **Atualização Cumulativa de Pacote de Software 2.0 para StorSimple Série 8000**. Clique em **Adicionar**. A atualização será adicionada ao carrinho de compras.

5. Clique em **Exibir carrinho**.
 
6. Clique em **Download**. Especifique ou **navegue** até o local onde deseja que o download apareça. A atualização será baixada em uma pasta (mesmo nome que a atualização) para o local escolhido. A pasta também pode ser copiada para um compartilhamento de rede que seja acessível do dispositivo.
       
	> [AZURE.NOTE]
	> 
	> - Você também precisará baixar a **Atualização do driver LSI** (Atualização do controlador SAS 2.0 para StorSimple Série 8000 - KB3121900), **Atualização Storport** (Hotfix do Windows Server 2012 R2 x64 Edition - KB3080728), **Atualização Spaceport** (Hotfix do Windows Server 2012 R2 x64 Edition - KB3090322) e **Atualização de firmware de disco** (Atualização Cumulativa de Firmware de Disco 2.0 para StorSimple Série 8000 - KB3121899) e copiar para a mesma pasta compartilhada.
	> - O hotfix deve estar acessível nos dois controladores para detectar possíveis mensagens de erro dos controladores pares.

#### Para instalar e verificar os hotfixes do modo normal

Execute as etapas a seguir para instalar e verificar os hotfixes regulares.

1. Para instalar os hotfixes, acesse a interface do Windows PowerShell no console serial do dispositivo StorSimple. Siga as instruções detalhadas em [Usar o PuTTy para se conectar ao console serial do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). No prompt de comando, pressione **Enter**.

4. Selecione a **Opção 1** para fazer logon no dispositivo com acesso completo.

5. Para instalar o hotfix, no prompt de comando, digite:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Use o IP em vez do DNS no caminho de compartilhamento no comando acima. O parâmetro credential é usado somente para acessar um compartilhamento autenticado.

	É recomendável usar o parâmetro de credencial para acessar compartilhamentos. Até mesmo os compartilhamentos abertos para "todos" geralmente não são abertos para usuários não autenticados.

    Um exemplo de saída é mostrado abaixo.

        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
      
        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not 
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````
 
6. Digite **Y** quando solicitado a confirmar a instalação do hotfix.

7. Monitore a atualização usando o cmdlet `Get-HcsUpdateStatus`.

    A saída de exemplo a seguir mostra a atualização em andamento. O `RunInprogress` será `True` quando a atualização estiver em andamento.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 12/21/2015 10:36:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     A saída de exemplo a seguir indica que a atualização foi concluída. O `RunInProgress` será `False` quando a atualização estiver concluída.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 12/21/2015 10:59:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
		

	> [AZURE.NOTE]Ocasionalmente, o cmdlet relatará `False` quando a atualização ainda estiver em andamento. Para garantir que o hotfix seja concluído, aguarde alguns minutos, execute esse comando novamente e verifique se `RunInProgress` é `False`. Em caso positivo, o hotfix foi concluído.
	
8. Após a conclusão da atualização de software, repita as etapas 3 a 5 para instalar e monitorar o agente SaaS e o agente do MDS usando o `CisMdsAgentUpdateBundle.exe`. Certifique-se de que `HcsMdsSoftwareUpdate.exe` seja instalado antes de `CisMdsAgentUpdateBundle.exe`.

9. Verifique as versões de software do sistema. Tipo:

    `Get-HcsSystem`

    Você deverá ver as seguintes versões:

    - HcsSoftwareVersion: 6.3.9600.17673
    - CisAgentVersion: 1.0.9150.0
    - MdsAgentVersion: 30.0.4698.13 
    
	Se os números de versão não mudarem após a aplicação da atualização, isso indica que houve falha na aplicação do hotfix. Caso isso aconteça, entre em contato com o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter mais ajuda.
    
9. Repita as etapas 3 a 5 para instalar e monitorar os hotfixes regulares restantes.

	- O driver LSI usando o pacote `HcsLsiUpdate.exe` (KB3121900).
	- A correção do Storport usando o pacote `Storport-KB3080728-x64.msu` (KB3080728).
	- A correção do Spaceport usando o pacote `spaceport-KB3090322-x64.msu` (KB3090322).

#### Para instalar e verificar o hotfix do modo de manutenção

Use o pacote `DiskFirmwarePackage.exe` (KB3121899) para instalar atualizações de firmware de disco. Estas são atualizações com interrupção e levam cerca de 30 minutos para ser concluídas. Você pode optar por instalá-las em uma janela de manutenção planejada conectando-se ao console serial do dispositivo. Para instalar atualizações de firmware de disco, siga as instruções abaixo.

1. Coloque o dispositivo no Modo de manutenção. Observe que você não deve usar a comunicação remota do Windows PowerShell ao se conectar a um dispositivo no Modo de manutenção. Você precisará executar esse cmdlet no controlador de dispositivo quando conectado por meio do console serial do dispositivo. Tipo:
		
	`Enter-HcsMaintenanceMode`

	Um exemplo de saída é mostrado abaixo.

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update2-8100-SHG0997879L76YD
		Software Version: 6.3.9600.17664
		Copyright (C) 2014 Microsoft Corporation. All rights reserved.
		You are connected to Controller0 - Passive
		---------------------------------------------------------------

		Serial Console Menu
		[1] Log in with full access
		[2] Log into peer controller with full access
		[3] Connect with limited access
		[4] Change language
		Please enter your choice>

	Ambos os controladores serão reiniciados. Após a reinicialização estiver concluída, ambos os controladores estarão em Modo de manutenção.

3. Para instalar a atualização de firmware de disco, digite:

	`Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

	Um exemplo de saída é mostrado abaixo.

        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
		Enter Password:
		WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
		Confirm
		This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
		[Y] Yes [N] No (Default is "Y"): Y
		WARNING: Installation is currently in progress. This operation can take several minutes to complete.
	

1.  Monitorar o progresso de instalação usando o comando `Get-HcsUpdateStatus`. A atualização é concluída quando o `RunInProgress` muda para `False`.
 
2.  Depois que a instalação for concluída, o controlador no qual o hotfix do modo de manutenção foi instalado será reinicializado. Faça logon como opção 1 com acesso completo e verifique a versão de firmware de disco. Tipo:
	
	`Get-HcsFirmwareVersion`
  
	As versões de firmware de disco esperados são:

	`XMGG, XGEG, KZ50, F6C2, VR08`

	Um exemplo de saída é mostrado abaixo.


        -----------------------MAINTENANCE MODE------------------------
    	Microsoft Azure StorSimple Appliance Model 8100
    	Name: Update2-8100-SHG0997879L76YD
    	Software Version: 6.3.9600.17664
    	Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    	You are connected to Controller1
    	---------------------------------------------------------------
    	
    	Controller1>Get-HcsFirmwareVersion
    	
    	
    	Controller0 : TalladegaFirmware
    	  ActiveBIOS:0.45.0006
    	  BackupBIOS:0.45.0008
    	  MainCPLD:17.0.0005
    	  ActiveBMCRoot:2.0.000E
    	  BackupBMCRoot:2.0.000E
    	  BMCBoot:2.0.0001
    	  LsiFirmware:19.00.00.00
    	  LsiBios:07.37.00.00
    	  Battery1Firmware:06.29
    	  Battery2Firmware:06.29
    	  DomFirmware:X231600
    	  CanisterFirmware:3.5.0.32
    	  CanisterBootloader:5.03
    	  CanisterConfigCRC:0xD1B030A4
    	  CanisterVPDStructure:0x06
    	  CanisterGEMCPLD:0x17
    	  CanisterVPDCRC:0xEE3504B4
    	  MidplaneVPDStructure:0x0C
    	  MidplaneVPDCRC:0xA6BD4F64
    	  MidplaneCPLD:0x10
    	  PCM1Firmware:1.00|1.05
    	  PCM1VPDStructure:0x05
    	  PCM1VPDCRC:0x41BEF99C
    	  PCM2Firmware:1.00|1.05
    	  PCM2VPDStructure:0x05
    	  PCM2VPDCRC:0x41BEF99C
    	
    	  DisksFirmware
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG

	 Execute o comando `Get-HcsFirmwareVersion` no segundo controlador para verificar se a versão do software foi atualizada. Em seguida, você pode sair do modo de manutenção. Digite o comando a seguir para cada controlador de dispositivo:

    `Exit-HcsMaintenanceMode`
     
1. Os controladores serão reinicializados quando você sair do Modo de manutenção. Depois que as atualizações do firmware de disco forem aplicadas com êxito e o dispositivo tiver saído do modo de manutenção, retorne ao portal clássico do Azure. As atualizações do modo de manutenção são atualizadas no portal somente depois de 24 horas.






 
 

<!---HONumber=AcomDC_0114_2016-->