## Criar um cofre de backup para um VM

O cofre de backup é uma entidade que armazena todos os pontos de backups e recuperação que foram criados ao longo do tempo. O cofre de backup também contém as políticas de backup que serão aplicadas às máquinas virtuais que passarão pelo backup.

Esta imagem mostra os relacionamentos entre as várias entidades do Backup do Azure: ![Relação e entidades do Backup do Azure](./media/backup-create-vault-for-vms/vault-policy-vm.png)

Para criar um cofre de backup:

1. Entre no [Portal do Azure](http://manage.windowsazure.com/).

2. No portal do Azure, clique em **Novo** > **Integração Híbrida** > **Backup**. Ao clicar em **Backup**, você alternará automaticamente para o portal clássico (mostrado após a Observação).

    ![Portal do Ibiza](./media/backup-create-vault-for-vms/Ibiza-portal-backup01.png)

    >[AZURE.NOTE] Se sua assinatura foi usada pela última vez no portal clássico, ela poderá ser aberta no portal clássico. Nesse caso, para criar um cofre de backup, clique em **Novo** > **Serviços de Dados** > **Serviços de Recuperação** > **Cofre de Backup** > **Criação Rápida** (veja a imagem abaixo).

    ![Criar cofre de backup](./media/backup-create-vault-for-vms/backup_vaultcreate.png)

3. Para **Nome**, insira um nome amigável para identificar o cofre. O nome deve ser exclusivo para a assinatura do Azure. Digite um nome que contenha de 2 a 50 caracteres. Ele deve começar com uma letra e pode conter apenas letras, números e hifens.

4. Em **Região**, selecione a região geográfica para o cofre. O cofre deve estar na mesma região que as máquinas virtuais que você deseja proteger. Se tiver máquinas virtuais em várias regiões, será necessário criar um cofre de backup em cada região. Não é necessário especificar contas de armazenamento para armazenar os dados de backup; o cofre de backup e o serviço de Backup do Azure cuidarão disso automaticamente.

5. Em **Assinatura**, selecione a assinatura que deseja associar ao cofre de backup. Haverá várias opções somente se sua conta organizacional estiver associada a várias assinaturas do Azure.

5. Clique em **Criar cofre**. Pode levar algum tempo para que o cofre de backup seja criado. Monitore as notificações de status na parte inferior do portal.

    ![Criar notificação de cofre](./media/backup-create-vault-for-vms/creating-vault.png)

6. Uma mensagem confirmará que o cofre foi criado com êxito. Ela será listada na página **Serviços de Recuperação** como **Ativa**. Verifique se a opção apropriada de redundância de armazenamento está marcada logo depois que o cofre for criado. Leia mais sobre [como definir a opção de redundância de armazenamento no cofre de backup](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Lista de cofres de backup](./media/backup-create-vault-for-vms/backup_vaultslist.png)

7. Clique no cofre de backup para abrir a página de **Início Rápido** na qual são exibidas as instruções para fazer o backup de máquinas virtuais do Azure.

    ![Instruções de backup de máquina virtual na página Painel](./media/backup-create-vault-for-vms/vmbackup-instructions.png)

<!---HONumber=AcomDC_0302_2016-->