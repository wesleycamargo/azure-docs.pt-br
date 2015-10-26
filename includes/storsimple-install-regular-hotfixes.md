<!--author=SharS last changed: 9/17/15-->

#### Para instalar hotfixes normais por meio do Windows PowerShell para StorSimple

1. Conecte-se ao console serial do dispositivo. Para obter mais informações, consulte [Etapa 1: Conectar-se ao console serial](storsimple-update-device.md#step1).

2. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**. Digite a senha. A senha padrão é **Senha1**.

3. No prompt de comando, digite:

    `Start-HcsHotfix`

       >[AZURE.IMPORTANT]>>-Este comando se aplica somente a hotfixes normais. Você executa esse comando em apenas um controlador, mas ambos os controladores são atualizados. >- Você pode notar um failover do controlador durante o processo de atualização. Porém, o failover não afetará a disponibilidade nem a operação do sistema.

4. Quando solicitado, forneça o caminho para a pasta compartilhada que contém os arquivos de hotfix.

5. Será solicitada a sua confirmação. Digite **Y** para prosseguir com a instalação do hotfix.

<!---HONumber=Oct15_HO3-->