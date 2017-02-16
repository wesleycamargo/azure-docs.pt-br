### <a name="prepare-for-push-install-on-windows-machines"></a>Preparar para a instalação por push em máquinas com Windows

1. Verifique se há conectividade de rede entre o computador Windows e o servidor de processo.
2. Crie uma conta que pode ser usada pelo servidor de processo para acessar o computador. A conta deve ter privilégios de administrador (local ou domínio) e é usada somente para a instalação por push e as atualizações de agente.

   > [!NOTE]
   > Se você não estiver usando uma conta de domínio, precisará desabilitar o controle de Acesso de Usuário Remoto no computador local. Você executa desabilita o controle de Acesso de Usuário Remoto ao adicionar uma nova chave do Registro DWORD **LocalAccountTokenFilterPolicy** under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System e define seu valor como 1. Aqui está o comando para fazer a mesma coisa de um prompt de comando **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. No Firewall do Windows do computador que você deseja proteger, selecione **Permitir um aplicativo ou recurso pelo Firewall**. Habilite o **Compartilhamento de Arquivo e Impressora** e a **Instrumentação de Gerenciamento do Windows**. Para computadores que pertencem a um domínio, é possível definir as configurações do firewall com um GPO.

   ![Configurações de firewall](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. Adicione a conta criada na CSPSConfigtool.
    - Faça logon no Servidor de Configuração.
    - Abra **cspsconfigtool.exe**. (Ele está disponível como um atalho na área de trabalho e na pasta %ProgramData%\home\svsystems\bin)
    - Na guia **Gerenciar Contas**, clique em **Adicionar Conta**.
    - Adicione a conta que você criou. Depois de adicionar a conta, você precisa fornecer as credenciais ao habilitar a replicação em um computador.


<!--HONumber=Jan17_HO3-->


