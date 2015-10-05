<!--author=SharS last changed: 9/17/15-->

#### Para instalar os hotfixes do modo de manutenção por meio do Windows PowerShell para StorSimple

> [AZURE.IMPORTANT]Quando você estiver no modo de Manutenção, precisará aplicar a atualização primeiro em um controlador e, em seguida, no outro controlador.

1. Coloque o dispositivo no modo de Manutenção. Consulte [Etapa 2: Entrar no modo de manutenção](storsimple-update-device.md#step2) para obter instruções sobre como entrar no modo de Manutenção.

2. Para aplicar o hotfix, digite:

     `Start-HcsHotfix`

3. Quando solicitado, forneça o caminho para a pasta compartilhada que contém os arquivos de hotfix.

4. Será solicitada a sua confirmação. Digite **Y** para prosseguir com a instalação do hotfix.

5. Após aplicar o hotfix em um controlador, faça logon no outro controlador. Aplique o hotfix da mesma forma que você fez para o controlador anterior.

6. Após a aplicação dos hotfixes, saia do modo de Manutenção. Consulte [Etapa 4: Sair do modo de manutenção](storsimple-update-device.md#step4) para obter instruções.

<!---HONumber=Sept15_HO4-->