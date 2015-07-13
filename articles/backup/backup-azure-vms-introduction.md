<properties
	pageTitle="Introdução ao backup de máquinas virtuais do Azure"
	description="Uma introdução ao backup de máquinas virtuais no Azure usando o serviço de Backup do Azure"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/26/2015"
	ms.author="aashishr"/>

# Backup de máquina virtual do Azure - Introdução

Esta seção fornece uma introdução ao uso do Backup do Microsoft Azure para proteger as máquinas virtuais do Azure. Lendo-o, você aprenderá sobre:

- Como funciona o backup de máquinas virtuais do Azure
- O procedimento para fazer backup de sua máquina virtual do Azure
- Os pré-requisitos para obter uma experiência positiva de backup
- Os erros típicos encontrados e como lidar com eles
- A lista de cenários sem suporte e como influenciar alterações no produto

Para saber mais sobre máquinas virtuais do Azure rapidamente, consulte a [Documentação da Máquina Virtual](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Por que fazer backup da máquina virtual do Azure?
A computação em nuvem permite que os aplicativos sejam executados em um ambiente escalonável e altamente disponível – por isso a Microsoft desenvolveu as máquinas virtuais do Azure. Os dados gerados por essas máquinas virtuais do Azure são importantes e exigem backup para fins de proteção. Os cenários típicos que exigem que os dados sejam restaurados de backups são:

- Exclusão acidental ou mal-intencionada de arquivos
- Corrupção da máquina virtual durante uma atualização de patch
- Exclusão acidental ou mal-intencionada de toda a máquina virtual

É possível fazer backup dos dados dessas máquinas virtuais de duas maneiras distintas:

- Fazer backup das fontes individuais de dados por meio da máquina virtual
- Fazer backup da máquina virtual inteira

O backup de toda a máquina virtual é popular porque é muito mais simples de gerenciar e também facilita restaurações do sistema operacional e do aplicativo inteiros. O Backup do Azure pode ser usado para backup de dados no convidado ou backup da máquina virtual completa.

Os benefícios comerciais do uso do Backup do Azure para backup de máquinas virtuais são:

- Automação dos fluxos de trabalho de backup e recuperação para máquinas virtuais
- Backups consistentes com aplicativos para assegurar que os dados recuperados sejam iniciados por meio de um estado consistente.
- Nenhum tempo de inatividade envolvido durante o backup de máquinas virtuais.
- É possível fazer backup de máquinas virtuais do Windows ou do Linux.
- Os pontos de recuperação estão disponíveis para fácil restauração no cofre de Backup do Azure.
- Remoção automática e coleta de lixo de pontos de recuperação mais antigos.

## Como funciona o backup de máquinas virtuais do Azure?
Para fazer backup de uma máquina virtual, primeiro é necessário um instantâneo pontual dos dados. O serviço de Backup do Azure inicia o trabalho de backup no horário agendado e dispara a extensão de backup para obter um instantâneo. A extensão de backup é coordenada com o serviço VSS no convidado para obter consistência e chama a API de instantâneo de blob do serviço de Armazenamento do Azure depois que a consistência é atingida. Isso é feito para obter um instantâneo consistente dos discos da máquina virtual, sem a necessidade de desligá-la.

Depois que o instantâneo é criado, os dados são transferidos pelo serviço do Backup do Azure para o cofre de backup. O serviço se encarrega de identificar e transferir somente os blocos que foram alterados desde o último backup – tornando o armazenamento de backups eficiente. Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado. Esse ponto de recuperação pode ser visto no portal de gerenciamento do Azure.

![Arquitetura de backup de máquinas virtuais do Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

>[AZURE.NOTE]Para máquinas virtuais do Linux, é possível apenas o backup consistente com arquivos.

## Calculando instâncias protegidas
As máquinas virtuais do Azure submetidas a backup usando o Backup do Azure estarão sujeitas aos [preços do Backup do Azure](http://azure.microsoft.com/pricing/details/backup/). O cálculo de instâncias protegidas se baseia no tamanho *real* da máquina virtual, que é a soma de todos os dados na máquina virtual – exceto o "disco de recursos". Você *não* é cobrado com base no tamanho máximo com suporte para cada disco de dados anexado à máquina virtual, mas sim nos dados reais armazenados no disco de dados. Da mesma forma, a cobrança do armazenamento de backup é baseada na quantidade de dados armazenados com o Backup do Azure, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, considere uma máquina virtual de tamanho A2-Standard que tem dois discos de dados adicionais com um tamanho máximo de 1 TB cada. A tabela a seguir fornece os dados reais armazenados em cada um desses discos:

|Tipo de disco|Tamanho máx.|Dados reais presentes|
|---------|--------|------|
| Disco do sistema operacional | 1023GB | 17GB |
| Disco local/disco de recursos | 135GB | 5 GB (não incluído no backup) |
| Disco de dados 1 |	1023GB | 30GB |
| Disco de dados 2 | 1023GB | 0GB |

Neste caso, o tamanho *real* da máquina virtual é de 17 GB + 30 GB + 0 GB = 47 GB. Isso se torna o tamanho da Instância Protegida em que a fatura mensal se baseia. Conforme aumenta a quantidade de dados na máquina virtual, o tamanho da Instância Protegida usada para cobrança também será alterado de forma adequada.

A cobrança não é iniciada até que o primeiro backup bem-sucedido seja concluído. Neste ponto, será iniciada a cobrança de instâncias Protegidas e de Armazenamento. A cobrança continuará desde que haja *dados de backup armazenados com o Backup do Microsoft Azure* para a máquina virtual. Executar a operação Parar Proteção não interrompe a cobrança se os dados de backup forem mantidos. A cobrança para uma máquina virtual especificada será descontinuada somente se a proteção for interrompida *e* os dados de backup forem excluídos. Quando não há nenhum trabalho de backup ativo (quando a proteção tiver sido interrompida), o tamanho da máquina virtual no momento do último backup bem-sucedido torna-se o tamanho da instância protegida em que a fatura mensal se baseia.

## Pré-requisitos
### 1. Cofre de backup
Para iniciar o backup de suas máquinas virtuais do Azure, você precisa primeiro criar um cofre de backup. O cofre é uma entidade que armazena todos os backups e pontos de recuperação que foram criados ao longo do tempo. O cofre também contém as políticas de backup que serão aplicadas às máquinas virtuais que passarão por backup.

A imagem abaixo mostra os relacionamentos entre as várias entidades do Backup do Azure: ![Relação e entidades do Backup do Azure](./media/backup-azure-vms-introduction/vault-policy-vm.png)

### Para criar um cofre de backup

1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com/).

2. Clique em **Novo** -> **Serviços de Dados** -> **Serviços de Recuperação** -> **Cofre de Backup** > **Criação Rápida**. Se você tem várias assinaturas associadas à sua conta organizacional, escolha a assinatura correta a ser associada ao cofre de backup. Em cada assinatura do Azure, você pode ter vários cofres de backup para organizar as máquinas virtuais que estão sendo protegidas.

3. Em **Nome**, digite um nome amigável para identificar o cofre. Ele precisa ser exclusivo para cada assinatura.

4. Em **Região**, selecione a região geográfica para o cofre. Observe que o cofre deve estar na mesma região que as máquinas virtuais que você deseja proteger. Se você tiver máquinas virtuais em diferentes regiões, crie um cofre em cada uma delas. Não é necessário especificar contas de armazenamento para armazenar os dados de backup – o cofre de backup e o serviço de Backup do Azure cuidarão disso automaticamente. ![Criar cofre de backup](./media/backup-azure-vms-introduction/backup_vaultcreate.png)

    >[AZURE.NOTE]O backup de máquinas virtuais com o serviço de Backup do Azure tem suporte somente em algumas regiões. Consulte a lista de [regiões com suporte](http://azure.microsoft.com/regions/#services). Se a região que você procura ainda não tem suporte, ela não aparecerá na lista suspensa durante a criação de cofre.

5. Clique em **Criar cofre**. Pode levar algum tempo para que o cofre de backup seja criado. Monitore as notificações de status na parte inferior do portal. ![Criar notificação de cofre](./media/backup-azure-vms-introduction/creating-vault.png)

6. Uma mensagem confirmará que o cofre foi criado com êxito e ele será listado na página de Serviços de Recuperação como Ativo. ![Lista de cofres de backup](./media/backup-azure-vms-introduction/backup_vaultslist.png)

7. Clicar no cofre de backup abre a página **Início Rápido**, em que são mostradas as instruções para backup de máquinas virtuais do Azure. ![Instruções de backup de máquina virtual na página Painel](./media/backup-azure-vms-introduction/vmbackup-instructions.png)

    >[AZURE.NOTE]Certifique-se de que a opção de redundância de armazenamento apropriada esteja selecionada logo depois que o cofre for criado. Leia mais sobre [como definir a opção de redundância de armazenamento no cofre de backup][redundância-de-armazenamento-no-cofre].

### 2. Agente de VM
Antes de iniciar o backup da máquina virtual do Azure, certifique-se de que o agente de VM do Azure esteja instalado corretamente na máquina virtual. Para fazer backup da máquina virtual, o serviço de Backup do Azure instala uma extensão para o agente de VM. Como o agente de VM é um componente opcional no momento em que a máquina virtual é criada, você precisa garantir que a caixa de seleção do agente de VM esteja marcada antes de a máquina virtual ser provisionada.

Saiba mais sobre o [Agente de VM](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e [como instalá-lo](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

>[AZURE.NOTE]Se você estiver planejando migrar sua máquina virtual de seu datacenter local para o Azure, certifique-se de baixar e instalar o MSI do agente de VM antes de iniciar o processo de migração. Isso também se aplica a máquinas virtuais protegidas no Azure usando o Azure Site Recovery.

## Limitações durante a visualização

- Não há suporte para o backup de máquinas virtuais com mais de cinco discos.
- Não há suporte para o backup de máquinas virtuais usando o armazenamento Premium.
- Não há suporte para o backup de máquinas virtuais usando várias NICs ou em uma configuração de balanceamento de carga.
- Não há suporte para a substituição de uma máquina virtual existente durante a restauração. Primeiro, exclua a máquina virtual existente e todos os discos associados e, em seguida, restaure os dados do backup.
- Não há suporte para o backup de máquinas virtuais restauradas o Azure Site Recovery.
- Não há suporte para backup e restauração entre regiões.
- O backup de máquinas virtuais com o serviço de Backup do Azure tem suporte somente em algumas regiões. Consulte a lista de [regiões com suporte](http://azure.microsoft.com/regions/#services). Se a região que você procura ainda não tem suporte, ela não aparecerá na lista suspensa durante a criação de cofre.
- O backup de máquinas virtuais com o serviço de Backup do Azure tem suporte somente para as versões selecionadas de sistema operacional:
  - **Linux**: a lista de distribuições aprovadas pelo Azure está disponível [aqui](../virtual-machines-linux-endorsed-distributions.md). Outras distribuições personalizadas do Linux também devem funcionar, contanto que o agente de VM esteja disponível na máquina virtual.
  - **Windows Server**: não há suporte para versões anteriores ao Windows Server 2008 R2.

Se houver algum recurso que você gostaria de ver incluído, [envie-nos comentários](http://aka.ms/azurebackup_feedback).

## Próximas etapas
Para começar a usar o backup de máquinas virtuais, saiba como:

- [Descobrir, registrar e proteger máquinas virtuais](backup-azure-vms.md)

- [Restaurar máquinas virtuais](backup-azure-restore-vms.md)

+ Monitorar trabalhos de backup


 

<!---HONumber=62-->