<properties
	pageTitle="Configuração de Farm do SharePoint Server | Microsoft Azure"
	description="Saiba sobre a configuração padrão dos farms do SharePoint quando você usar o recurso Farm de Servidores do SharePoint do portal de visualização do Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2015"
	ms.author="josephd"/>


# Detalhes de configuração do Farm de Servidores do SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda a criação de recursos com o modelo clássico de implantação.

O Farm de Servidores do SharePoint é um recurso do portal de visualização do Azure que cria automaticamente um farm pré-configurado do SharePoint 2013 para você. Existem duas configurações de farm:

- Basic
- Alta disponibilidade

As seções a seguir fornecem detalhes da configuração de cada farm.

Para obter informações adicionais, consulte [Farm do SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md).

## Farm do SharePoint básico

O farm do SharePoint básico consiste em três máquinas virtuais nesta configuração:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png)

Aqui estão os detalhes da configuração:

-	Assinatura do Azure: especificada durante a configuração inicial.
-	Nomes de domínio do Azure (também conhecidos como serviços de nuvem): nomes de domínio separados são criados automaticamente para cada máquina virtual.
-	Conta de armazenamento: especificada durante a configuração inicial.
-	Rede virtual:
	-   Tipo: somente nuvem
    -	Espaço de endereço: 10.0.0.0/26

- Máquinas virtuais:
	-	*HostNamePrefix*-DC (controlador de domínio do AD DS)
	-	*HostNamePrefix*-SQL (servidor do SQL Server 2014)
	-	*HostNamePrefix*-SP (servidor do SharePoint 2013)

- Controlador de domínio:
	-	Imagem da máquina virtual: Windows Server 2012 R2.
	-	Prefixo do nome do host: especificado durante a configuração inicial.
	-	Tamanho: A1 (padrão).
	-	Nome de domínio: contoso.com (padrão)
	-	Nome da conta do administrador de domínio: especificado durante a configuração inicial.
	-	Senha da conta do administrador de domínio: especificada durante a configuração inicial.

- Servidor de banco de dados
	-	Imagem da máquina virtual: SQL Server 2014 RTM Enterprise no Windows Server 2012 R2.
	-	Prefixo do nome do host: especificado durante a configuração inicial.
	-	Tamanho: A5 (padrão).
	-	Nome da conta de acesso do banco de dados: especificado durante a configuração inicial.
	-	Senha da conta de acesso do banco de dados: especificada durante a configuração inicial.
	-	Nome da conta de serviço do SQL Server: sqlservice (padrão).
	-	Senha da conta de serviço do SQL Server: especificada durante a configuração inicial.

- SharePoint Server
	-	Imagem da máquina virtual: versão de avaliação do SharePoint Server 2013.
	-	Prefixo do nome do host: especificado durante a configuração inicial.
	-	Tamanho: A2 (padrão).
	-	Nome da conta do farm do SharePoint: sp\_farm (padrão).
	-	Senha da conta do farm do SharePoint: especificada durante a configuração inicial.
	-	Senha do farm do SharePoint: especificada durante a configuração inicial.


## Farm do SharePoint de alta disponibilidade

O farm do SharePoint de alta disponibilidade consiste em nove máquinas virtuais nesta configuração:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)

Aqui estão os detalhes da configuração:

-	Assinatura do Azure: especificada durante a configuração inicial.
-	Nomes de domínio do Azure (também conhecidos como serviços de nuvem): nomes de domínio separados são criados de acordo com a figura a seguir.
-	Conta de armazenamento: especificada durante a configuração inicial.
-	Rede virtual:
	-	Tipo: somente nuvem
	-	Espaço de endereço: 10.0.0.0/26

-	Máquinas virtuais:
	-	*HostNamePrefix*-DC1 (controlador de domínio do AD DS)
	-	*HostNamePrefix*-DC2 (controlador de domínio do AD DS)
	-	*HostNamePrefix*-SQL1 (servidor do SQL Server 2014)
	-	*HostNamePrefix*-SQL2 (servidor do SQL Server 2014)
	-	*HostNamePrefix*-SQL0 (servidor do Windows Server 2012 R2)
	-	*HostNamePrefix*-WEB1 (servidor do SharePoint 2013)
	-	*HostNamePrefix*-WEB2 (servidor do SharePoint 2013)
	-	*HostNamePrefix*-APP1 (servidor do SharePoint 2013)
	-	*HostNamePrefix*-APP2 (servidor do SharePoint 2013)

-	Controladores de domínio:
	-	Imagem da máquina virtual: Windows Server 2012 R2.
	-	Prefixo do nome do host: especificado durante a configuração inicial.
	-	Tamanho: A1 (padrão).
	-	Nome de domínio: contoso.com (padrão)
	-	Nome da conta do administrador de domínio: especificado durante a configuração inicial.
	-	Senha da conta do administrador de domínio: especificada durante a configuração inicial.

-	Servidores de banco de dados:
	-	Imagem da máquina virtual: SQL Server 2014 RTM Enterprise no Windows Server 2012 R2.
	-	Prefixo do nome do host: especificado durante a configuração inicial.
	-	Tamanho: A5 (padrão) para servidores de banco de dados, A0 (padrão) para a testemunha de compartilhamento de arquivos.
	-	Nome da conta de acesso do banco de dados: especificado durante a configuração inicial.
	-	Senha da conta de acesso do banco de dados: especificada durante a configuração inicial.
	-	Nome da conta de serviço do SQL Server: sqlservice (padrão).
	-	Senha da conta de serviço do SQL Server: especificada durante a configuração inicial.

-	Servidores do SharePoint:
	-	Imagem da máquina virtual: versão de avaliação do SharePoint Server 2013.
	-	Prefixo do nome do host: especificado durante a configuração inicial.
	-	Tamanho: A2 (padrão).
	-	Nome da conta do farm do SharePoint: sp\_farm (padrão).
	-	Senha da conta do farm do SharePoint: especificada durante a configuração inicial.
	-	Senha do farm do SharePoint: especificada durante a configuração inicial.

> [AZURE.NOTE]Os servidores do SharePoint são criados da imagem de avaliação do SharePoint Server 2013. Para continuar usando a máquina virtual após a expiração da avaliação, é necessário converter a instalação para usar uma chave de licença de Volume ou de Varejo para as edições Standard ou Enterprise do SharePoint Server 2013.

## Gerenciador de Recursos do Azure

O recurso Farm do SharePoint Server do Portal de Visualização do Azure cria máquinas virtuais no Gerenciamento de Serviços. Para criar farms do SharePoint Server 2013 no Gerenciador de Recursos do Azure, consulte [Implantar farms do SharePoint com os modelos do Gerenciador de Recursos do Azure](virtual-machines-workload-template-sharepoint.md).

## Recursos adicionais

[Farm do SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md)

[SharePoint em máquinas virtuais do Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

<!---HONumber=Sept15_HO3-->