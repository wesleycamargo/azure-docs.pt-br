<properties
	pageTitle="VM do Oracle WebLogic Server e Banco de dados| Microsoft Azure"
	description="Criar um imagem do Oracle WebLogic Server 12c e Oracle Database 12c do Azure em execução no Windows Server 2012, usando o modelo de implantação do Gerenciador de Recursos."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Criar uma máquina virtual Oracle WebLogic Server 12c e Oracle Database 12c no Azure

Este artigo mostra como criar uma máquina virtual baseada em uma imagem Oracle WebLogic Server 12c e Oracle Database 12c fornecida pela Microsoft em execução no Windows Server 2012 no Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.


##Para criar uma máquina virtual Oracle WebLogic Server 12c e Oracle Database 12c no Azure

1. Entre no [portal de visualização do Azure](https://ms.portal.azure.com/).

2.	Clique em **Marketplace**, em **Computação** e digite **Oracle** na caixa de pesquisa.

3.	Selecione a imagem **Oracle Database 12c e WebLogic Server 12c Standard Edition no Windows Server 2012** ou **Oracle Database 12c e WebLogic Server 12c Enterprise Edition no Windows Server 2012**. Examine as informações sobre essa imagem (como o tamanho mínimo recomendado) e depois clique em **Avançar**.

4.	Especifique um **Nome de Host** para a máquina virtual.

5.	Especifique um **Nome de Usuário** para a máquina virtual. Observe que esse nome de usuário serve para entrar remotamente na máquina virtual; esse não é o nome de usuário do banco de dados Oracle.

6.	Especifique e confirme uma senha para a máquina virtual ou forneça uma chave pública Secure Shell (SSH).

7.	Escolha um **Tipo de Preço**. Observe que as camadas de preço recomendadas são exibidas por padrão. Para ver todas as opções de configuração, clique em **Exibir tudo** no canto superior direito.

8. Defina as configurações opcionais, conforme o necessário. Siga estas considerações:

	a. Deixe **Conta de Armazenamento** como está para criar uma nova conta de armazenamento com o nome da máquina virtual.

	b. Deixe **Conjunto de Disponibilidade** como **Não Configurado**.

	c. Não adicione nenhum ponto de extremidade neste momento.

9.	Escolha ou crie um grupo de recursos. Para obter mais informações, consulte [Usando o portal de visualização do Azure para gerenciar os recursos do Azure](resource-group-portal.md).

10. Escolha uma **Assinatura**.

11. Escolha um **Local**.


##Para criar o banco de dados hospedado nesta máquina virtual

Siga as instruções em [Criar uma máquina virtual Oracle Database 12c no Azure](virtual-machines-creating-oracle-database-virtual-machine.md), começando com a seção **Para criar o banco de dados usando a máquina virtual Oracle Database 12c no Azure**.

##Para configurar o Oracle WebLogic Server 12c hospedado nesta máquina virtual
Siga as instruções em [Criar uma máquina virtual Oracle WebLogic Server 12c no Azure](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md), começando com a seção **Para configurar a máquina virtual Oracle WebLogic Server 12c no Azure**. Se você quiser configurar um cluster do WebLogic Server, consulte também [Criação de um cluster do Oracle WebLogic Server 12c no Azure](virtual-machines-creating-oracle-webLogic-server-12c-cluster.md).

##Recursos adicionais
[Considerações diversas sobre imagens de máquinas virtuais do Oracle](miscellaneous-considerations-for-oracle-virtual-machine-images-new-article.md)

[Lista de imagens de máquinas virtuais do Oracle](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

[Conexão ao Oracle Database de um aplicativo Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Oracle WebLogic Server 12c usando o Linux no Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_1125_2015-->