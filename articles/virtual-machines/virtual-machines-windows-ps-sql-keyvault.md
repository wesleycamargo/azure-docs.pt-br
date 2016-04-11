<properties
	pageTitle="Configurar a integração do Cofre da Chave do Azure para SQL Server em VMs do Azure (Gerenciador de Recursos)"
	description="Saiba como automatizar a configuração da criptografia do SQL Server para uso com o cofre de chave do Azure. Este tópico explica como usar a integração do Cofre da Chave do Azure com máquinas virtuais do SQL Server criadas com o Gerenciador de Recursos."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="03/24/2016"
	ms.author="jroth"/>

# Configurar a integração do Cofre da Chave do Azure para SQL Server em VMs do Azure (Gerenciador de Recursos)

> [AZURE.SELECTOR]
- [Gerenciador de Recursos](virtual-machines-windows-ps-sql-keyvault.md)
- [Clássico](virtual-machines-windows-classic-ps-sql-keyvault.md)

## Visão geral
Há vários recursos de criptografia SQL Server, como [transparent data encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [criptografia de nível de coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), e [criptografia de backup](https://msdn.microsoft.com/library/dn449489.aspx). Essas formas de criptografia exigem o gerenciamento e armazenamento de chaves criptográficas usadas para a criptografia. O serviço Cofre da Chave do Azure (AKV) foi criado para melhorar a segurança e o gerenciamento dessas chaves em um local seguro e altamente disponível. O [SQL Server Connector](http://www.microsoft.com/download/details.aspx?id=45344) permite que o SQL Server use essas chaves do Cofre da Chave do Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

Se você estiver executando o SQL Server em máquinas locais, há [etapas para acessar o Cofre da Chave do Azure da máquina local com SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Mas, para o SQL Server em VMs do Azure, você pode economizar tempo usando o recurso *Integração do Cofre da Chave do Azure*.

Quando esse recurso está habilitado, ele instala automaticamente o SQL Server Connector, configura o provedor de EKM a fim de acessar o Cofre da Chave do Azure e cria a credencial para permitir que você acesse seu cofre. Ao examinar as etapas da documentação local mencionada anteriormente, é possível ver que esse recurso automatiza as etapas 2 e 3. A única coisa que você ainda precisará fazer manualmente é criar o cofre da chave e as chaves. A partir daí, toda a configuração de sua VM do SQL será automatizada. Quando esse recurso concluir a configuração, você poderá executar instruções T-SQL para começar a criptografar seus bancos de dados ou backups como faria normalmente.

[AZURE.INCLUDE [Preparação para a integração de AKV](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## Habilitando a integração de AKV
Se você estiver provisionando uma nova máquina de virtual do SQL Server com o Gerenciador de Recursos, o Portal do Azure fornecerá uma etapa para habilitar a integração do Cofre de Chaves do Azure.

![Integração do cofre da chave do Azure com SQL ARM](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Para obter uma explicação detalhada sobre o provisionamento, consulte [Provisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

Se precisar habilitar a integração de AKV em uma VM existente, você poderá usar um modelo. Para obter mais informações, consulte [Modelo de início rápido do Azure para integração com o Cofre de Chaves do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-keyvault-setup).


[AZURE.INCLUDE [Próximas Etapas da Integração de AKV](../../includes/virtual-machines-sql-server-akv-next-steps.md)]

<!-----------HONumber=AcomDC_0330_2016-->