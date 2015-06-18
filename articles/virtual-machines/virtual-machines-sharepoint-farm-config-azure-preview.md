<properties 
	pageTitle="Detalhes da configuração do Farm do SharePoint Server" 
	description="Descreve a configuração padrão dos farms do SharePoint" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-sharepoint" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="josephd"/>


# Detalhes da configuração do Farm do SharePoint Server

Farm do SharePoint Server é um recurso do Portal de Visualização do Microsoft Azure que cria automaticamente um farm do SharePoint Server 2013 pré-configurado para você. Existem duas configurações de farm:

- Basic
- Alta disponibilidade

As seções a seguir fornecem detalhes da configuração de cada farm.

Para obter informações adicionais, consulte [Farm do SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md).

## Farm do SharePoint básico

O farm do SharePoint básico consiste em três máquinas virtuais nesta configuração:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png)

Aqui estão os detalhes da configuração:

-	Assinatura do Azure: especificada durante a configuração inicial.
-	Nomes de Domínio do Azure (também conhecidos como serviços de nuvem): nomes de domínio separados são criados automaticamente para cada máquina virtual.
-	Conta de armazenamento: especificada durante a configuração inicial.
-	Rede virtual 	
	-   Tipo: somente nuvem	
    -	Espaço de endereço: 192.168.16.0/26    

- Máquinas virtuais
	-	*HostNamePrefix*-DC (controlador de domínio do AD DS)
	-	*HostNamePrefix*-SQL (servidor do SQL Server 2014)
	-	*HostNamePrefix*-SP (servidor do SharePoint 2013)

- Controlador de domínio
	-	Imagem da máquina virtual: Windows Server 2012 R2.
	-	Prefixo do nome do host: especificado durante a configuração inicial.
	-	Tamanho: A1 (padrão)
	-	Nome de domínio: contoso.com (padrão)
	-	Nome da conta do administrador de domínio: especificado durante a configuração inicial.
	-	Senha da conta do administrador de domínio: especificada durante a configuração inicial.

- SQL Server
	-	Imagem da máquina virtual: SQL Server 2014 RTM Enterprise no Windows Server 2012 R2.
	-	Prefixo do nome do host: especificado durante a configuração inicial.
	-	Tamanho: A5 (padrão)
	-	Nome da conta de acesso do banco de dados: especificado durante a configuração inicial.
	-	Senha da conta de acesso do banco de dados: especificada durante a configuração inicial.
	-	Nome da conta de serviço do SQL Server: especificado durante a configuração inicial.
	-	Senha da conta de serviço do SQL Server: especificada durante a configuração inicial.

- SharePoint Server
	-	Imagem da máquina virtual: versão de avaliação do SharePoint Server 2013.
	-	Prefixo do nome do host: especificado durante a configuração inicial.
	-	Tamanho: A2 (padrão)
	-	Nome da conta do farm do SharePoint: especificado durante a configuração inicial.
	-	Senha da conta do farm do SharePoint: especificada durante a configuração inicial.
	-	Senha do farm do SharePoint: especificada durante a configuração inicial.


## Farm do SharePoint de alta disponibilidade

O farm do SharePoint de alta disponibilidade consiste em nove máquinas virtuais nesta configuração:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)
 
Aqui estão os detalhes da configuração:

-	Assinatura do Azure: especificada durante a configuração inicial.
-	Nomes de Domínio do Azure (também conhecidos como serviços de nuvem): nomes de domínio separados são criados de acordo com a figura a seguir.
-	Conta de armazenamento: especificada durante a configuração inicial.
-	Rede virtual	
	-	Tipo: somente nuvem
	-	Espaço de endereço: 192.168.16.0/26	

-	Máquinas virtuais
	-	*HostNamePrefix*-DC1 (controlador de domínio do AD DS)
	-	*HostNamePrefix*-DC2 (controlador de domínio do AD DS)
	-	*HostNamePrefix*-SQL1 (servidor do SQL Server 2014)
	-	*HostNamePrefix*-SQL2 (servidor do SQL Server 2014)
	-	*HostNamePrefix*-SQL0 (servidor do Windows Server 2012 R2)
	-	*HostNamePrefix*-WEB1 (servidor do SharePoint 2013)
	-	*HostNamePrefix*-WEB2 (servidor do SharePoint 2013)
	-	*HostNamePrefix*-APP1 (servidor do SharePoint 2013)
	-	*HostNamePrefix*-APP2 (servidor do SharePoint 2013)

-	Controladores de domínio
	-	Imagem da máquina virtual: Windows Server 2012 R2.
	-	Prefixo do nome do host: especificado durante a configuração inicial.
	-	Tamanho: A1 (padrão)
	-	Nome de domínio: contoso.com (padrão)
	-	Nome da conta do administrador de domínio: especificado durante a configuração inicial.
	-	Senha da conta do administrador de domínio: especificada durante a configuração inicial.

-	Servidores SQL
	-	Imagem da máquina virtual: SQL Server 2014 RTM Enterprise no Windows Server 2012 R2.
	-	Prefixo do nome do host: especificado durante a configuração inicial.
	-	Tamanho: A5 (padrão)
	-	Nome da conta de acesso do banco de dados: especificado durante a configuração inicial.
	-	Senha da conta de acesso do banco de dados: especificada durante a configuração inicial.
	-	Nome da conta de serviço do SQL Server: especificado durante a configuração inicial.
	-	Senha da conta de serviço do SQL Server: especificada durante a configuração inicial.

-	Servidores SharePoint
	-	Imagem da máquina virtual: versão de avaliação do SharePoint Server 2013.
	-	Prefixo do nome do host: especificado durante a configuração inicial.
	-	Tamanho: A2 (padrão)
	-	Nome da conta do farm do SharePoint: especificado durante a configuração inicial.
	-	Senha da conta do farm do SharePoint: especificada durante a configuração inicial.		
	-	Senha do farm do SharePoint: especificada durante a configuração inicial.

> [AZURE.NOTE]Os servidores do SharePoint são criados a partir da imagem de trilha do SharePoint Server 2013. Para continuar usando a máquina virtual após a expiração da avaliação, é necessário converter a instalação para usar uma chave de licença de Volume ou de Varejo para as edições Standard ou Enterprise do SharePoint Server 2013.

## Recursos adicionais

[Farm do SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md)

[SharePoint nos serviços de infraestrutura do Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

<!---HONumber=58--> 