<properties
	pageTitle="Configurar o Gerenciador de Recursos do Azure dos grupos de disponibilidade AlwaysOn | Microsoft Azure"
	description="Crie um grupo de disponibilidade AlwaysOn com as máquinas virtuais do Azure no modo Gerenciador de Recursos do Azure. Este tutorial usa principalmente a interface do usuário para criar a solução inteira automaticamente."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="MikeRayMSFT"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-resource-manager" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/04/2016"
	ms.author="MikeRayMSFT" />

# Configurar um grupo de disponibilidade AlwaysOn nas máquinas virtuais do Gerenciador de Recursos do Azure (GUI)

> [AZURE.SELECTOR]
- [Portal - Resource Manager](virtual-machines-sql-server-alwayson-availability-groups-gui-arm.md)
- [Portal – Clássico](virtual-machines-windows-classic-portal-sql-availability.md)
- [PowerShell - clássico](virtual-machines-windows-classic-ps-sql-availability.md)

<br/>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modelo clássico.


Este tutorial de ponta a ponta mostra como criar um grupo de disponibilidade do SQL Server com máquinas virtuais do Gerenciador de Recursos do Azure. O tutorial usa folhas do Azure para configurar um modelo. Você revisará as configurações padrão, digitará as configurações necessárias e atualizará as folhas enquanto percorre este tutorial.

