<properties
	pageTitle="Farm do SharePoint Server | Microsoft Azure"
	description="Crie rapidamente um novo farm básico ou altamente disponível do SharePoint Server 2013 usando o Farm do SharePoint Server no Portal de Visualização do Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2015"
	ms.author="josephd"/>

# Farm do SharePoint Server

Com o Farm do SharePoint Server, o Portal de visualização do Microsoft Azure cria um farm pré-configurado do SharePoint Server 2013. Isso pode economizar muito tempo quando necessitar de um farm SharePoint básico ou de alta disponibilidade para um ambiente de desenvolvimento e teste, ou se estiver avaliando o SharePoint Server 2013 como uma solução de colaboração para a sua organização.

O farm do SharePoint básico consiste em três máquinas virtuais nesta configuração.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png)

É possível usar esta configuração de farm para uma configuração simplificada para o desenvolvimento do aplicativo SharePoint na sua primeira avaliação do SharePoint 2013.

O farm do SharePoint de alta disponibilidade consiste em nove máquinas virtuais nesta configuração.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png)

É possível usar esta configuração de farm para testar cargas de clientes maiores, alta disponibilidade do site externo do SharePoint e o SQL Server AlwaysOn para um farm do SharePoint. Também é possível usar esta configuração para a implementação do aplicativo SharePoint em um ambiente de alta disponibilidade.

Para os detalhes de configuração de ambos os farms, consulte [Detalhes de configuração de farm no SharePoint Server](virtual-machines-sharepoint-farm-config-azure-preview.md).

