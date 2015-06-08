<properties
	pageTitle="O Backup do Azure - criar um cofre de backup e especificar a redundância de armazenamento"
	description="Aprenda a criar um cofre de backup, especifique as opções de redundância de armazenamento no Backup do Azure"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="04/08/2015"
	 ms.author="prvijay"/>

# Criar um cofre de Backup
Para fazer backup de arquivos e dados do System Center Data Protection Manager (SCDPM) ou Windows Server no Azure ou ao fazer backup de VMs de IaaS no Azure, você deve criar um cofre de backup na região geográfica onde você deseja armazenar os dados.

Este tutorial o orientará através da criação de cofre que você usará para armazenar backups.

1. Entrar para a [Portal de gerenciamento](https://manage.windowsazure.com/)
2. Clique em **novo** -> **Data Services** -> **Serviços de recuperação** -> **cofre de Backup** e escolha **criação rápida** <br/> ![Criar cofre][1]

3. Para o **nome** parâmetro, digite um nome amigável para identificar o Cofre de backup. Isso precisa ser exclusivo para cada assinatura.

4. Para o **região** parâmetro, selecione a região geográfica para o Cofre de backup. A escolha determina a localização geográfica para que os dados de backup são enviados. Escolhendo um geograficamente próximo de sua localidade, você pode reduzir a latência da rede ao fazer o backup do Azure.

5. Clique em **criar cofre** para concluir o fluxo de trabalho. Pode levar algum tempo para que o cofre de backup seja criado. Para verificar o status, você pode monitorar as notificações na parte inferior do portal. <br/> ![Criação de cofre][2]

6. Depois que o Cofre de backup foi criado, uma mensagem informará o cofre foi criado com êxito e ele será listado nos recursos para serviços de recuperação como **ativos**. <br/> ![Criando o status do cofre][3]


## Backup do Azure - opções de redundância de armazenamento

O melhor momento para identificar sua opção de redundância de armazenamento é logo após a criação de um cofre e antes de todas as máquinas são registradas no cofre. Depois que um item tiver sido registrado no cofre, a opção de redundância de armazenamento está bloqueada e não pode ser modificada.

Determinam a redundância de armazenamento do armazenamento de back-end do Azure Backup suas necessidades comerciais. Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup principal (por exemplo, você estiver fazendo backup para o Azure de um servidor do Windows), você deve considerar a opção de armazenamento com redundância geográfica separação (o padrão). Isso é visto sob o **Configurar** opção de seu Cofre de Backup. <br/> ![GRS][4]

### Armazenamento com redundância geográfica (GRS)
O GRS mantém seis cópias de seus dados. Com o GRS, seus dados são replicados três vezes na região primária e também são replicados três vezes para uma região secundária a centenas de quilômetros de distância da região primária, oferecendo o nível mais alto de durabilidade. Em caso de falha na região principal, armazenando dados em GRS, Backup do Azure garante que os dados sejam duráveis em duas regiões separadas.

### Armazenamento com redundância local (LRS)
O LRS (armazenamento com redundância local) mantém três cópias de seus dados. O LRS é replicado três vezes em uma única instalação, em uma única região. O LRS protege seus dados contra falhas de hardware normais, mas não da falha de um recurso do Azure inteiro.

Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup terciário (por exemplo, você está usando SCDPM para ter um backup local copiar local & usando o Azure para suas necessidades de sua retenção de longo prazo), convém escolher o local de armazenamento com redundância do **Configurar** opção de seu Cofre de Backup. Isso traz o custo de armazenamento de dados no Azure, fornecendo um nível mais baixo de durabilidade para seus dados podem ser aceitáveis para cópias terciárias. <br/> ![LRS][5]




## Observação

+ Desde março de 2015, os clientes não têm uma programação (por exemplo: PowerShell) maneira de criar um cofre de backup.

+ A redundância de armazenamento deve ser selecionada logo após a criação de um cofre e antes de todas as máquinas são registradas no cofre. Depois que um item tiver sido registrado no cofre, a opção de redundância de armazenamento está bloqueada e não pode ser modificada.

<!--Image references-->
[1]: ./media/backup-azure-backup-create-vault/createvault1.png
[2]: ./media/backup-azure-backup-create-vault/creatingvault1.png
[3]: ./media/backup-azure-backup-create-vault/backupvaultstatus1.png
[4]: ./media/backup-azure-backup-create-vault/grs.png
[5]: ./media/backup-azure-backup-create-vault/lrs.png

<!---HONumber=GIT-SubDir-->