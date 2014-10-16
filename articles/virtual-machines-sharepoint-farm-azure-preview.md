<properties title="SharePoint Server Farm" pageTitle="SharePoint Server Farm" description="Describes the new SharePoint Server Farm feature available in the Azure Preview Portal" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="josephd"></tags>

# Farm do SharePoint Server

Com o Farm do SharePoint Server, o Portal de visualização do Microsoft Azure cria um farm pré-configurado do SharePoint Server 2013. Isso pode economizar muito tempo quando necessitar de um farm SharePoint básico ou de alta disponibilidade para um ambiente de desenvolvimento e teste, ou se estiver avaliando o SharePoint Server 2013 como uma solução de colaboração para a sua organização.

O farm básico de SharePoint consiste em três máquinas virtuais na configuração a seguir:

![sharepointfarm][]

É possível usar esta configuração de farm para uma configuração simplificada para o desenvolvimento do aplicativo SharePoint na sua primeira avaliação do SharePoint 2013.

O farm de alta disponibilidade do SharePoint consistem em nove máquinas virtuais na configuração a seguir:

![sharepointfarm][1]

É possível usar esta configuração de farm para testar cargas de clientes maiores, alta disponibilidade do site externo do SharePoint e o SQL Server AlwaysOn para um farm do SharePoint. Também é possível usar esta configuração para a implementação do aplicativo SharePoint em um ambiente de alta disponibilidade.

Para os detalhes de configuração de ambos os farms, consulte [Detalhes de configuração de farm no SharePoint Server][].

Estes farms possuem um ponto de extremidade pré-configurado para permitir o tráfego autenticado da web (porta 80 de TPC) ao servidor Web do SharePoint através de um computador cliente conectado à Internet. Este ponto de extremidade é um site de equipe pré-configurado. Para acessar este site de equipe, primeiro deve-se obter o nome do domínio do servidor Web do farm do SharePoint:

1.  No Portal de visualização do Azure, clique em **Navegar** e, em seguida, clique em **Máquinas Virtuais**.
2.  Na lista de máquinas virtuais, clique no nome do seu servidor Web (termina em SP, WEB1 ou WEB2).
3.  No painel do seu computador virtual do servidor Web, clique em**Propriedades**.
4.  Observe o FQDN no **Nome do domínio**.
5.  No navegador de Internet, acesse a URL **[http://[FQDN]][]**. Por exemplo, para o nome do domínio spfarm1-web1-contoso.cloudpapp.net, a URL é **<http://spfarm1-web1-contoso.cloudpapp.net>**.
6.  Quando for solicitado, digite as credenciais da conta do usuário specificado quando o farm foi criado.

No site Administração central do SharePoint, é possível configurar o My sites, os aplicativos SharePoint e outra funcionalidade. Para obter mais informações, consulte [Configurar o SharePoint 2013][]. Para acessar o site Administração central do SharePoint, primeiro deve-se obter o seu número de porta externa.

1.  No Portal de visualização do Azure, clique em **Navegar** e, em seguida, clique em **Máquinas Virtuais**.
2.  Na lista de máquinas virtuais, clique no nome do SharePoint Server para a configuração básica (termina em SP) oun o nome dos servidores do aplicativo para a configuração de alta disponibilidade (termina em APP1 ou APP2).
3.  No painel da máquina virtual, clique em **Propriedades**.
4.  Observe o FQDN no **Nome do domínio**.
5.  Feche o painel **Propriedades**.
6.  No painel da máquina virtual, role para baixo para a lista de **Terminais**.
7.  Observe o número da porta para o ponto de extremidade nomeado SPCentralAdmin.
8.  No navegador de Internet, acesse a URL **[http://[FQDN]][]:[port number]**. Por exemplo, para o nome do domínio spfarm1-web1-contoso.cloudpapp.net e o número de porta externa 54398, a URL é **<http://spfarm1-app1-contoso.cloudpapp.net:54398>**.
9.  Quando for solicitado, digite as credenciais da conta do usuário specificado quando o farm foi criado.

Observações:

-   O Portal de visualização do Azure cria estas máquinas virtuais em sua assinatura.
-   O Portal de visualização do Azure cria ambos os farms em uma rede virtual somente em nuvem com a presença da web voltada para a Internet. Esta não é uma conexão VPN de site para site em sua rede de organização.
-   É possível administrar estes servidores através de conexões de área de trabalho remota.

## Percorrendo a configuração

Para criar o seu farm do SharePoint com o SharePoint Farm, faça o seguinte:

1.  No [Portal de visualização do Microsoft Azure][], clique em **Máquinas Virtuais** \> **Farm do SharePoint Server**.
2.  No painel **Criar um farm do SharePoint**, digite o nome de um grupo de recursos.
3.  Digite um nome do usuário e uma senha para uma conta de administrador local em cada máquina virtual em seu farm. Escolha um nome e uma senha difíceis de adivinhar.
4.  Se desejar um farm de alta disponibilidade, clique em **Habilitar alta disponibilidade**.
5.  Para configurar os seus controladores de domínio, clique na seta. É possível especificar um prefixo de nome do host (o padrão é o nome do grupo de recursos), o nome do domínio raiz de floresta (o padrão é contoso.com) e o tamanho dos seus controladores de domínio (o padrão é A1).
6.  Para configurar os seus servidores SQL, clique na seta. É possível especificar um prefixo de nome do host (o padrão é o nome do grupo de recursos), o tamanho dos seus servidores SQL (o padrão é A5), um nome e uma senha da conta de acesso do banco de dados (o padrão é usar a conta do administrador) e um nome da conta de serviço do SQL Server (o padrão é sqlservice) e a senha (o padrão é usar a mesma senha da conta do administrador).
7.  Para configurar os seus servidores SharePoint, clique na seta. É possível especificar um prefixo de nome do host (o padrão é o nome do grupo de recursos), o tamanho dos seus servidores SQL (o padrão é A23), uma conta do usuário do SharePoint (o padrão é sp\_setup) e a senha, um nome da conta farm do SharePoint (o padrão é sp\_farm) e a senha e um passphase de farm do SharePoint. O padrão é usar a senha do administrador para a conta do usuário do SharePoint, a conta do farm e o passphrase.
8.  Para configurar a configuração opcional (sua rede virtual, ou a conta de armazenamento e diagnóstico), clique na seta.
9.  Para especificar a assinatura, clique na seta.
10. Para especificar a localização (região), clique na seta.
11. Quando tiver concluído, clique em **Criar**.

## Gerenciador de Recursos do Azure

O Farmo do SharePoint Server usa o Gerenciador de Recursos do Azure e os scripts para criar automaticamente as configurações do servidor para estes farms do SharePoint. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos][].

  [sharepointfarm]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png
  [1]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png
  [Detalhes de configuração de farm no SharePoint Server]: ../virtual-machines-sharepoint-farm-config-azure-preview/
  
  [Configurar o SharePoint 2013]: http://technet.microsoft.com/library/ee836142.aspx
  [Portal de visualização do Microsoft Azure]: https://portal.azure.com/
  [Usando o Windows PowerShell com o Gerenciador de Recursos]: http://azure.microsoft.com/pt-br/documentation/articles/powershell-azure-resource-manager/
