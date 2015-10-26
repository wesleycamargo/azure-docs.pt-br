<properties 
   pageTitle="Conectar-se a uma Máquina Virtual do SQL Server no Azure | Microsoft Azure"
   description="Este tópico usa recursos criados com o modelo de implantação clássica e descreve como se conectar ao SQL Server em execução em uma Máquina Virtual no Azure. Os cenários diferem dependendo da configuração da rede e do local do cliente."
   services="virtual-machines"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar"    
   tags="azure-service-management"/>
<tags 
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="08/18/2015"
   ms.author="jroth" />

# Conectar-se a uma máquina virtual do SQL Server no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.
 
 
## Visão geral

Configurar a conectividade com o SQL Server em execução em uma máquina virtual do Azure não difere significativamente das etapas necessárias para uma instância do SQL Server local. Você ainda precisa trabalhar com etapas de configuração que envolvem o firewall, a autenticação e os logons do banco de dados.

Mas há alguns aspectos da conectividade com o SQL Server que são específicos para máquinas virtuais do Azure. Este artigo aborda alguns [cenários gerais de conectividade](#connection-scenarios) e descreve [etapas detalhadas para configurar a conectividade com o SQL Server em uma VM do Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE]Este artigo se concentra na conectividade. Para ver uma apresentação completa sobre provisionamento e conectividade, consulte [Provisionando uma Máquina Virtual do SQL Server no Azure](virtual-machines-provision-sql-server.md).

## Cenários de conexão

A maneira como um cliente se conecta ao SQL Server em execução em uma máquina virtual varia dependendo do local do cliente e da configuração da máquina/rede. Esses cenários incluem:

- [Conectar-se ao SQL Server no mesmo serviço de nuvem](#connect-to-sql-server-in-the-same-cloud-service)
- [Conectar-se ao SQL Server pela Internet](#connect-to-sql-server-over-the-internet)
- [Conectar-se ao SQL Server na mesma rede virtual](#connect-to-sql-server-in-the-same-virtual-network)

### Conectar-se ao SQL Server no mesmo serviço de nuvem

Várias máquinas virtuais podem ser criadas no mesmo serviço de nuvem. Para compreender este cenário com máquinas virtuais, consulte [Como conectar máquinas virtuais a uma rede virtual ou serviço de nuvem](cloud-services-connect-virtual-machine.md).

Primeiro, execute as [etapas neste artigo para configurar a conectividade](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). Observe que você não precisa configurar um ponto de extremidade público se for estabelecer uma conexão entre máquinas que estão no mesmo serviço de nuvem.

Você pode usar o **nome de host** da VM na cadeia de conexão do cliente. O nome de host é o nome que você deu à VM durante a criação. Por exemplo, se a VM do SQL tiver o nome **mysqlvm**, com um nome DNS do serviço de nuvem **mycloudservice.cloudapp.net**, uma VM do cliente no mesmo serviço de nuvem poderia usar a seguinte cadeia de conexão para se conectar:

	"Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### Conectar-se ao SQL Server pela Internet

Se quiser se conectar ao seu mecanismo de banco de dados do SQL Server pela Internet, você deve criar um ponto de extremidade de máquina virtual para a comunicação TCP de entrada. Essa etapa de configuração do Azure, direciona o tráfego da porta TCP de entrada para uma porta TCP que está acessível para a máquina virtual.

Primeiro, execute as [etapas neste artigo para configurar a conectividade](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). Qualquer cliente com acesso à Internet poderia, então, se conectar à instância do SQL Server especificando o nome DNS do serviço de nuvem (como **mycloudservice.cloudapp.net**) e o ponto de extremidade da VM (como **57500**).

	"Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Embora isso habilite a conectividade para clientes pela Internet, não significa que qualquer pessoa possa se conectar ao seu SQL Server. Clientes externos precisam ter o nome de usuário e a senha corretos. Para aumentar a segurança, não use a conhecida porta 1433 para o ponto de extremidade de máquina virtual público. Se possível, considere a ideia de adicionar uma ACL ao ponto de extremidade para restringir o tráfego apenas aos clientes permitidos. Para obter instruções sobre como usar ACLs com pontos de extremidade, consulte [Gerenciar a ACL em um ponto de extremidade](virtual-machines-set-up-endpoints.md#manage-the-acl-on-an-endpoint).

>[AZURE.NOTE]É importante observar que, quando você usa essa técnica para se comunicar com o SQL Server, todos os dados retornados são considerados tráfego de saída do datacenter. Eles estão sujeitos aos [preços normais por transferências de dados de saída](http://azure.microsoft.com/pricing/details/data-transfers). Isso vale mesmo se você usar essa técnica de outra máquina ou serviço de nuvem no mesmo datacenter do Azure, porque o tráfego ainda passa pelo balanceador de carga público do Azure.

### Conectar-se ao SQL Server na mesma rede virtual

A [Rede Virtual](..\virtual-network\virtual-networks-overview.md) permite cenários adicionais. Você pode conectar VMS na mesma rede virtual, ainda que essas VMs existam em serviços de nuvem diferentes. E com um [VPN site a site](../vpn-gateway/vpn-gateway-site-to-site-create.md), você pode criar uma arquitetura híbrida que conecta as VMs a computadores e redes locais.

Redes virtuais também permitem que você adicione suas VMs do Azure a um domínio. Essa é a única maneira de usar a autenticação do Windows para o SQL Server. Outros cenários de conexão requerem autenticação SQL com nomes de usuário e senhas.

Primeiro, execute as [etapas neste artigo para configurar a conectividade](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). Se você for configurar um ambiente de domínio e a autenticação do Windows, não é necessário seguir as etapas neste artigo para configurar a autenticação e os logons do SQL. Além disso, um ponto de extremidade público não é necessário neste cenário.

Supondo que tenha configurado o DNS, você pode se conectar à instância do SQL Server especificando o nome de host da VM do SQL Server na cadeia de conexão. O exemplo a seguir pressupõe que a autenticação do Windows também foi configurada e que o usuário recebeu acesso à instância do SQL Server.

	"Server=mysqlvm;Integrated Security=true" 

Observe que, neste cenário, você pode também especificar o endereço IP da VM.

## Etapas para configurar a conectividade com o SQL Server em uma VM do Azure

[AZURE.INCLUDE [Conectar-se ao SQL Server em uma VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

## Próximas etapas

Para ver instruções de provisionamento com estas etapas de conectividade, consulte [Provisionando uma Máquina Virtual do SQL Server no Azure](virtual-machines-provision-sql-server.md).

Se pretende usar os Grupos de Disponibilidade AlwaysOn para alta disponibilidade e recuperação de desastres, você deve considerar implementar um ouvinte. Clientes do banco de dados se conectam ao ouvinte e não diretamente a uma das instâncias do SQL Server. O ouvinte direciona os clientes para a réplica primária do grupo de disponibilidade. Para obter mais informações, consulte [Configurar um ouvinte de ILB para Grupos de Disponibilidade AlwaysOn no Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

É importante reler todas as práticas recomendadas de segurança para o SQL Server em execução em uma máquina virtual do Azure. Para obter mais informações, consulte [Considerações sobre Segurança para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-security-considerations.md).

Para outros tópicos relacionados à execução do SQL Server em VMs do Azure, consulte [SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=Oct15_HO3-->