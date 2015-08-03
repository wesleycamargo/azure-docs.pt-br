#### Para instalar a Atualização 1 do Windows PowerShell para StorSimple

1. Execute as etapas a seguir para baixar a atualização do software.

    1. Inicie o Internet Explorer e acesse [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).
    2. Se você for um usuário iniciante, será solicitada a instalação de um Catálogo do Microsoft Update. Clique em **Instalar**.
    
        ![Instalar o catálogo](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. Você verá uma tela de pesquisa do catálogo. Digite **3063418** na caixa de pesquisa e clique **Pesquisar**.

        ![Pesquisar o catálogo](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. Você verá o pacote **StorSimple Update 1.0 Appliance Update**. Clique em **Adicionar**. A atualização será adicionada ao carrinho de compras.

        ![Atualizar o pacote](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. Clique em **Exibir carrinho**.
 
        ![Exibir carrinho](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. Clique em **Download**. Especifique ou navegue até o local onde deseja que o download seja exibido. A atualização (all-hcsmdssoftwareupdate_288da2cc8cd2e3c3958b603a79346cb586fb8fe3.exe) será baixada em uma pasta do pacote StorSimple Update 1.0 Appliance Update (KB3063418)" para o local escolhido. A pasta também pode ser copiada para um compartilhamento de rede que seja acessível do dispositivo.
        
2. Para instalar a atualização do software, acesse a interface do Windows PowerShell no console serial do dispositivo StorSimple. Siga as instruções detalhadas em [Usar o PuTTy para se conectar ao console serial do dispositivo](#use-putty-to-connect-to-the-serial-console).

3. No prompt de comando, pressione Enter.

4. Selecione a **Opção 1** para fazer logon no dispositivo com acesso completo.

5. Para instalar o pacote de atualização, no prompt de comando, digite:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    O parâmetro credential é usado somente para acessar um compartilhamento autenticado.

    A saída de exemplo é mostrada a seguir.

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

7. Monitore a atualização usando o cmdlet Get-HcsUpdateStatus.

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

        ````
 
8. Depois que a atualização for concluída, vá para a página de Manutenção do Portal de Gerenciamento. Verifique se há atualizações disponíveis. Você deve ver que existem mais atualizações de software disponíveis.

9. Clique em **Instalar atualizações** para aplicar todas as atualizações de software disponíveis no portal.

10. Depois que as atualizações de software estiverem concluídas, verifique as versões do software, do driver e do firmware do sistema. Digite o seguinte comando:

    `Get-HcsSystem`

    Você deverá ver as seguintes versões:

    - HcsSoftwareVersion: 6.3.9600.17491
    - CisAgentVersion: 1.0.9037.0
    - MdsAgentVersion: 26.0.4696.1433 
 
11. Para verificar se o firmware foi atualizado corretamente, digite:

    `Start-HcsFirmwareCheck`

    O status do firmware deve ser **UpToDate**.

<!---HONumber=July15_HO4-->