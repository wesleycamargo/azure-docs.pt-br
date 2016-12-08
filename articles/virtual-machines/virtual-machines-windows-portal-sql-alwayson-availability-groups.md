---
title: Configurar automaticamente o grupo de disponibilidade Always On na VM do Azure - Resource Manager
description: "Crie um grupo de disponibilidade AlwaysOn com as máquinas virtuais do Azure no modo do Azure Resource Manager. Este tutorial usa principalmente a interface do usuário para criar a solução inteira automaticamente."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: MikeRayMSFT
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: fc8dd1f618d36ba8586d5130c579c9d59b8393e5


---
# <a name="configure-always-on-availability-group-in-azure-vm-automatically---resource-manager"></a>Configurar automaticamente o grupo de disponibilidade Always On na VM do Azure - Resource Manager
> [!div class="op_single_selector"]
> * [Resource Manager: Modelo](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [Resource Manager: Manual](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [Clássico: Interface de usuário](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Clássico: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

<br/>

Este tutorial de ponta a ponta mostra como criar um grupo de disponibilidade do SQL Server com máquinas virtuais do Gerenciador de Recursos do Azure. O tutorial usa folhas do Azure para configurar um modelo. Você revisará as configurações padrão, digitará as configurações necessárias e atualizará as folhas enquanto percorre este tutorial.

Ao final do tutorial, sua solução de grupo de disponibilidade do SQL Server no Azure consistirá nos seguintes elementos:

* Uma rede virtual que contém várias sub-redes, incluindo uma de front-end e uma de back-end
* Dois controladores de domínio com um domínio do AD (Active Directory)
* Duas VMs do SQL Server implantadas na sub-rede de back-end e ingressadas no domínio do AD
* Um cluster WSFC de três nós com o modelo de quórum de Nó Principal
* Um grupo de disponibilidade com duas réplicas de confirmação síncrona de um banco de dados de disponibilidade

A figura abaixo é uma representação gráfica da solução.

![Arquitetura de laboratório de teste para o AG no Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Todos os recursos nesta solução pertencem a um único grupo de recursos.

Este tutorial pressupõe o seguinte:

* Você já tem uma conta do Azure. Se não tiver uma, [inscreva-se para uma conta de avaliação](http://azure.microsoft.com/pricing/free-trial/).
* Você já sabe como provisionar uma VM do SQL Server da galeria de máquinas virtuais usando a GUI. Para obter mais informações, consulte [Provisionando uma máquina virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Você já tem uma compreensão sólida dos grupos de disponibilidade. Para obter mais informações, confira [Grupos de disponibilidade Always On (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Se você estiver interessado em usar os grupos de disponibilidade com o SharePoint, confira também [Configure SQL Server 2012 Always On Availability Groups for SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx)(Configurar grupos de disponibilidade AlwaysOn do SQL Server 2012 para o SharePoint 2013).
> 
> 

Neste tutorial, você usará o portal do Azure para:

* Selecione o modelo AlwaysOn do portal
* Examinar as configurações de modelo e atualizar alguns parâmetros de configuração para o seu ambiente
* Monitorar o Azure enquanto ele cria todo o ambiente
* Conectar-se a um dos controladores de domínio e a um dos SQL Servers

[!INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Provisionar o cluster da galeria
O Azure fornece uma imagem da galeria para a solução inteira. Para localizar o modelo:

1. Faça logon no portal do Azure usando sua conta.
2. No Portal do Azure, clique em **+Novo.**  O portal abrirá a folha Novo.
3. Na folha Novo, pesquise por **AlwaysOn**.
   ![Localizar o modelo AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Nos resultados da pesquisa, localize **SQL Server AlwaysOn Cluster (Cluster do SQL Server AlwaysOn)**.
   ![Modelo AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. Em **Selecione um modelo de implantação**, escolha **Resource Manager**.

### <a name="basics"></a>Noções básicas
Clique em **Básico** e configure o seguinte:

* **Nome de usuário do administrador** é uma conta de usuário com permissões de administrador de domínio e um membro da função de servidor fixa sysadmin do SQL Server em ambas as instâncias do SQL Server. Neste tutorial, use **DomainAdmin**.
* **Senha** é a senha da conta de administrador de domínio. Use uma senha complexa. Confirme a senha.
* **Assinatura** é a assinatura que será cobrada pelo Azure para executar todos os recursos implantados para o grupo de disponibilidade. Se sua conta tiver várias assinaturas, você poderá especificar uma assinatura diferente.
* **Grupo de recursos** é o nome do grupo ao qual pertencem todos os recursos do Azure criados por este tutorial. Neste tutorial, use **SQL-HA-RG**. Para saber mais, confira (Visão geral do Gerenciador de Recursos do Azure) [resource-group-overview.md/#resource-groups].
* **Localização** é a região do Azure em que os recursos para este tutorial serão criados. Selecione uma região do Azure para hospedar a infraestrutura.

A folha **Básico** terá a seguinte aparência:

![Noções básicas](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

* Clique em **OK**.

### <a name="domain-and-network-settings"></a>Configurações de rede e de domínio
Esse modelo da galeria do Azure cria um novo domínio com novos controladores de domínio. Ele também cria uma nova rede e duas sub-redes. O modelo não permite a criação de servidores em um domínio ou rede virtual existente. A próxima etapa é definir as configurações de rede e de domínio.

Na folha **Domain and network settings (Configurações de rede e domínio)** , examine os valores predefinidos para as configurações de rede e domínio:

* **Nome de domínio raiz da floresta** é o nome de domínio que será usado no domínio do AD que hospedará o cluster. Neste tutorial, use **contoso.com**.
* **Nome da Rede Virtual** é o nome de rede da rede virtual do Azure. Neste tutorial, use **autohaVNET**.
* **Domain Controller subnet name (Nome da sub-rede do Controlador de Domínio)** é o nome de uma parte da rede virtual que hospeda o controlador de domínio. Neste tutorial, use **subnet-1**. A sub-rede usará o prefixo de endereço **10.0.0.0/24**.
* **SQL Server subnet name (Nome da sub-rede do SQL Server)** é o nome da parte da rede virtual que hospeda os servidores SQL e a testemunha de compartilhamento de arquivos. Neste tutorial, use **subnet-2**. A sub-rede usará o prefixo de endereço **10.0.1.0/26**.

Para saber mais sobre as redes virtuais no [Azure, confira Visão geral da Rede Virtual](../virtual-network/virtual-networks-overview.md).  

As **Configurações de domínio e rede** devem ter esta aparência:

![Configurações de rede e de domínio](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Se necessário, você pode alterar esses valores. Neste tutorial, usamos os valores predefinidos.

* Examine as configurações e clique em **OK**.

### <a name="availability-group-settings"></a>configurações de grupo de disponibilidade
Nas **configurações do grupo de disponibilidade** , examine os valores predefinidos para o grupo de disponibilidade e o ouvinte.

* **Availablity group name (Nome do grupo de disponibilidade) (Nome do grupo de disponibilidade)** é o nome do recurso clusterizado para o grupo de disponibilidade. Neste tutorial, use **Contoso-ag**.
* **Nome do ouvinte do grupo de disponibilidade** é usado pelo cluster e pelo balanceador de carga interno. Clientes que se conectam ao SQL Server podem usar esse nome para se conectar à réplica do banco de dados apropriada. Neste tutorial, use **Contoso-listener**.
* **Porta do ouvinte do grupo de disponibilidade** especifica a porta TCP que o ouvinte do SQL Server usará. Neste tutorial, use a porta padrão **1433**.

Se necessário, você pode alterar esses valores. Neste tutorial, use os valores predefinidos.  

![configurações de grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

* Clique em **OK**.

### <a name="vm-size-storage-settings"></a>Tamanho da VM, configurações de armazenamento
Em **VM size, storage settings (Tamanho da VM, configurações de armazenamento)** , escolha um tamanho da máquina virtual do SQL Server e examine as outras configurações.

* **SQL Server virtual machine size (Tamanho da máquina virtual do SQL Server)** é o tamanho da máquina virtual do Azure para ambos os servidores SQL. Escolha um tamanho de máquina virtual apropriado para sua carga de trabalho. Se você estiver criando esse ambiente para o tutorial, use **DS2**. Para cargas de trabalho de produção, escolha um tamanho de máquina virtual que dê suporte à carga de trabalho. Muitas cargas de trabalho de produção exigirão **DS4** ou maior. O modelo criará duas máquinas virtuais desse tamanho e instalará o SQL Server em cada uma delas. Para obter mais informações, confira [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> O Azure instalará a Enterprise Edition do SQL Server. O custo depende da edição e do tamanho da máquina virtual. Para obter informações detalhadas sobre os custos atuais, confira [Máquinas virtuais - Preço](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
> 
> 

* **Domain controller virtual machine size (Tamanho de máquina virtual do controlador de domínio)** é o tamanho da máquina virtual para os controladores de domínio. Neste tutorial, use **D2**.
* **File Share Witness virtual machine size (Tamanho da máquina virtual da testemunha de compartilhamento de arquivos)** é o tamanho da máquina virtual para a testemunha de compartilhamento de arquivos. Neste tutorial, use **A1**.
* **SQL Storage account (Conta de armazenamento SQL)** é o nome da conta de armazenamento para manter os dados do SQL Server e os discos do sistema operacional. Neste tutorial, use **alwaysonsql01**.
* **DC Storage account (Conta de armazenamento do controlador de domínio)** é o nome da conta de armazenamento para os controladores de domínio. Neste tutorial, use **alwaysondc01**.
* **SQL Server data disk size (Tamanho do disco de dados do SQL Server)** em TB é o tamanho do disco de dados do SQL Server em TB. Especifique um número de 1 a 4. Esse é o tamanho do disco de dados que será anexado a cada SQL Server. Neste tutorial, use **1**.
* **Storage optimization (Otimização de armazenamento)** define as configurações de armazenamento específicas para as máquinas virtuais do SQL Server com base no tipo de carga de trabalho. Todos os SQL Servers neste cenário usam Armazenamento Premium com o cache de host de disco do Azure definido como somente leitura. Além disso, você pode otimizar as configurações do SQL Server para a carga de trabalho escolhendo uma destas três configurações:
  
  * **General workload (Carga de trabalho geral)** não define nenhuma configuração específica
  * **Transactional processing (Processamento transacional)** define os sinalizadores de rastreamento 1117 e 1118
  * **Data warehousing (Armazenamento de dados)** define os sinalizadores de rastreamento 1117 e 610

Neste tutorial, use a **General workload (Carga de trabalho geral)**.

![Configurações de armazenamento de tamanho da VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

* Examine as configurações e clique em **OK**.

#### <a name="a-note-about-storage"></a>Uma observação sobre armazenamento
As otimizações adicionais dependem do tamanho dos discos de dados do SQL Server. Para cada terabyte de disco de dados, o Azure adiciona mais 1 TB de Armazenamento Premium (SSD). Quando um servidor requer 2 TB ou mais, o modelo cria um pool de armazenamento em cada servidor SQL. Um pool de armazenamento é uma forma de virtualização de armazenamento em que vários discos estão configurados para fornecer maior capacidade, flexibilidade e desempenho.  O modelo cria um espaço de armazenamento no pool de armazenamento e apresenta isso como dados únicos para o sistema operacional. O modelo designa o disco como o disco de dados do SQL Server. O modelo ajusta o pool de armazenamento para o SQL Server com as seguintes configurações:

* Tamanho de faixa é a configuração de intercalação do disco virtual. Para cargas de trabalho transacionais, isso é definido como 64 KB. Para cargas de trabalho de armazenamento de dados, a configuração é 256 KB.
* A resiliência é simples (sem resiliência).

> [!NOTE]
> O armazenamento Premium do Azure é redundante localmente e mantém três cópias dos dados em uma única região; portanto, não é necessário ter resiliência adicional no pool de armazenamento.
> 
> 

* A contagem de colunas é igual ao número de discos no pool de armazenamento.

Para saber mais sobre o espaço de armazenamento e pools de armazenamento, confira:

* [Visão geral de Espaços de Armazenamento](http://technet.microsoft.com/library/hh831739.aspx).
* [Backup do Windows Server e pools de armazenamento](http://technet.microsoft.com/library/dn390929.aspx)

Para obter mais informações sobre as práticas recomendadas de configuração do SQL Server, confira [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="sql-server-settings"></a>Configurações do SQL Server
Em **Configurações do SQL Server** , examine e modifique o prefixo do nome da VM do SQL Server, a versão do SQL Server, a conta de serviço e senha do SQL Server e a agenda de manutenção de aplicação de patch automática do SQL.

* **SQL Server Name Prefix (Prefixo do Nome do SQL Server)** é usado para criar um nome para cada SQL Server. Neste tutorial, use **Contoso-ag**. Os nomes do SQL Server serão *Contoso-ag-0* e *Contoso-ag-1*.
* **Versão do SQL Server** é a versão do SQL Server. Neste tutorial, use **SQL Server 2014**. Você também pode escolher **SQL Server 2012** ou **SQL Server 2016**.
* **SQL Server service account user name (Nome de usuário da conta de serviço do SQL Server)** é o nome da conta de domínio para o serviço do SQL Server. Neste tutorial, use **sqlservice**.
* **Senha** é a senha para a conta de serviço do SQL Server.  Use uma senha complexa. Confirme a senha.
* **SQL Auto Patching maintenance schedule (Agenda de manutenção de Aplicação de Patch Automática do SQL)** identifica o dia da semana em que o Azure aplicará o patch automaticamente nos servidores SQL. Neste tutorial, digite **Domingo**.
* **SQL Auto Patching maintenance start hour (Hora de início da manutenção de Aplicação de Patch Automática do SQL)** é a hora do dia da região do Azure em que a aplicação de patch automática será iniciada.

> [!NOTE]
> A janela de aplicação de patches para cada VM é alterada em uma hora. Apenas uma máquina virtual é corrigida por vez para evitar a interrupção dos serviços.
> 
> 

![Configurações do SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Examine as configurações e clique em **OK**.

### <a name="summary"></a>Resumo
Na página Resumo do Azure, valide as configurações. Você também pode baixar o modelo. Examine o resumo. Clique em **OK**.

### <a name="buy"></a>Comprar
Essa folha final contém os **Termos de uso** e a **Política de Privacidade**. Examine essas informações. Quando estiver pronto para começar a criar as máquinas virtuais do Azure e todos os outros recursos necessários para o grupo de disponibilidade, clique em **Criar**.

O portal do Azure criará o grupo de recursos e todos os recursos.

## <a name="monitor-deployment"></a>Monitorar implantação
Monitore o progresso da implantação no portal do Azure. Um ícone que representa a implantação está fixado automaticamente ao painel do portal do Azure.

![Painel do Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Conectar-se ao SQL Server
As novas instâncias do SQL Server são executadas em máquinas virtuais que não têm conexões com a Internet. No entanto, os controladores de domínio têm uma conexão com a Internet. Para se conectar aos SQL Servers usando a área de trabalho remota, primeiro use o protocolo RDP para um dos controladores de domínio. No controlador de domínio, abra um segundo RDP para o SQL Server.

Para usar protocolo RDP com o controlador de domínio primário, siga estas etapas:

1. No painel do portal do Azure, verifique se a implantação foi bem-sucedida.
2. Clique em **Recursos**.
3. Na folha **Recursos**, clique em **ad-primary-dc**, que é o nome do computador da máquina virtual do controlador de domínio primário.
4. Na folha de **ad-primary-dc** click **Conectar**. Seu navegador perguntará se você deseja abrir ou salvar o objeto de conexão remota. Clique em **Abrir**.
   ![Conectar-se ao controlador de domínio](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/13-ad-primary-dc-connect.png)
5. **Conexão de Área de Trabalho Remota** pode avisar você de que o distribuidor dessa conexão remota não pode ser identificado. Clique em **Conectar**.
6. A segurança do Windows solicita que você insira suas credenciais para se conectar ao endereço IP do controlador de domínio primário. Clique em **Usar outra conta**. Em **Nome de usuário**, digite **contoso\DomainAdmin**. Essa é a conta que você escolheu para o nome de usuário de administrador. Use a senha complexa que você escolheu quando configurou o modelo.
7. **Área de Trabalho Remota** pode avisar você de que o computador remoto não pôde ser autenticado devido a problemas com o certificado de segurança. Ele mostra o nome do certificado de segurança. Se você seguiu o tutorial, o nome será **ad-primary-dc.contoso.com**. Clique em **Sim**.

Agora você está conectado ao controlador de domínio primário. Para usar o protocolo RDP no SQL Server, siga estas etapas:

1. No controlador de domínio, abra a **Conexão de Área de Trabalho Remota**.
2. Em **Computador**, digite o nome de um dos servidores SQL. Neste tutorial, digite **sqlserver-0**.
3. Use a mesma conta de usuário e senha que você usou no protocolo RDP para o controlador de domínio.

Agora você está conectado ao SQL Server com protocolo RDP. Você pode abrir o SQL Server Management Studio, conectar-se à instância padrão do SQL Server e verificar se o grupo de disponibilidade está configurado.




<!--HONumber=Nov16_HO3-->


