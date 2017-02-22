### <a name="prepare-for-push-install-on-linux-servers"></a>Preparar para instalação por push em servidores Linux

1. Verifique se há conectividade de rede entre o computador Linux e o servidor de processo.
2. Crie uma conta que pode ser usada pelo servidor de processo para acessar o computador. A conta deve ser um usuário **root** no servidor Linux de origem (essa conta é usada somente para instalação/atualizações por push).
3. Verifique se o arquivo `/etc/hosts` no servidor Linux de origem contém entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.
4. Instale os últimos pacotes openssh, openssh-server e openssl no computador que você deseja replicar.
5. Verifique se SSH está habilitado e em execução na porta 22.
6. Habilite a autenticação de subsistema e senha SFTP no arquivo sshd_config, como se segue:
  - Faça logon como **root**.
  - No arquivo /etc/ssh/sshd_config, localize a linha que começa com **PasswordAuthentication**.
  - Remova a marca de comentário da linha e altere o valor de **no** para **yes**.
   6.4 Localize a linha que começa com **Subsystem** e remova a marca de comentário existente nessa linha.

     ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

7. Adicione a conta criada na CSPSConfigtool.

    - Faça logon no Servidor de Configuração.
    - Abra **cspsconfigtool.exe**. (Ele está disponível como um atalho na área de trabalho e na pasta %ProgramData%\home\svsystems\bin)
    - Na guia **Gerenciar Contas**, clique em **Adicionar Conta**.
    - Adicione a conta que você criou. Depois de adicionar a conta, você precisa fornecer as credenciais ao habilitar a replicação em um computador.


<!--HONumber=Jan17_HO3-->


