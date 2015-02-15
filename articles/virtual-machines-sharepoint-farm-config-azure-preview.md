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
	ms.date="1/26/2015" 
	ms.author="josephd"/>


#Detalhes da configuração do Farm do SharePoint Server#

Farm do SharePoint Server é um recurso do Portal de Visualização do Microsoft Azure que cria automaticamente um farm do SharePoint Server 2013 pré-configurado para você. Existem duas configurações de farm:

- Basic
- Alta disponibilidade

As seções a seguir fornecem detalhes da configuração de cada farm.

Para obter informações adicionais, consulte [Farm do SharePoint Server](../virtual-machines-sharepoint-farm-azure-preview/).

##Farm do SharePoint básico##

O farm do SharePoint básico consiste em três máquinas virtuais nesta configuração:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png) 

Aqui estão os detalhes da configuração:

-	Assinatura do Azure: Especificada durante a configuração inicial.
-	Nomes de Domínio do Azure (também conhecidos como serviços de nuvem): Nomes de Domínio do Azure são criados automaticamente para cada máquina virtual.
-	Conta de armazenamento: Especificada durante a configuração inicial.
-	Rede virtual 	
	-   Tipo: Somente nuvem	
    -	Espaço de endereço: 192.168.16.0/26    

- Máquinas virtuais
	-	*HostNamePrefix*-DC (controlador de domínio do AD DS)
	-	*HostNamePrefix*- SQL (servidor do SQL Server 2014)
	-	*HostNamePrefix*-SP (servidor do SharePoint 2013)

- Controlador de domínio
	-	Prefixo do nome de host: Especificada durante a configuração inicial.
	-	Tamanho: A1 (padrão)
	-	Nome de domínio: contoso.com (padrão)
	-	Nome da conta do administrador de domínio: Especificada durante a configuração inicial.
	-	Senha da conta do administrador de domínio: Especificada durante a configuração inicial.

- SQL Server
	-	Prefixo do nome de host: Especificada durante a configuração inicial.
	-	Tamanho: A5 (padrão)
	-	Nome da conta de acesso do banco de dados: Especificada durante a configuração inicial.
	-	Senha da conta de acesso do banco de dados: Especificada durante a configuração inicial.
	-	Nome da conta de serviço do SQL Server: Especificada durante a configuração inicial.
	-	Senha da conta de serviço do SQL Server: Especificada durante a configuração inicial.

- SharePoint Server
	-	Prefixo do nome de host: Especificada durante a configuração inicial.
	-	Tamanho: A2 (padrão)
	-	Nome da conta do farm do SharePoint: Especificada durante a configuração inicial.
	-	Senha da conta do farm do SharePoint: Especificada durante a configuração inicial.
	-	Senha do farm do SharePoint: Especificada durante a configuração inicial.


##Farm do SharePoint de alta disponibilidade##

O farm do SharePoint de alta disponibilidade consiste em nove máquinas virtuais nesta configuração:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)
 
Aqui estão os detalhes da configuração:

-	Assinatura do Azure: Especificada durante a configuração inicial.
-	Nomes de Domínio do Azure (também conhecidos como serviços de nuvem): Nomes de domínio separados são criados de acordo com a figura acima.
-	Conta de armazenamento: Especificada durante a configuração inicial.
-	Rede virtual	
	-	Tipo: Somente nuvem
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
	-	Prefixo do nome de host: Especificada durante a configuração inicial.
	-	Tamanho: A1 (padrão)
	-	Nome de domínio: contoso.com (padrão)
	-	Nome da conta do administrador de domínio: Especificada durante a configuração inicial.
	-	Senha da conta do administrador de domínio: Especificada durante a configuração inicial.

-	Servidores SQL
	-	Prefixo do nome de host: Especificada durante a configuração inicial.
	-	Tamanho: A5 (padrão)
	-	Nome da conta de acesso do banco de dados: Especificada durante a configuração inicial.
	-	Senha da conta de acesso do banco de dados: Especificada durante a configuração inicial.
	-	Nome da conta de serviço do SQL Server: Especificada durante a configuração inicial.
	-	Senha da conta de serviço do SQL Server: Especificada durante a configuração inicial.

-	Servidores SharePoint
	-	Prefixo do nome de host: Especificada durante a configuração inicial.
	-	Tamanho: A2 (padrão)
	-	Nome da conta do farm do SharePoint: Especificada durante a configuração inicial.
	-	Senha da conta do farm do SharePoint: Especificada durante a configuração inicial.		
	-	Senha do farm do SharePoint: Especificada durante a configuração inicial.

##Recursos adicionais##

[Farm do SharePoint Server](../virtual-machines-sharepoint-farm-azure-preview/)

[SharePoint nos serviços de infraestrutura do Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

<!--HONumber=42-->
