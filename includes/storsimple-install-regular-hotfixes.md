
#### Para instalar hotfixes normais por meio do Windows PowerShell para StorSimple

1. Conecte-se ao console serial do dispositivo. Para obter mais informações, confira [Conectar-se ao console serial](#connect-to-the-serial-console).

2. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**. Digite a senha. A senha padrão é **Senha1**.

3. No prompt de comando, digite:

     `Start-HcsHotfix` <br/>

     >[AZURE.IMPORTANT]>>-Este comando se aplica somente para hotfixes normais. Executar esse comando em apenas um controlador, mas ambos os controladores serão atualizados. >-Você pode perceber um failover do controlador durante o processo de atualização; no entanto, o failover não afetará a disponibilidade ou operação do sistema.

4. Quando solicitado, forneça o caminho para a pasta compartilhada que contém os arquivos de hotfix.

5. Será solicitada a sua confirmação. Digite **Y** para prosseguir com a instalação do hotfix.

<!---HONumber=July15_HO4-->