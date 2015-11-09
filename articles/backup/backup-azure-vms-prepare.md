<properties
	pageTitle="Preparando seu ambiente para fazer backup de máquinas virtuais do Azure | Microsoft Azure"
	description="Verifique que seu ambiente está preparado para fazer backup de máquinas virtuais do Azure"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>

# Preparando seu ambiente para fazer backup de máquinas virtuais do Azure
Antes de fazer o backup de uma máquina virtual do Azure, você precisa concluir alguns pré-requisitos para preparar seu ambiente. Se você tiver feito isso, você pode iniciar o [backup de suas VMs](backup-azure-vms.md), caso contrário, siga as etapas abaixo para verificar se seu ambiente está preparado.

## 1\. Cofre de backup
Para iniciar o backup de suas máquinas virtuais do Azure, você precisa primeiro criar um cofre de backup. O cofre é uma entidade que armazena todos os backups e pontos de recuperação que foram criados ao longo do tempo. O cofre também contém as políticas de backup que serão aplicadas às máquinas virtuais que passarão por backup.

Esta imagem mostra os relacionamentos entre as várias entidades do Backup do Azure: ![Relação e entidades do Backup do Azure](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Para criar um cofre de backup:

1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com/).

2. Clique em **Novo** -> **Serviços de Dados** -> **Serviços de Recuperação** -> **Cofre de Backup** > **Criação Rápida**. Se você tem várias assinaturas associadas à sua conta organizacional, escolha a assinatura correta a ser associada ao cofre de backup. Em cada assinatura do Azure, você pode ter vários cofres de backup para organizar as máquinas virtuais que estão sendo protegidas.

3. Em **Nome**, digite um nome amigável para identificar o cofre. Ele precisa ser exclusivo para cada assinatura.

4. Em **Região**, selecione a região geográfica para o cofre. O cofre deve estar na mesma região que as máquinas virtuais que você deseja proteger. Se você tiver máquinas virtuais em diferentes regiões, crie um cofre em cada uma delas. Não é necessário especificar contas de armazenamento para armazenar os dados de backup – o cofre de backup e o serviço de Backup do Azure cuidarão disso automaticamente.

    ![Criar cofre de backup](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. Clique em **Criar cofre**. Pode levar algum tempo para que o cofre de backup seja criado. Monitore as notificações de status na parte inferior do portal.

    ![Criar notificação de cofre](./media/backup-azure-vms-prepare/creating-vault.png)

6. Uma mensagem confirmará que o cofre foi criado com êxito e ele será listado na página de Serviços de Recuperação como Ativo. Certifique-se de que a opção apropriada de redundância de armazenamento esteja marcada logo depois que o cofre for criado. Leia mais sobre [como definir a opção de redundância de armazenamento no cofre de backup](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Lista de cofres de backup](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. Clique no cofre de backup para abrir a página de **Início Rápido** na qual são exibidas as instruções para fazer o backup de máquinas virtuais do Azure.

    ![Instruções de backup de máquina virtual na página Painel](./media/backup-azure-vms-prepare/vmbackup-instructions.png)

## 2\. Agente de VM
Antes de iniciar o backup da máquina virtual do Azure, verifique se o Agente de VM do Azure está instalado corretamente na máquina virtual. Como o agente de VM é um componente opcional no momento em que a máquina virtual é criada, verifique se a caixa de seleção do Agente de VM está marcada antes que a máquina virtual seja provisionada.

Saiba mais sobre o [Agente de VM](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e [como instalá-lo](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### Extensão de backup
Para fazer backup da máquina virtual, o serviço do Backup do Azure instala uma extensão para o Agente de VM. O serviço do Backup do Azure atualiza e corrige perfeitamente a extensão de backup sem intervenção adicional do usuário.

A extensão de backup será instalada se a VM estiver em execução. Uma VM em execução também oferece uma maior chance de obter um ponto de recuperação consistente com o aplicativo. No entanto, o serviço do Backup do Azure continuará a realizar o backup da VM mesmo quando esta estiver desativada e a extensão não puder ser instalada (também conhecida como VM Offline). Nesse caso, o ponto de recuperação será o *Controle de falhas* conforme discutido anteriormente.

## 3\. Conexão de rede
A extensão de backup precisa estar conectada à Internet para funcionar corretamente, pois ela envia comandos para um ponto de extremidade do Armazenamento do Azure (URL de HTTP) para gerenciar os instantâneos da VM. Sem conexão com a Internet, estas solicitações HTTP da VM atingirão o tempo limite e a operação de backup falhará.

## Limitações

- Não há suporte para o backup de máquinas virtuais baseadas no Gerenciador de Recursos do Azure (também conhecido como IaaS V2).
- Não há suporte para o backup de máquinas virtuais com mais de 16 discos de dados.
- Não há suporte para o backup de máquinas virtuais usando o Armazenamento Premium.
- Não há suporte para o backup de máquinas virtuais com múltiplos IPs reservados.
- Não há suporte para o backup de máquinas virtuais com um IP reservado e nenhum ponto de extremidade definido.
- Não há suporte para o backup de máquinas virtuais usando vários NICs.
- Não há suporte para o backup de máquinas virtuais em uma configuração de balanceamento de carga (interna e voltada para a Internet).
- Não há suporte para a substituição de uma máquina virtual existente durante a restauração. Primeiro, exclua a máquina virtual existente e todos os discos associados e, em seguida, restaure os dados do backup.
- Não há suporte para backup e restauração entre regiões.
- Há suporte para o backup de máquinas virtuais usando o serviço do Backup do Azure em todas as regiões públicas do Azure. Veja aqui uma [lista de verificação](http://azure.microsoft.com/regions/#services) das regiões com suporte. Se a região que você procura ainda não tem suporte, ela não aparecerá na lista suspensa durante a criação de cofre.
- O backup de máquinas virtuais usando o serviço do Backup do Azure tem suporte somente para determinadas versões de Sistema Operacional:
  - **Linux**: a lista de distribuições aprovadas pelo Azure está disponível [aqui](../virtual-machines-linux-endorsed-distributions.md). Outras distribuições personalizadas do Linux também devem funcionar, contanto que o agente de VM esteja disponível na máquina virtual.
  - **Windows Server**: não há suporte para versões anteriores ao Windows Server 2008 R2.
- A restauração de uma VM controladora de domínio que é parte de uma configuração multi-DC tem suporte somente através do PowerShell. Leia mais sobre [como restaurar um controlador de domínio com vários DCs](backup-azure-restore-vms.md#restoring-domain-controller-vms)

## Perguntas?
Se houver dúvidas ou algum recurso que você gostaria de ver incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

## Próximas etapas

- [Planeje sua infraestrutura de backup da VM](backup-azure-vms-introduction.md)
- [Backup de máquinas virtuais](backup-azure-vms.md)
- [Gerenciar o backup de máquinas virtuais](backup-azure-manage-vms.md)

<!---HONumber=Nov15_HO1-->