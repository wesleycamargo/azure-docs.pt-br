<!--author=SharS last changed: 9/17/15-->

#### Para instalar a Atualização 1.2 do Windows PowerShell para StorSimple

1. Execute as etapas a seguir para baixar a atualização do software.

    1. Inicie o Internet Explorer e acesse [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).
    2. Se você for um usuário iniciante, será solicitada a instalação de um Catálogo do Microsoft Update. Clique em **Instalar**.
    
        ![Instalar o catálogo](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. Você verá uma tela de pesquisa do catálogo. Digite **3063418** na caixa de pesquisa e clique **Pesquisar**.

        ![Pesquisar o catálogo](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. Você verá o pacote **StorSimple Update 1.2 Appliance Update**. Clique em **Adicionar**. A atualização será adicionada ao carrinho de compras.

        ![Atualizar o pacote](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. Clique em **Exibir carrinho**.
 
        ![Exibir carrinho](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. Clique em **Download**. Especifique ou **navegue** até o local onde deseja que o download apareça. A atualização será baixada em uma pasta do **pacote de Atualização de Dispositivo do StorSimple Atualização 1.2** (KB3063418) no local escolhido. A pasta também pode ser copiada para um compartilhamento de rede que seja acessível do dispositivo.
    
	Esse procedimento descreve como instalar a atualização do dispositivo de software como um hotfix, as atualizações de firmware de disco do servidor Microsoft Update, bem como o driver LSI e as Atualizações do Windows do Portal de Gerenciamento. No entanto, você pode optar por instalar as atualizações de software, driver e firmware de disco como hotfixes. Em seguida, você precisará baixar a Atualização do Controlador SAS do StorSimple 1.2 SAS (KB3043005) e a Atualização de Firmware de Disco do StorSimple 1.2 (KB3063416) e copiá-los para a mesma pasta compartilhada. Para instalar as atualizações de firmware de disco como um hotfix, siga as instruções em [instalando hotfixes do modo de manutenção por meio do Windows PowerShell para StorSimple](storsimple-update-device.md#install-hotfixes-via-windows-powershell-for-storsimple).
    
	> [AZURE.NOTE]O hotfix deve estar acessível nos dois controladores para detectar possíveis mensagens de erro dos controladores pares.
            
2. Para instalar a atualização do software, acesse a interface do Windows PowerShell no console serial do dispositivo StorSimple. Siga as instruções detalhadas em [Usar o PuTTy para se conectar ao console serial do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

3. No prompt de comando, pressione **Enter**.

4. Selecione a **Opção 1** para fazer logon no dispositivo com acesso completo.

5. Para instalar o pacote de atualização, no prompt de comando, digite:

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
        LastHotfixTimestamp : 9/02/2015 10:36:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     A saída de exemplo a seguir indica que a atualização foi concluída. O `RunInProgress` será `False` quando a atualização estiver concluída.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 9/02/2015 10:56:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
		

	> [AZURE.NOTE]Ocasionalmente, o cmdlet relatará `False` quando a atualização ainda estiver em andamento. Para garantir que o hotfix seja concluído, aguarde alguns minutos, execute esse comando novamente e verifique se `RunInProgress` é `False`. Em caso positivo, o hotfix foi concluído.
	
8. Depois que a atualização do software estiver concluída, verifique as versões de software do sistema. Digite o seguinte comando:

    `Get-HcsSystem`

    Você deverá ver as seguintes versões:

    - HcsSoftwareVersion: 6.3.9600.17584
    - CisAgentVersion: 1.0.9049.0
    - MdsAgentVersion: 26.0.4696.1433 
    
	Se os números de versão não mudarem após a aplicação da atualização, isso indica que houve falha na aplicação do hotfix. Caso isso aconteça, entre em contato com o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter mais ajuda.
    
9. Agora, você vai instalar as atualizações de firmware de disco que foram interrompidas e isso levará de 30 a 45 minutos. Você pode optar por instalá-las em uma janela de manutenção planejada conectando-se ao console serial do dispositivo. Para instalar atualizações do firmware de disco, siga as instruções em [Instalar atualizações do modo de manutenção por meio do Windows PowerShell para StorSimple](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

10. Depois que as atualizações do firmware de disco forem aplicadas com êxito e o dispositivo tiver saído do modo de manutenção, retorne ao Portal de Gerenciamento. As atualizações do modo de manutenção são atualizadas no portal somente depois de 24 horas. Talvez seja preciso aguardar para aplicar as atualizações não interrompidas restantes no Portal de Gerenciamento.

11. Quando estiver pronto para aplicar as atualizações, navegue até a página **Manutenção** e, na parte inferior da página, clique em **Verificar Atualizações**. Você será notificado que as atualizações estão disponíveis, que incluem o driver e as Atualizações do Windows. Clique em **Instalar Atualizações** para iniciar a instalação. Você termina depois que todas as atualizações forem instaladas com êxito.





 
 

<!---HONumber=Sept15_HO4-->