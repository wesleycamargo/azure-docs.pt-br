---
title: "Conectar-se a uma Máquina Virtual do SQL Server (Resource Manager) | Microsoft Docs"
description: "Saiba como se conectar ao SQL Server em execução em uma Máquina Virtual no Azure. Este tópico usa o modelo de implantação clássica. Os cenários diferem dependendo da configuração da rede e do local do cliente."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/28/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 37b0abfa38282d7e8789ba5687f0937a60262464
ms.lasthandoff: 03/25/2017


---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>Conectar-se a uma Máquina Virtual do SQL Server no Azure (Gerenciador de Recursos)
> [!div class="op_single_selector"]
> * [Gerenciador de Recursos](virtual-machines-windows-sql-connect.md)
> * [Clássico](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Visão geral
Este tópico descreve como se conectar à instância do SQL Server em execução em uma máquina virtual do Azure. Ele aborda alguns [cenários gerais de conectividade](#connection-scenarios) e descreve [etapas detalhadas para configurar a conectividade com o SQL Server em uma VM do Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para exibir a versão clássica deste artigo, veja [Conectar-se a uma Máquina Virtual do SQL Server no Azure Clássico](../classic/sql-connect.md).

Para ver uma apresentação completa sobre provisionamento e conectividade, veja [Provisionando uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Cenários de conexão
A maneira como um cliente se conecta ao SQL Server em execução em uma máquina virtual varia dependendo do local do cliente e da configuração da máquina/rede. Esses cenários incluem:

* [Conectar-se ao SQL Server pela Internet](#connect-to-sql-server-over-the-internet)
* [Conectar-se ao SQL Server na mesma rede virtual](#connect-to-sql-server-in-the-same-virtual-network)

### <a name="connect-to-sql-server-over-the-internet"></a>Conectar-se ao SQL Server pela Internet
Se você quiser se conectar ao seu mecanismo de banco de dados do SQL Server da Internet, várias etapas deverão ser executadas, por exemplo, configurar o firewall, habilitar a autenticação do SQL e configurar o grupo de segurança de rede. É necessário ter uma regra do Grupo de Segurança de Rede para permitir o tráfego TCP na porta 1433.

Se você usar o portal para provisionar uma imagem de máquina virtual do SQL Server com o Resource Manager, essas etapas serão executadas ao selecionar **Pública** como a opção de conectividade do SQL:

![Opção de conectividade SQL pública durante o provisionamento](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Se isso não foi feito durante o provisionamento, é possível configurar manualmente o SQL Server e suas máquinas virtuais seguindo as [etapas nesse artigo para configurar a conectividade manualmente](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

> [!NOTE]
> A imagem de máquina virtual para o SQL Server Express edition não habilita automaticamente o protocolo TCP/IP. Para o Express Edition, você deve usar o SQL Server Configuration Manager para [habilitar manualmente o protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) depois de criar a máquina virtual.
> 
> 

Feito isso, qualquer cliente com acesso à Internet pode se conectar à instância do SQL Server, especificando o endereço IP público da máquina virtual ou o rótulo de DNS atribuído a esse endereço IP. Se a porta do SQL Server for 1433, você não precisará especificá-la na cadeia de conexão.

    "Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Embora isso habilite a conectividade para clientes pela Internet, não significa que qualquer pessoa possa se conectar ao seu SQL Server. Clientes externos precisam ter o nome de usuário e a senha corretos. Para obter segurança adicional, você pode evitar a porta 1433 conhecida. Por exemplo, se você configurou o SQL Server para escutar na porta 1500 e estabeleceu regras do grupo de segurança de rede e de firewall apropriadas, você pode se conectar acrescentando o número da porta ao nome do servidor, como no exemplo a seguir:

    "Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

> [!NOTE]
> É importante observar que, quando você usa essa técnica para se comunicar com o SQL Server, todos os dados de saída do data center do Azure estão sujeitos a [preços de transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/)normais.
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Conectar-se ao SQL Server na mesma rede virtual
[Rede Virtual](../../../virtual-network/virtual-networks-overview.md) permite cenários adicionais. Você pode conectar VMS na mesma rede virtual, ainda que essas VMs existam em grupos de recursos diferentes. E com um [VPN site a site](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), você pode criar uma arquitetura híbrida que conecta as VMs a computadores e redes locais.

Redes virtuais também permitem que você adicione suas VMs do Azure a um domínio. Essa é a única maneira de usar a autenticação do Windows para o SQL Server. Outros cenários de conexão requerem autenticação SQL com nomes de usuário e senhas.

Se você usar o portal para provisionar uma imagem de máquina virtual do SQL Server com o Resource Manager, as regras de firewall apropriadas para a comunicação na rede virtual serão configuradas ao selecionar **Privada** como a opção de conectividade do SQL. Se isso não foi feito durante o provisionamento, é possível configurar manualmente o SQL Server e suas máquinas virtuais seguindo as [etapas nesse artigo para configurar a conectividade manualmente](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Se você estiver planejando configurar um ambiente de domínio e Autenticação do Windows, não é necessário seguir as etapas nesse artigo para configurar a Autenticação e os logons do SQL. Você também não precisa configurar as regras do Grupo de Segurança de Rede para acesso pela Internet.

> [!NOTE]
> A imagem de máquina virtual para o SQL Server Express edition não habilita automaticamente o protocolo TCP/IP. Para o Express Edition, você deve usar o SQL Server Configuration Manager para [habilitar manualmente o protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) depois de criar a máquina virtual.
> 
> 

Supondo que tenha configurado o DNS na sua rede virtual, você pode se conectar à instância do SQL Server especificando o nome do computador da VM do SQL Server na cadeia de conexão. O exemplo a seguir também pressupõe que a autenticação do Windows foi configurada e que o usuário recebeu acesso à instância do SQL Server.

    "Server=mysqlvm;Integrated Security=true"

Observe que, neste cenário, você pode também especificar o endereço IP da VM.

## <a name="steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm"></a>Etapas para configurar manualmente a conectividade com o SQL Server em uma VM do Azure
As etapas a seguir demonstram como configurar manualmente a conectividade da instância do SQL Server e, então, de modo opcional, se conectar pela Internet usando o SSMS (SQL Server Management Studio). Observe que muitas dessas etapas são feitas por você quando você seleciona as opções apropriadas de conectividade do SQL Server no portal.

Para poder conectar-se à instância do SQL Server na Internet ou em outra VM, você deve concluir as seguintes tarefas, conforme descrito nas seções a seguir:

* [Abrir portas TCP no firewall do Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Configurar o SQL Server para escutar no protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Configurar o SQL Server para autenticação do modo misto](#configure-sql-server-for-mixed-mode-authentication)
* [Criar logons de autenticação do SQL Server](#create-sql-server-authentication-logins)
* [Configurar uma regra de entrada do Grupo de Segurança de Rede](#configure-a-network-security-group-inbound-rule-for-the-vm)
* [Configurar um rótulo de DNS para o endereço IP público](#configure-a-dns-label-for-the-public-ip-address)
* [Conectar-se ao Mecanismo de Banco de Dados de outro computador](#connect-to-the-database-engine-from-another-computer)

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Próximas etapas
Para ver instruções de provisionamento com estas etapas de conectividade, consulte [Provisionando uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

[Explorar o Roteiro de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) do SQL Server nas máquinas virtuais do Azure.

Para outros tópicos relacionados à execução do SQL Server em VMs do Azure, consulte [SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).


