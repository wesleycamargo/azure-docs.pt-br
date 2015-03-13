<properties 
	pageTitle="Farm do SharePoint Server" 
	description="Descreve o novo recurso Farm do SharePoint Server disponível no Portal de Visualização do Azure" 
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

#Farm do SharePoint Server#

Com o Farm do SharePoint Server, o Portal de visualização do Microsoft Azure cria um farm pré-configurado do SharePoint Server 2013. Isso pode economizar muito tempo quando necessitar de um farm SharePoint básico ou de alta disponibilidade para um ambiente de desenvolvimento e teste, ou se estiver avaliando o SharePoint Server 2013 como uma solução de colaboração para a sua organização.

O farm do SharePoint básico consiste em três máquinas virtuais nesta configuração.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png)

É possível usar esta configuração de farm para uma configuração simplificada para o desenvolvimento do aplicativo SharePoint na sua primeira avaliação do SharePoint 2013.

O farm do SharePoint de alta disponibilidade consiste em nove máquinas virtuais nesta configuração.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png)

É possível usar esta configuração de farm para testar cargas de clientes maiores, alta disponibilidade do site externo do SharePoint e o SQL Server AlwaysOn para um farm do SharePoint. Também é possível usar esta configuração para a implementação do aplicativo SharePoint em um ambiente de alta disponibilidade.
 
Para os detalhes de configuração de ambos os farms, consulte [Detalhes de configuração de farm no SharePoint Server](../virtual-machines-sharepoint-farm-config-azure-preview/).

##Percorrendo a configuração##

Para criar seu farm do SharePoint farm com o modelo de Farm do SharePoint Server, faça o seguinte:

1. No [Portal de visualização do Microsoft Azure](https://portal.azure.com/), clique em **Novo** > **Farm do SharePoint Server**.
2. No painel **Criar um farm do SharePoint**, digite o nome de um grupo de recursos.
3. Digite um nome de usuário e uma senha para uma conta de administrador local em cada máquina virtual em seu farm. Escolha um nome e uma senha difícil de adivinhar, grave-a e armazene-a em um local seguro.
4. Se desejar um farm de alta disponibilidade, clique em **Habilitar alta disponibilidade**.
5. Para configurar os seus controladores de domínio, clique na seta. É possível especificar um prefixo de nome do host (o padrão é o nome do grupo de recursos), o nome do domínio raiz de floresta (o padrão é contoso.com) e o tamanho dos seus controladores de domínio (o padrão é A1).
6. Para configurar os seus servidores SQL, clique na seta. É possível especificar um prefixo de nome do host (o padrão é o nome do grupo de recursos), o tamanho dos seus servidores SQL (o padrão é A5), um nome e uma senha da conta de acesso do banco de dados (o padrão é usar a conta do administrador) e um nome da conta de serviço do SQL Server (o padrão é sqlservice) e a senha (o padrão é usar a mesma senha da conta do administrador).
7. Para configurar os seus servidores SharePoint, clique na seta. É possível especificar um prefixo de nome do host (o padrão é o nome do grupo de recursos), o tamanho dos seus servidores SharePoint (o padrão é A2), uma conta do usuário do SharePoint (o padrão é sp_setup) e a senha, um nome da conta farm do SharePoint (o padrão é sp_farm) e a senha e um passphase de farm do SharePoint. O padrão é usar a senha do administrador para a conta do usuário do SharePoint, a conta do farm e o passphrase.
8. Para configurar uma configuração opcional (a rede virtual, conta de armazenamento, diagnóstico), clique na seta.
9. Para especificar a assinatura, clique na seta.
10. Quando tiver concluído, clique em **Criar**.

##Acessando e gerenciando os farms do SharePoint##

Os farms do SharePoint tem um ponto de extremidade pré-configurado para permitir o tráfego da Web não autenticado (porta TCP 80) para o servidor Web do SharePoint para um computador cliente conectado à Internet. Este ponto de extremidade é um site de equipe pré-configurado. Para acessa este site de equipe:

1.	No Portal de visualização do Azure, clique em **Navegar** e, em seguida, clique em **Grupos de Recursos**. 
2.	Na lista de grupos de recursos, clique no nome do seu grupo de recursos do farm do SharePoint.
3.	No painel do seu grupo de recursos do farm do SharePoint, clique em **Histórico de implantação**. 
4.	No painel **Histórico de implantação**, clique em **Microsoft.SharePointFarm**.
5.	No painel **Microsoft.SharePointFarm**, selecione a URL no campo SHAREPOINTSITEURL e copie-a. 
6.	No seu navegador da Internet, cole a URL no campo de endereço.
7.	Quando for solicitado, digite as credenciais da conta do usuário especificado quando o farm foi criado.

No site Administração central do SharePoint, é possível configurar o My sites, os aplicativos SharePoint e outra funcionalidade. Para obter mais informações, consulte [Configurar o SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx). Para acessar o site Administração central do SharePoint:

1.	No Portal de visualização do Azure, clique em **Navegar** e, em seguida, clique em **Grupos de Recursos**. 
2.	Na lista de grupos de recursos, clique no nome do seu grupo de recursos do farm do SharePoint.
3.	No painel do seu grupo de recursos do farm do SharePoint, clique em **Histórico de implantação**. 
4.	No painel **Histórico de implantação**, clique em **Microsoft.SharePointFarm**.
5.	No painel **Microsoft.SharePointFarm**, selecione a URL no campo SHAREPOINTCENTRALADMINURL e copie-a. 
6.	No seu navegador da Internet, cole a URL no campo de endereço.
7.	Quando for solicitado, digite as credenciais da conta do usuário especificado quando o farm foi criado.


Observações:

- O Portal de visualização do Azure cria estas máquinas virtuais em sua assinatura.
- O Portal de visualização do Azure cria ambos os farms em uma rede virtual somente em nuvem com a presença da web voltada para a Internet. Esta não é uma conexão VPN de site para site em sua rede de organização. 
- É possível administrar estes servidores através de conexões de área de trabalho remota.


##Gerenciador de Recursos do Azure##

O Farm do SharePoint Server usa o Gerenciador de Recursos do Azure e os scripts para criar automaticamente as configurações do servidor para estes farms do SharePoint. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/).

##Recursos adicionais

[SharePoint nos serviços de infraestrutura do Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste](http://azure.microsoft.com/documentation/articles/virtual-networks-setup-sharepoint-hybrid-cloud-testing/)
<!--HONumber=42-->
