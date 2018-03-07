### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Preparação para uma instalação por push em um servidor Linux

1. Verifique se há conectividade de rede entre o computador Linux e o servidor de processo.
2. Crie uma conta que o servidor de processo possa usar para acessar o computador. A conta deve ser de um usuário **raiz** no servidor Linux de origem. Use essa conta somente para a instalação por push e atualizações.
3. Verifique se o arquivo /etc/hosts no servidor Linux de origem contém entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.
4. Instale os pacotes openssh, openssh-server e openssl mais recentes no computador que você deseja replicar.
5. Verifique se o Secure Shell (SSH) está habilitado e em execução na porta 22.
6. Habilitar a autenticação de senha e subsistema SFTP no arquivo sshd_config. Siga estas etapas:

    a. Conecte-se como **raiz**.

    b. No arquivo **/etc/ssh/sshd_config**, localize a linha que começa com **PasswordAuthentication**.

    c. Remova a marca de comentário da linha e altere o valor para **yes**.

    d. Localize a linha que começa com **Subsystem** e remova a marca de comentário da linha.

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. Reinicie o serviço **sshd**.

7. Adicione a conta criada em CSPSConfigtool. Siga estas etapas:

    a. Conecte-se ao servidor de configuração.

    b. Abra **cspsconfigtool.exe**. Ele está disponível como atalho na área de trabalho e na pasta %ProgramData%\home\svsystems\bin.

    c. Na guia **Gerenciar Contas**, selecione **Adicionar Conta**.

    d. Adicione a conta que você criou.

    d. Insira as credenciais usadas quando você habilitar a replicação de um computador.