>[AZURE.NOTE] No Portal de Gerenciamento do Azure, há uma nova configuração de galeria para grupos de disponibilidade AlwaysOn com um Ouvinte. Isso configura tudo o que você precisa para grupos de disponibilidade AlwaysOn automaticamente. Para obter mais informações, consulte [Oferta do AlwaysOn do SQL Server na Galeria do portal clássico do Microsoft Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

Ao final do tutorial, sua solução SQL Server AlwaysOn no Azure consistirá nos seguintes elementos:

- Uma rede virtual que contém várias sub-redes, incluindo uma de front-end e uma de back-end

- Dois controladores de domínio com um domínio do AD (Active Directory)

- Duas VMs do SQL Server implantadas na sub-rede de back-end e ingressadas no domínio do AD

- Um cluster WSFC de três nós com o modelo de quórum de Nó Principal

- Um grupo de disponibilidade com duas réplicas de confirmação síncrona de um banco de dados de disponibilidade

A figura abaixo é uma representação gráfica da solução.

![Arquitetura de laboratório de teste para o AG no Azure](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/0-EndstateSample.png)

Todos os recursos nesta solução pertencem a um único grupo de recursos.

Este tutorial pressupõe o seguinte:

- Você já tem uma conta do Azure. Se não tiver uma, [inscreva-se em uma conta de avaliação](http://azure.microsoft.com/pricing/free-trial/).

- Você já sabe como provisionar uma VM do SQL Server da galeria de máquinas virtuais usando a GUI. Para obter mais informações, veja [Provisioning a SQL Server virtual machine in the Azure Portal](virtual-machines-windows-portal-sql-server-provision.md)

- Você já tem uma compreensão sólida dos grupos de disponibilidade AlwaysOn. Para saber mais, confira [Grupos de disponibilidade AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Se você estiver interessado em usar os grupos de disponibilidade AlwaysOn com o SharePoint, confira também [Configurar grupos de disponibilidade AlwaysOn do SQL Server 2012 no SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).

Neste tutorial, você usará o portal do Azure para:

- Selecionar o novo modelo de grupo de disponibilidade AlwaysOn no portal

- Examinar as configurações de modelo e atualizar alguns parâmetros de configuração para o seu ambiente

- Monitorar o Azure enquanto ele cria todo o ambiente

- Conectar-se a um dos controladores de domínio e a um dos SQL Servers

## Provisionar um grupo de disponibilidade AlwaysOn da galeria com o modelo de implantação do Gerenciador de Recursos

O Azure fornece uma imagem da galeria para a solução inteira. Para localizar o modelo:

1. 	Faça logon no portal do Azure usando sua conta.
1.	No Portal do Azure, clique em **+Novo**. O portal abrirá a folha Novo. 
1.	Na folha Novo, procure **AlwaysOn**. ![Localizar o modelo AlwaysOn](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/16-findalwayson.png)
1.	Nos resultados da pesquisa, localize **Cluster do SQL Server AlwaysOn**. ![Modelo AlwaysOn](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/17-alwaysontemplate.png)
1.	Em **Selecione um modelo de implantação**, escolha **Resource Manager**.

### Noções básicas

Clique em **Básico** e configure o seguinte:

- **Nome de usuário administrador** é uma conta de usuário com permissões de administrador de domínio e um membro da função de servidor fixa sysadmin do SQL Server em ambas as instâncias do SQL Server. Neste tutorial, use **DomainAdmin**. 

- **Senha** é a senha da conta de administrador de domínio. Use uma senha complexa. Confirme a senha.

- **Assinatura** é a assinatura que será cobrada pelo Azure para executar todos os recursos implantados no grupo de disponibilidade AlwaysOn. Se sua conta tiver várias assinaturas, você pode especificar uma assinatura diferente.
 
- **Grupo de recursos** é o nome do grupo a que pertencem todos os recursos do Azure criados por este tutorial. Neste tutorial, use **SQL-HA-RG**. Para saber mais, confira (Visão geral do Gerenciador de Recursos do Azure) [resource-group-overview.md/#resource-groups].

- **Local** é a região do Azure onde os recursos para este tutorial serão criados. Selecione uma região do Azure para hospedar a infraestrutura.

A folha **Básico** terá a seguinte aparência:

![Noções básicas](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/1-basics.png)

- Clique em **OK**. 

### Configurações de rede e de domínio

Esse modelo da galeria do Azure cria um novo domínio com novos controladores de domínio. Ele também cria uma nova rede e duas sub-redes. O modelo não permite a criação de servidores em um domínio ou rede virtual existente. A próxima etapa é definir as configurações de rede e de domínio.

Na folha **Configurações de rede e domínio**, revise os valores predefinidos para as configurações de rede e domínio:

- **Nome de domínio raiz de floresta** é o nome de domínio que será usado no domínio do AD que hospedará o cluster. Neste tutorial, use **contoso.com**. 

- **Nome da Rede Virtual** é o nome de rede da rede virtual do Azure. Neste tutorial, use **autohaVNET**.

- **Nome da sub-rede do controlador de domínio** é o nome da parte da rede virtual que hospeda o controlador de domínio. Neste tutorial, use **subnet-1**. A sub-rede usará o prefixo de endereço **10.0.0.0/24**.

- **Nome da sub-rede do SQL Server** é o nome da parte da rede virtual que hospeda os servidores SQL e a testemunha do compartilhamento de arquivos. Neste tutorial, use **subnet-2**. A sub-rede usará o prefixo de endereço **10.0.1.0/26**.

Para saber mais sobre redes virtuais no [Azure, confira Visão geral da rede virtual](../virtual-network/virtual-networks-overview.md).

As **Configurações de rede e domínio** devem ter esta aparência:

![Configurações de rede e de domínio](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/2-domain.png)
 
Se necessário, você pode alterar esses valores. Neste tutorial, usamos os valores predefinidos.
 
- Examine as configurações e clique em **OK**. 

###configurações de grupo de disponibilidade

Em **Configurações do grupo de disponibilidade**, revise os valores predefinidos para o grupo de disponibilidade e o ouvinte.

- **Nome do grupo de disponibilidade** é o nome do recurso clusterizado para o grupo de disponibilidade. Neste tutorial, use **Contoso-ag**. 

- **Nome do ouvinte do grupo de disponibilidade** é usado pelo cluster e pelo balanceador de carga interno. Clientes que se conectam ao SQL Server podem usar esse nome para se conectar à réplica do banco de dados apropriada. Neste tutorial, use **Contoso-listener**.

-  **Porta de escuta do grupo de disponibilidade** especifica a porta TCP que o SQL Server usará. Neste tutorial, use a porta padrão **1433**.

Se necessário, você pode alterar esses valores. Neste tutorial, use os valores predefinidos.

![configurações de grupo de disponibilidade](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/3-availabilitygroup.png)

- Clique em **OK**. 

###Tamanho da VM, configurações de armazenamento

Em **Tamanho da VM, configurações de armazenamento**, escolha um tamanho de máquina virtual do SQL Server e examine as outras configurações.

- **Tamanho da máquina virtual do SQL Server** é o tamanho da máquina virtual do Azure para ambos os SQL Servers. Escolha um tamanho de máquina virtual apropriado para sua carga de trabalho. Se você estiver criando esse ambiente para o tutorial, use **DS2**. Para cargas de trabalho de produção, escolha um tamanho de máquina virtual que dê suporte à carga de trabalho. Muitas cargas de trabalho de produção exigirão **DS4** ou maior. O modelo criará duas máquinas virtuais desse tamanho e instalará o SQL Server em cada uma delas. Para saber mais, confira [Sizes for virtual machines](virtual-machines-linux-sizes.md).

>[AZURE.NOTE]O Azure instalará a Enterprise Edition do SQL Server. O custo depende da edição e do tamanho da máquina virtual. Para obter informações detalhadas sobre os custos atuais, confira [Preço das máquinas virtuais](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

- **Tamanho de máquina virtual do controlador de domínio** é o tamanho da máquina virtual para os controladores de domínio. Neste tutorial, use **D2**.

- **Tamanho da máquina virtual da testemunha de compartilhamento de arquivos** é o tamanho da máquina virtual para a testemunha de compartilhamento de arquivos. Neste tutorial, use **A1**.

- **Conta de armazenamento SQL** é o nome da conta de armazenamento para manter os dados do SQL Server e os discos do sistema operacional. Neste tutorial, use **alwaysonsql01**.

- **Conta de armazenamento do controlador de domínio** é o nome da conta de armazenamento para os controladores de domínio. Neste tutorial, use **alwaysondc01**.

- **Tamanho do disco de dados do SQL Server** em TB é o tamanho do disco de dados do SQL Server em TB. Especifique um número de 1 a 4. Esse é o tamanho do disco de dados que será anexado a cada SQL Server. Neste tutorial, use **1**.

- **Otimização de armazenamento** define as configurações de armazenamento específicas para as máquinas virtuais do SQL Server com base no tipo de carga de trabalho. Todos os SQL Servers neste cenário usam Armazenamento Premium com o cache de host de disco do Azure definido como somente leitura. Além disso, você pode otimizar as configurações do SQL Server para a carga de trabalho escolhendo uma destas três configurações:

    - **Carga de trabalho geral** não define nenhuma configuração específica 

    - **Processamento transacional** define sinalizadores de rastreamento 1117 e 1118

    - O **data warehouse** define sinalizadores de rastreamento 1117 e 610

Neste tutorial, use **Carga de trabalho geral**.

![Configurações de armazenamento de tamanho da VM](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/4-vm.png)

- Examine as configurações e clique em **OK**. 

####Uma observação sobre armazenamento

As otimizações adicionais dependem do tamanho dos discos de dados do SQL Server. Para cada terabyte de disco de dados, o Azure adiciona mais 1 TB de Armazenamento Premium (SSD). Quando um servidor requer 2 TB ou mais, o modelo cria um pool de armazenamento em cada servidor SQL. Um pool de armazenamento é uma forma de virtualização de armazenamento em que vários discos estão configurados para fornecer maior capacidade, flexibilidade e desempenho. O modelo cria um espaço de armazenamento no pool de armazenamento e apresenta isso como dados únicos para o sistema operacional. O modelo designa o disco como o disco de dados do SQL Server. O modelo ajusta o pool de armazenamento para o SQL Server com as seguintes configurações:

- Tamanho de faixa é a configuração de intercalação do disco virtual. Para cargas de trabalho transacionais, isso é definido como 64 KB. Para cargas de trabalho de armazenamento de dados, a configuração é 256 KB. 

- A resiliência é simples (sem resiliência).

>[AZURE.NOTE] O armazenamento Premium do Azure é redundante localmente e mantém três cópias dos dados em uma única região; portanto, não é necessário ter resiliência adicional no pool de armazenamento.

- A contagem de colunas é igual ao número de discos no pool de armazenamento. 

Para saber mais sobre o espaço de armazenamento e pools de armazenamento, confira:

- [Storage Spaces Overview](http://technet.microsoft.com/library/hh831739.aspx). 

- [Backup do Windows Server e pools de armazenamento](http://technet.microsoft.com/library/dn390929.aspx)

Para saber mais sobre práticas recomendadas de configuração do SQL Server, confira [Performance best practices for SQL Server in Azure virtual machines](virtual-machines-windows-classic-sql-perf.md)


###Configurações do SQL Server

Em **Configurações do SQL Server**, examine e modifique o prefixo do nome da VM do SQL Server, a versão do SQL Server, a conta de serviço e senha do SQL Server e a agenda de manutenção de aplicação de patch automática do SQL.

- **Prefixo do nome do SQL Server** é usado para criar um nome para cada SQL Server. Neste tutorial, use **Contoso-ag**. Os nomes do SQL Server serão *Contoso-ag-0* e *Contoso-ag-1*. 

- **Versão do SQL Server** é a versão do SQL Server. Neste tutorial, use **SQL Server 2014**. Você também pode escolher **SQL Server 2012** ou **SQL Server 2016**.

- **Nome de usuário da conta de serviço do SQL Server** é o nome da conta de domínio para o serviço SQL Server. Neste tutorial, use **sqlservice**.

- **Senha** é a senha para a conta de serviço do SQL Server. Use uma senha complexa. Confirme a senha.

- **Agenda de manutenção de aplicação de patch automática do SQL** identifica o dia da semana que o Azure aplicará patch automaticamente nos SQL Servers. Neste tutorial, digite **Domingo**.

- **Hora de início da manutenção de aplicação de patch automática do SQL** é a hora do dia da região do Azure em que a aplicação de patch automática será iniciada.

>[AZURE.NOTE]A janela de aplicação de patches para cada VM é alterada em uma hora. Apenas uma máquina virtual é corrigida por vez para evitar a interrupção dos serviços.

![Configurações do SQL Server](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/5-sql.png)

Examine as configurações e clique em **OK**.

###Resumo

Na página Resumo do Azure, valide as configurações. Você também pode baixar o modelo. Examine o resumo. Clique em **OK**.

###Comprar

Essa folha final contém os **Termos de uso** e a **Política de Privacidade**. Examine essas informações. Quando estiver pronto para começar a criar as máquinas virtuais do Azure e todos os outros recursos necessários para o grupo de disponibilidade AlwaysOn, clique em **Criar**.
 
O portal do Azure criará o grupo de recursos e todos os recursos.

##Monitorar implantação

Monitore o progresso da implantação no portal do Azure. Um ícone que representa a implantação está fixado automaticamente ao painel do portal do Azure.

![Painel do Azure](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/11-deploydashboard.png)

##Conectar-se ao SQL Server

As novas instâncias do SQL Server são executadas em máquinas virtuais que não têm conexões com a Internet. No entanto, os controladores de domínio têm uma conexão com a Internet. Para se conectar aos SQL Servers usando a área de trabalho remota, primeiro use o protocolo RDP para um dos controladores de domínio. No controlador de domínio, abra um segundo RDP para o SQL Server.

Para usar protocolo RDP com o controlador de domínio primário, siga estas etapas:

1.	No painel do portal do Azure, verifique se a implantação foi bem-sucedida. 

1.	Clique em **Recursos**.

1.	Na folha **Recursos**, clique em **ad-primary-dc**, que é o nome de computador da máquina virtual do controlador de domínio primário.

1.	Na folha **ad-primary-dc**, clique em **Conectar**. Seu navegador perguntará se você deseja abrir ou salvar o objeto de conexão remota. Clique em **Abrir**. ![Conectar-se ao controlador de domínio](./media/virtual-machines-sql-server-alwayson-availability-groups-gui-arm/13-ad-primary-dc-connect.png)
1.	**Conexão de área de trabalho remota** pode avisar você de que o publicador dessa conexão remota não pode ser identificado. Clique em **Conectar**.

1.	A segurança do Windows solicita que você insira suas credenciais para se conectar ao endereço IP do controlador de domínio primário. Clique em **Usar outra conta**. Em **Nome de usuário**, digite **contoso\\DomainAdmin**. Essa é a conta que você escolheu para o nome de usuário de administrador. Use a senha complexa que você escolheu quando configurou o modelo.

1.	**Área de trabalho remota** pode avisar você que o computador remoto não pôde ser autenticado devido a problemas com o certificado de segurança. Ele mostra o nome do certificado de segurança. Se você seguiu o tutorial, o nome será **ad-primary-dc.contoso.com**. Clique em **Sim**.

Agora você está conectado ao controlador de domínio primário. Para usar o protocolo RDP no SQL Server, siga estas etapas:

1.	No controlador de domínio, abra **Conexão de Área de Trabalho Remota**. 

1.	Em **Computador**, digite o nome de um dos SQL Servers. Neste tutorial, digite **sqlserver-0**.

1.	Use a mesma conta de usuário e senha que você usou no protocolo RDP para o controlador de domínio.

Agora você está conectado ao SQL Server com protocolo RDP. Você pode abrir o SQL Server Management Studio, conectar-se à instância padrão do SQL Server e verificar se o grupo de disponibilidade AlwaysOn está configurado.

<!-----------HONumber=AcomDC_0330_2016-->