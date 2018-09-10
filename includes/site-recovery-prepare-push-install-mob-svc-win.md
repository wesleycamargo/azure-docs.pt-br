### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Preparação para uma instalação por push em um computador Windows

1. Verifique se há conectividade de rede entre o computador Windows e o servidor de processo.
1. Crie uma conta que o servidor de processo possa usar para acessar o computador. A conta deve ter direitos de administrador, seja local ou do domínio. Use essa conta somente para a instalação por push e atualizações de agentes.

   > [!NOTE]
   > Se você não usar uma conta de domínio, desabilite o controle de Acesso de Usuário Remoto no computador local. Para desabilitar o controle de Acesso de Usuário Remoto, na chave do Registro HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, adicione um novo DWORD: **LocalAccountTokenFilterPolicy**. Defina o valor como **1**. Para fazer essa tarefa no prompt de comando, execute o seguinte comando:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
1. No Firewall do Windows do computador que você deseja proteger, selecione **Permitir um aplicativo ou recurso pelo Firewall**. Habilite **Compartilhamento de Arquivo e Impressora** e **Instrumentação de Gerenciamento do Windows (WMI)**. Em computadores pertencentes a um domínio, pode-se configurar o firewall com um objeto de Política de Grupo (GPO).

   ![Configurações de firewall](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

1. Adicione a conta criada em CSPSConfigtool. Siga estas etapas:

    a. Conecte-se ao servidor de configuração.

    b. Abra **cspsconfigtool.exe**. Ele está disponível como atalho na área de trabalho e na pasta %ProgramData%\home\svsystems\bin.

    c. Na guia **Gerenciar Contas**, selecione **Adicionar Conta**.

    d. Adicione a conta que você criou.

    e. Insira as credenciais usadas quando você habilitar a replicação de um computador.