> [AZURE.NOTE]A Microsoft lançou o SharePoint Server 2016 IT Preview. Para facilitar a instalação e teste desta visualização, você pode usar uma imagem da galeria de máquinas virtuais do Azure com o SharePoint Server 2016 IT Preview e seus pré-requisitos pré-instalados. Para obter mais informações, veja [Testar o SharePoint Server 2016 IT Preview no Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Percorrendo a configuração

Para criar seu farm do SharePoint farm com o modelo de Farm do SharePoint Server, faça o seguinte:

1. No [Portal de Visualização do Microsoft Azure](https://portal.azure.com/), clique em **Novo** > **Computação** > **Farm do SharePoint Server**. Se **Farm do SharePoint Server** não aparecer, clique em **Novo** > **Computação** > **Marketplace**, digite **SharePoint** em **Pesquisar Computação** e depois clique em **Farm do SharePoint Server**. No painel **Farm do SharePoint Server**, clique em **Criar**.
2. No painel **Criar um farm do SharePoint**, digite o nome de um grupo de recursos.
3. Digite um nome de usuário e uma senha para uma conta de administrador local em cada máquina virtual em seu farm. Escolha um nome e uma senha difícil de adivinhar, grave-a e armazene-a em um local seguro.
4. Se desejar um farm de alta disponibilidade, clique em **Habilitar alta disponibilidade**.
5. Para configurar os seus controladores de domínio, clique na seta. É possível especificar um prefixo de nome do host (o padrão é o nome do grupo de recursos), o nome do domínio raiz de floresta (o padrão é contoso.com) e o tamanho dos seus controladores de domínio (o padrão é A1).
6. Para configurar os seus servidores SQL, clique na seta. É possível especificar um prefixo de nome do host (o padrão é o nome do grupo de recursos), o tamanho dos seus servidores SQL (o padrão é A5), um nome e uma senha da conta de acesso do banco de dados (o padrão é usar a conta do administrador) e um nome da conta de serviço do SQL Server (o padrão é sqlservice) e a senha (o padrão é usar a mesma senha da conta do administrador).
7. Para configurar os seus servidores SharePoint, clique na seta. É possível especificar um prefixo de nome do host (o padrão é o nome do grupo de recursos), o tamanho dos seus servidores SharePoint (o padrão é A2), uma conta do usuário do SharePoint (o padrão é sp\_setup) e a senha, um nome da conta farm do SharePoint (o padrão é sp\_farm) e a senha e um passphase de farm do SharePoint. O padrão é usar a senha do administrador para a conta do usuário do SharePoint, a conta do farm e o passphrase.
8. Para configurar os parâmetros de configuração opcionais de rede virtual, conta de armazenamento ou diagnóstico, clique na seta correspondente.
9. Para especificar a assinatura, clique na seta.
10. Quando tiver concluído, clique em **Criar**.

> [AZURE.NOTE]O controlador de domínio não tem as ferramentas de Gerenciamento do Active Directory instaladas por padrão. Para instalá-los, execute o comando **Install-WindowsFeature AD-Domain-Services -IncludeManagementTools** em um prompt de comando do Windows PowerShell com nível de administrador na máquina virtual do controlador de domínio.

## Acessando e gerenciando os farms do SharePoint

Os farms do SharePoint tem um ponto de extremidade pré-configurado para permitir o tráfego da Web não autenticado (porta TCP 80) para o servidor Web do SharePoint para um computador cliente conectado à Internet. Este ponto de extremidade é um site de equipe pré-configurado. Para acessa este site de equipe:

1.	No Portal de visualização do Azure, clique em **Navegar** e, em seguida, clique em **Grupos de Recursos**.
2.	Na lista de grupos de recursos, clique no nome do seu grupo de recursos do farm do SharePoint.
3.	No painel do seu grupo de recursos do farm do SharePoint, clique em **Histórico de implantação**.
4.	No painel **Histórico de implantação**, clique em **Microsoft.SharePointFarm**.
5.	No painel **Microsoft.SharePointFarm**, selecione a URL no campo **SHAREPOINTSITEURL** e copie-a.
6.	No seu navegador da Internet, cole a URL no campo de endereço.
7.	Quando for solicitado, digite as credenciais da conta do usuário especificado quando o farm foi criado.

No site Administração central do SharePoint, é possível configurar o My sites, os aplicativos SharePoint e outra funcionalidade. Para obter mais informações, consulte [Configurar o SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx). Para acessar o site Administração central do SharePoint:

1.	No Portal de visualização do Azure, clique em **Navegar** e, em seguida, clique em **Grupos de Recursos**.
2.	Na lista de grupos de recursos, clique no nome do seu grupo de recursos do farm do SharePoint.
3.	No painel do seu grupo de recursos do farm do SharePoint, clique em **Histórico de implantação**.
4.	No painel **Histórico de implantação**, clique em **Microsoft.SharePointFarm**.
5.	No painel **Microsoft.SharePointFarm**, selecione a URL no campo **SHAREPOINTCENTRALADMINURL** e copie-a.
6.	No seu navegador da Internet, cole a URL no campo de endereço.
7.	Quando for solicitado, digite as credenciais da conta do usuário especificado quando o farm foi criado.


Observações:

- O Portal de visualização do Azure cria estas máquinas virtuais na assinatura especificada.
- O Portal de visualização do Azure cria ambos os farms em uma rede virtual somente em nuvem com a presença da web voltada para a Internet. Não há nenhuma conexão de VPN site a site ou de Rota Expressa para a rede da sua organização.
- É possível administrar esses servidores por meio de conexões de área de trabalho remota. Para saber mais, confira [Como fazer logon em uma máquina virtual executando o Windows Server](virtual-machines-log-on-windows-server.md).

## Gerenciador de Recursos do Azure

O recurso Farm do SharePoint Server do Portal de Visualização do Azure cria máquinas virtuais com o modelo de implantação clássico. Para criar farms do SharePoint Server 2013 com o modelo de implantação do Gerenciador de Recursos do Azure, veja [Implantar farms do SharePoint com modelos do Gerenciador de Recursos do Azure](virtual-machines-workload-template-sharepoint.md).

## Recursos adicionais

[Detalhes da configuração do Farm do SharePoint Server](virtual-machines-sharepoint-farm-config-azure-preview.md)

[SharePoint nos serviços de infraestrutura do Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Farms do SharePoint hospedados nos serviços de infraestrutura do Azure](virtual-machines-sharepoint-infrastructure-services.md)

<!---HONumber=Sept15_HO2-->