<properties
	pageTitle="Introdução ao backup virtual da máquina do Azure"
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

Esta seção fornece uma introdução ao uso do Microsoft Azure Backup para proteger as máquinas virtuais do Azure. Ao fazer sua leitura, você aprenderá sobre:

- Como o Azure funciona de backup de máquina virtual
- O procedimento para fazer backup de sua máquina virtual do Azure
- Os pré-requisitos para obter uma experiência positiva de backup
- Os erros típicos encontrados e como lidar com eles
- A lista de cenários sem suporte e como influenciar a alterações no produto

Para saber mais sobre máquinas virtuais do Azure rapidamente, consulte o [documentação de máquina Virtual](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Por que fazer backup de máquina virtual do Azure?
A computação em nuvem permite que os aplicativos executados em um ambiente flexível e altamente disponível – o motivo pelo qual a Microsoft desenvolveu máquinas virtuais do Azure. Os dados gerados por essas máquinas virtuais do Azure é importantes e exigem backup para fins de proteção. São cenários típicos que exigem dados a ser restaurado a partir de backups:

- Exclusão acidental ou mal-intencionada de arquivos
- Corrupção da máquina virtual durante uma atualização de patch
- Exclusão acidental ou mal-intencionado de toda a máquina virtual

Podem ser feito backup dos dados dessas máquinas virtuais de duas maneiras distintas:

- Fazer backup as fontes individuais de dados de máquina virtual
- Fazer backup da máquina virtual inteira

Faça backup de toda a máquina virtual é popular porque é muito mais simples de gerenciar e ele também facilita o fácil restaurações do sistema operacional e aplicativo inteiro. O Backup do Azure pode ser usado para backup da máquina virtual completa ou backup de dados no convidado.

Os benefícios comerciais do usando o Backup do Azure para backup de máquinas virtuais são:

- Automação dos fluxos de trabalho de backup e recuperação para máquinas virtuais
- Backups consistentes com aplicativos para assegurar que recuperou dados inicia de um estado consistente.
- Nenhum tempo de inatividade envolvido durante o backup de máquinas virtuais.
- Windows ou máquinas virtuais Linux pode fazer backup.
- Pontos de recuperação estão disponíveis para fácil restauração no cofre de Backup do Azure.
- Automática remoção e coleta de lixo de pontos de recuperação mais antigos.

## Como funciona o backup da máquina virtual do Azure?
Para fazer backup de uma máquina virtual, primeiro é necessário um instantâneo point-in-time dos dados. O serviço de Backup do Azure inicia o trabalho de backup no horário agendado e dispara a extensão de backup para obter um instantâneo. A extensão de backup coordenado com o serviço VSS no convidado para obter consistência e chama a API de instantâneo de blob do serviço de armazenamento do Azure depois de consistência foi atingida. Isso é feito para obter um instantâneo consistente dos discos da máquina virtual, sem a necessidade de desligá-lo.

Depois que o instantâneo foi tirado, os dados são transferidos pelo serviço do Azure Backup para o Cofre de backup. O serviço se encarrega de identificação e transferir somente os blocos que foram alterados desde o último backup – tornando o armazenamento de backups eficientes. Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado. Esse ponto de recuperação pode ser visto no portal de gerenciamento do Azure.

![Arquitetura de backup da máquina virtual do Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

>[AZURE.NOTE]Para máquinas virtuais Linux, é possível apenas consistente de arquivo de backup.

## Calculando protegido instâncias
Máquinas virtuais do Azure backup usando o Backup do Azure estará sujeito [preços do Azure Backup](http://azure.microsoft.com/pricing/details/backup/). O cálculo de instâncias protegido se baseia o *real* tamanho da máquina virtual, que é a soma de todos os dados na máquina virtual – exceto o "disco de recurso". Você está *não* cobrado com base no tamanho máximo com suporte para cada disco de dados anexado à máquina virtual, mas os dados reais armazenados no disco de dados. Da mesma forma, a cobrança de armazenamento de backup é baseada na quantidade de dados armazenados com o Backup do Azure, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, executar uma máquina virtual padrão A2 porte que tem dois discos de dados adicionais com um tamanho máximo de 1TB. A tabela a seguir fornece os dados reais armazenados em cada um desses discos:

|Tipo de disco|Tamanho máximo|Dados reais presentes|
|---------|--------|------|
| Disco do sistema operacional | 1023GB | 17GB |
| Disco local / disco de recurso | 135GB | 5GB (não incluído no backup) |
| Disco de dados 1 |	1023GB | 30GB |
| Disco de dados 2 | 1023GB | 0GB |

O *real* nesse caso, o tamanho da máquina virtual é 17 GB + 30 GB + 0 GB = 47 GB. Isso se torna o tamanho de instância protegidos da fatura mensal com base em. Conforme aumenta a quantidade de dados na máquina virtual, o tamanho de instância protegidos usado para cobrança também serão alteradas apropriadamente.

A cobrança não é iniciado até que o primeiro backup bem-sucedido é concluído. Neste ponto começará a cobrança de armazenamento e instâncias protegido. A cobrança continuará desde que haja *qualquer backup dos dados armazenados com o Azure Backup* para a máquina virtual. Executando a operação parar proteção não interrompe a cobrança se os dados de backup são mantidos. A cobrança para uma máquina virtual especificada será descontinuada somente se a proteção for interrompida *e* os dados de backup são excluídos. Quando não há nenhum trabalhos de backup ativos (quando a proteção foi interrompida), o tamanho da máquina virtual no momento do último backup bem-sucedido torna-se o tamanho de instância protegidos da fatura mensal com base em.

## Pré-requisitos
### 1. Cofre de backup
Para iniciar o backup de máquinas virtuais do Azure, você precisa primeiro criar um cofre de backup. O cofre é uma entidade que armazena todos os backups e pontos de recuperação que foram criados ao longo do tempo. O cofre também contém as políticas de backup que serão aplicadas às máquinas virtuais que está sendo feitas backup.

A imagem a seguir mostra as relações entre as várias entidades de Backup do Azure: ![Relação e entidades de Backup do azure](./media/backup-azure-vms-introduction/vault-policy-vm.png)

### Para criar um cofre de backup

1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com/).

2. Clique em **novo** > **Data Services** > **Serviços de recuperação** > **cofre de Backup** > **criação rápida**. Se você tiver várias assinaturas associadas à sua conta organizacional, escolha a assinatura correta para associar o Cofre de backup. Em cada assinatura do Azure, você pode ter vários cofres de backup para organizar as máquinas virtuais que estão sendo protegidas.

3. Em **Nome**, digite um nome amigável para identificar o cofre. Isso precisa ser exclusivo para cada assinatura.

4. Em **Região**, selecione a região geográfica para o cofre. Observe que o cofre deve estar na mesma região que as máquinas virtuais que deseja proteger. Se você tiver máquinas virtuais em diferentes regiões crie um cofre em cada um. Não é necessário especificar contas de armazenamento para armazenar os dados de backup – o Cofre de backup e o serviço de Backup do Azure cuidará disso automaticamente. ![Criar Cofre de backup](./media/backup-azure-vms-introduction/backup_vaultcreate.png)

    >[AZURE.NOTE]Máquina virtual backup usando o serviço de Backup do Azure tem suporte apenas em Selecionar regiões. Verificar lista de [suporte para regiões](http://azure.microsoft.com/regions/#services). Se a região que você está procurando não há suporte para hoje, ela não aparecerá na lista suspensa durante a criação de cofre.

5. Clique em **criar cofre**. Pode levar algum tempo para que o cofre de backup seja criado. Monitore as notificações de status na parte inferior do portal. ![Criar notificação de cofre](./media/backup-azure-vms-introduction/creating-vault.png)

6. Uma mensagem confirmará que o cofre foi criado com êxito e ele será listado na página de serviços de recuperação como ativo. ![Lista de cofres de backup](./media/backup-azure-vms-introduction/backup_vaultslist.png)

7. Clicar no cofre de backup vai para o **início rápido** página, em que as instruções para backup de máquinas virtuais do Azure são mostradas. ![Instruções de backup da máquina virtual na página painel](./media/backup-azure-vms-introduction/vmbackup-instructions.png)

    >[AZURE.NOTE]Certifique-se de que a opção de redundância de armazenamento apropriado é escolhida logo após o cofre foi criado. Leia mais sobre [definindo a opção de redundância de armazenamento no cofre de backup][vault-storage-redundancy].

### 2. Agente de VM
Antes de iniciar a máquina virtual do Azure de backup, certifique-se de que o Azure VM Agent está instalado corretamente na máquina virtual. Para fazer backup da máquina virtual, o serviço de Backup do Azure instala uma extensão para o agente de VM. Como o VM agent é um componente opcional no momento em que a máquina virtual é criada, você precisa garantir que a caixa de seleção para que o agente VM está selecionada antes da máquina virtual é provisionada.

Saiba mais sobre o [VM Agent](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e [como instalá-lo](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

>[AZURE.NOTE]Se você estiver planejando migrar sua máquina virtual de seu datacenter local para Azure, certifique-se de que você baixe e instale o VM agent MSI antes de iniciar o processo de migração. Isso também se aplica a máquinas virtuais protegidas no Azure usando o Azure Site Recovery.

## Limitações durante a visualização

- Não há suporte para o backup de máquinas virtuais com mais de cinco discos.
- Não há suporte para o backup de máquinas virtuais usando armazenamento Premium.
- Não há suporte para o backup de máquinas virtuais usando várias placas de rede ou em uma configuração de balanceamento de carga.
- Não há suporte para a substituição de uma máquina virtual existente durante a restauração. Primeiro, exclua a máquina virtual existente e todos os discos associados e, em seguida, restaurar os dados de backup.
- Não há suporte para o backup de máquinas virtuais restaurado com recuperação de Site do Azure.
- Não há suporte entre regiões backup e restauração.
- Máquina virtual backup usando o serviço de Backup do Azure tem suporte apenas em Selecionar regiões. Verificar lista de [suporte para regiões](http://azure.microsoft.com/regions/#services). Se a região que você está procurando não há suporte para hoje, ela não aparecerá na lista suspensa durante a criação de cofre.
- Backup de máquinas virtuais usando o serviço de Backup do Azure só tem suporte somente para versões de sistema operacional selecionadas:
  - **Linux**: A lista de distribuições endossadas pelo Azure está disponível [aqui](../virtual-machines-linux-endorsed-distributions.md). Outras distribuições de trazer-o-proprietário-Linux também devem funcionar como o agente de VM está disponível na máquina virtual.
  - **Windows Server**: não há suporte para versões anteriores do Windows Server 2008 R2.

Se houver algum recurso que você gostaria de ver incluído, [Enviar comentários](http://aka.ms/azurebackup_feedback).

## Próximas etapas
Para iniciar o backup de máquinas virtuais, saiba como:

- [Detectar, registrar e proteger as máquinas virtuais](backup-azure-vms.md)

- [Restaurar máquinas virtuais](backup-azure-restore-vms.md)

+ Monitorar os trabalhos de backup

<!---HONumber=GIT-SubDir--> 