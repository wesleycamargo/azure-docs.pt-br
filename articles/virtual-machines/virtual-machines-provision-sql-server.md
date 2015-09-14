<properties 
	pageTitle="Provisionar uma máquina virtual do SQL Server no Azure"
	description="Este tutorial ensina a criar e a configurar uma máquina virtual do SQL Server no Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>

<tags 
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2015"
	ms.author="jroth"/>

# Provisionar uma máquina virtual do SQL Server no Azure

> [AZURE.SELECTOR]
- [Portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)

## Visão geral

A galeria de Máquinas Virtuais do Azure inclui várias imagens que contêm o Microsoft SQL Server. Você pode selecionar uma das imagens de máquina virtual da galeria e, com apenas alguns cliques, provisioná-la para o ambiente do Azure.

Neste tutorial, você vai:

* [Conectar-se ao portal de gerenciamento do Azure e provisionar uma máquina virtual na galeria](#Provision)
* [Abrir a máquina virtual usando a Área de Trabalho Remota e concluir a instalação](#RemoteDesktop)
* [Concluir as etapas de configuração para conectar-se à máquina virtual usando o SQL Server Management Studio em outro computador](#SSMS)
* [Próximas etapas](#Optional)

##<a id="Provision">Provisionar uma máquina virtual do SQL Server por meio da galeria</a>

1. Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com) usando sua conta. Se você não tiver uma conta do Azure, visite [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

2. No Portal de Gerenciamento do Azure, na parte inferior esquerda da página da Web, clique em **+NOVO**, **COMPUTAÇÃO**, **MÁQUINA VIRTUAL** e, em seguida, em **DA GALERIA**.

3. Na página **Escolher uma imagem**, clique em **SQL SERVER**. Em seguida, selecione uma imagem do SQL Server. Clique na seta Avançar no canto inferior direito da página.

	![Escolha uma imagem](./media/virtual-machines-provision-sql-server/choose-sql-vm.png)

Para as informações mais atualizadas sobre as imagens do SQL Server com suporte no Azure, consulte [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-infrastructure-services.md).

>[AZURE.NOTE]Se você tiver uma máquina virtual criada usando a imagem da plataforma da edição de avaliação do SQL Server, não será possível atualizá-la para uma imagem de edição paga por minuto na galeria. Você pode escolher uma das duas seguintes opções:
>
> - Você pode criar uma nova máquina virtual usando a edição do SQL Server paga por minuto da Galeria e migrar seus arquivos de banco de dados para essa nova máquina virtual seguindo as etapas em [Migrar um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-migrate-onpremises-database)
> - Ou você pode atualizar uma instância existente da Edição de Avaliação do SQL Server para outra edição do SQL Server sob o contrato [License Mobility por meio do Software Assurance no Azure](http://azure.microsoft.com/pricing/license-mobility/) seguindo as etapas em [Atualizar para outra edição do SQL Server](https://msdn.microsoft.com/library/cc707783.aspx). Para obter informações sobre como adquirir a cópia licenciada do SQL Server, consulte [Como comprar o SQL Server (a página pode estar em inglês)](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx).

4. Na primeira página **Configuração de Máquina Virtual**, forneça as seguintes informações:
	- UMA **DATA DE LANÇAMENTO DA VERSÃO**. Se houver várias imagens, selecione a mais recente.
	- Um **NOME DA MÁQUINA VIRTUAL** exclusivo.
	- Na caixa **NOVO NOME DE USUÁRIO**, digite um nome de usuário exclusivo para a conta de administrador local da máquina.
	- Na caixa **NOVA SENHA**, digite uma senha forte. 
	- Na caixa **CONFIRMAR SENHA**, digite a senha novamente.
	- Selecione o **Tamanho** apropriado na lista suspensa. 

	![Configuração da VM](./media/virtual-machines-provision-sql-server/4VM-Config.png)

	>[AZURE.NOTE]O tamanho da máquina virtual é especificado durante o provisionamento:
 	>
	> - Um2 é o tamanho menor recomendado para cargas de trabalho de produção. 
    > - O tamanho mínimo recomendado para uma máquina virtual é A3 ao usar o SQL Server Enterprise Edition.
    > - Selecione A3 ou maior ao usar o SQL Server Enterprise Edition.
   	> - Selecione A4 ou superior ao usar imagens do SQL Server 2012 ou 2014 Enterprise Otimizado para Cargas de Trabalho Transacionais.  
   	> - Selecione A7 ou superior ao usar imagens do SQL Server 2012 ou 2014 Enterprise Otimizado para Cargas de Trabalho de Data Warehousing. 
   	> - Para obter um melhor desempenho, use DS2 ou DS3 com Armazenamento Premium. Para obter mais informações, consulte [Práticas recomendadas para o desempenho do SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-performance-best-practices.md).
   	> - O tamanho selecionado limita o número de discos de dados que você pode configurar. Para obter informações mais atualizadas sobre tamanhos de máquina virtual disponíveis e o número de discos de dados que você pode anexar a uma máquina virtual, consulte [Tamanhos de Máquina Virtual para o Azure](virtual-machines-size-specs.md) .

5. Depois de inserir os detalhes de configuração de VM, clique na seta Avançar no canto inferior direito para continuar.

5. Na segunda página **Configuração da máquina virtual**, configure recursos para rede, armazenamento e disponibilidade:
	- Na caixa **Serviço de Nuvem**, selecione **Criar um novo serviço de nuvem**.
	- Na caixa **Nome DNS do Serviço de Nuvem**, forneça a primeira parte de um nome DNS de sua escolha, para que ele complete um nome no formato **TESTNAME.cloudapp.net** 
	- Selecione uma **ASSINATURA**, se você tiver várias assinaturas para escolher. A escolha determina quais **contas de armazenamento** estão disponíveis.
- Na caixa **Região/Grupo de Afinidade/Rede Virtual**, selecione uma região onde essa imagem virtual será hospedada.
	- Na **conta de armazenamento**, gere automaticamente uma conta, ou selecione uma na lista. Altere a **ASSINATURA** para ver mais contas. 
	- Na caixa **CONJUNTO DE DISPONIBILIDADE**, selecione **(nenhum)**.
	- Leia e aceite o termos legais.
	

6. Clique na seta de avanço para continuar.


7. Clique na marca de seleção no canto inferior à direita para continuar.

8. Aguarde enquanto o Azure prepara sua máquina virtual. Espere que o status de máquina virtual prossiga por:

	- **Iniciando (Provisionamento)**
	- **Interrompido**
	- **Iniciando (Provisionamento)**
	- **Executando (Provisionamento)**
	- **Executando**
	

##<a id="RemoteDesktop">Abra a VM usando a Área de Trabalho Remota para concluir a configuração</a>

1. Quando o provisionamento for concluído, clique no nome da sua máquina virtual para ir para a página PAINEL. Na parte inferior da página, clique em **Conectar**.

2. Clique no botão **Abrir**.

	![Clique no botão Abrir](./media/virtual-machines-provision-sql-server/click-open-to-connect.png)

3. Na caixa de diálogo **Segurança do Windows**, clique em **Usar outra conta**.

	![Clique em Usar outra conta](./media/virtual-machines-provision-sql-server/credentials.png)

4. Use o nome do computador como o nome de domínio, seguido pelo nome do seu administrador neste formato:.`machinename\username` Digite sua senha e conecte-se ao computador.

4. Na primeira vez que você fizer logon, vários processos serão concluídos, incluindo a configuração da área de trabalho, atualizações do Windows e a conclusão das tarefas de configuração inicial do Windows (sysprep). Depois que o sysprep do Windows é concluído, a configuração do SQL Server conclui as tarefas de configuração. Essas tarefas podem causar um atraso de alguns minutos enquanto são concluídas. O `SELECT @@SERVERNAME` pode não retornar o nome correto até a conclusão da instalação do SQL Server e o SQL Server Management Studio pode não ser visível na página inicial.

Quando você está conectado à máquina virtual com a Área de Trabalho Remota do Windows, a máquina virtual funciona de maneira muito semelhante à qualquer outro computador. Conecte-se à instância padrão do SQL Server com o SQL Server Management Studio (em execução na máquina virtual) da maneira normal.

##<a id="SSMS">Conectar à instância VM do SQL Server do SSMS em outro computador</a>

[AZURE.INCLUDE [Conectar-se ao SQL Server em uma VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

## <a id="cdea">Conectar-se ao Mecanismo de Banco de Dados em seu aplicativo</a>

Se você puder se conectar a uma instância do SQL Server em execução em uma máquina virtual do Azure usando o Management Studio, você poderá se conectar usando uma cadeia de conexão semelhante à seguinte.

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Para obter mais informações, consulte [Como solucionar problemas de conexão com o Mecanismo de Banco de Dados do SQL Server (a página pode estar em inglês)](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

##<a id="Optional">Próximas etapas</a>

Você viu como criar e configurar um SQL Server em uma máquina virtual do Azure usando a imagem de plataforma. Em muitos casos, a próxima etapa é migrar os bancos de dados para essa nova VM do SQL Server. Para obter diretrizes sobre a migração de banco de dados, consulte [Como migrar um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-migrate-onpremises-database.md).

A lista a seguir fornece recursos adicionais para o SQL Server em máquinas virtuais do Azure.

### Recursos recomendados para o SQL Server em VMs do Azure:
- [Visão geral do SQL Server em máquinas virtuais do Azure](virtual-machines-sql-server-infrastructure-services.md)

- [Conectar-se a uma máquina virtual do SQL Server no Azure](virtual-machines-sql-server-connectivity.md)

- [Práticas recomendadas para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-performance-best-practices.md)

- [Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-security-considerations.md)

### Alta Disponibilidade e Recuperação de Desastre:
- [Alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)

- [Backup e restauração para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-backup-and-restore.md)

### Cargas de trabalho do SQL Server no Azure:
- [Business Intelligence do SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-business-intelligence.md)

### White papers:
- [Compreender o banco de dados SQL Azure e o SQL Server em máquinas virtuais do Azure](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-application-patterns-and-development-strategies.md)

<!---HONumber=September15_HO1-->