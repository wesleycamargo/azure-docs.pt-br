<properties 
	pageTitle="Configurar os Grupos de Disponibilidade AlwaysOn no Azure (GUI)"
	description="Crie um Grupo de Disponibilidade AlwaysOn em Máquinas Virtuais do Azure. Este tutorial usa principalmente a interface do usuário e ferramentas em vez de scripts."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/12/2015"
	ms.author="jroth"/>

# Configurar os Grupos de Disponibilidade AlwaysOn no Azure (GUI)

Este tutorial ponta a ponta mostra como implementar os Grupos de Disponibilidade usando o SQL Server AlwaysOn em execução em máquinas virtuais do Azure.

>[AZURE.NOTE]No Portal de Gerenciamento do Azure, há uma nova configuração de galeria para Grupos de Disponibilidade AlwaysOn com um Ouvinte. Isso configura tudo o que você precisa para Grupos de Disponibilidade AlwaysOn automaticamente. Para obter mais informações, consulte [Oferta do AlwaysOn do SQL Server na Galeria do Portal do Microsoft Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). Para usar o PowerShell, consulte o tutorial do mesmo cenário em [Configurar Grupos de Disponibilidade AlwaysOn no Azure com o PowerShell](virtual-machines-sql-server-alwayson-availability-groups-powershell.md).

Ao final do tutorial, sua solução SQL Server AlwaysOn no Azure consistirá nos seguintes elementos:

- Uma rede virtual que contém várias sub-redes, incluindo uma de front-end e uma de back-end

- Um controlador de domínio com um domínio do AD (Active Directory)

- Duas VMs do SQL Server implantadas na sub-rede de back-end e ingressadas no domínio do AD

- Um cluster WSFC de três nós com o modelo de quórum de Nó Principal

- Um grupo de disponibilidade com duas réplicas de confirmação síncrona de um banco de dados de disponibilidade

A figura abaixo é uma representação gráfica da solução.

![Arquitetura de laboratório de teste para o AG no Azure](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC791912.png)

Observe que se trata de uma das configurações possíveis. Por exemplo, você pode minimizar o número de VMs para um grupo de disponibilidade de duas réplicas para economizar horas de computação no Azure usando o controlador de domínio como a testemunha de compartilhamento de arquivo de quórum em um cluster do WSFC de dois nós. Esse método reduz a contagem de VMs em uma em comparação com a configuração acima.

Este tutorial pressupõe o seguinte:

- Você já tem uma conta do Azure.

- Você já sabe como provisionar uma VM do SQL Server da galeria de máquinas virtuais usando a GUI. Para obter mais informações, consulte [Provisionando uma Máquina Virtual do SQL Server no Azure](virtual-machines-provision-sql-server.md)

- Você já tem uma compreensão sólida dos Grupos de Disponibilidade AlwaysOn. Para obter mais informações, consulte [Grupos de Disponibilidade AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE]Se você estiver interessado em usar os Grupos de Disponibilidade AlwaysOn com o SharePoint, consulte também [Configurar os Grupos de Disponibilidade AlwaysOn do SQL Server 2012 para o SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## Criar a Rede Virtual e o Servidor de Controlador de Domínio

Comece com uma nova conta de avaliação do Azure. Depois de concluir a configuração da conta, você deverá estar na tela inicial do Portal do Azure.

1. Clique no botão **Novo** no canto inferior esquerdo da página, conforme mostrado abaixo.

	![Clique em Novo no Portal](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665511.gif)

1. Clique em **Serviços de Rede**, em **Rede Virtual,** e em **Criação Personalizada**, conforme mostrado abaixo.

	![Criar Rede Virtual](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665512.gif)

1. Na caixa de diálogo **CRIAR UMA REDE VIRTUAL**, crie uma nova rede virtual percorrendo as páginas com as configurações a seguir.

	|Página|Configurações|
|---|---|
|Detalhes de rede virtual|**NAME = ContosoNET**<br/>**REGION = West US**|
|Conectividade de VPN e servidores DNS|Nenhum|
|Espaços de Endereço da Rede Virtual|As configurações são mostradas na captura de tela abaixo: ![Criar Rede Virtual](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784620.png)|

1. Em seguida, crie a VM que será usada como o DC (Controlador de Domínio). Clique novamente em **Novo**, depois em **Computação**, em **Máquina Virtual** e depois em **Da Galeria**, conforme mostrado abaixo.

	![Criar uma máquina virtual](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784621.png)

1. Na caixa de diálogo **CRIAR UMA MÁQUINA VIRTUAL**, configure uma nova VM percorrendo as páginas com as configurações a seguir.

	|Página|Configurações|
|---|---|
|Selecionar o sistema operacional da máquina virtual|Windows Server 2012 R2 Datacenter|
|Configuração de máquina virtual|**VERSION RELEASE DATE** = (mais recente)<br/>**VIRTUAL MACHINE NAME** = ContosoDC<br/>**TIER** = BASIC<br/>**SIZE** = A2 (dois núcleos)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000|
|Configuração de máquina virtual|**CLOUD SERVICE** = Criar um novo serviço de nuvem<br/>**CLOUD SERVICE DNS NAME** = Um nome de serviço de nuvem exclusivo<br/>**DNS NAME** = Um nome exclusivo (ex.: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Traseira(10.10.2.0/24)<br/>**STORAGE ACCOUNT** = Use uma conta de armazenamento gerada automaticamente<br/>**AVAILABILITY SET** = (Nenhum)|
|Opções de máquina virtual|Usar padrões|

Ao terminar de configurar a nova VM, aguarde até ela ser provisionada. Esse processo leva algum tempo para ser concluído e se você clicar na guia **Máquina Virtual** no Portal do Azure, poderá ver os estados de ciclo de ContosoDC desde **Iniciando (Provisionando)** até **Parado**, **Iniciando**, **Em execução (Provisionando)** e, finalmente, **Em execução**.

O servidor de DC agora foi provisionado com êxito. Em seguida, você configurará o domínio do Active Directory nesse servidor de DC.

## Configurar o Controlador de Domínio

Nas etapas a seguir, você poderá configurar o computador ContosoDC como um controlador de domínio para corp.contoso.com.

1. No portal, selecione o computador **ContosoDC**. Na guia **Painel**, clique em **Conectar** para abrir um arquivo RDP para o acesso à área de trabalho remota.

	![Conectar à Máquina Virtual](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784622.png)

1. Faça logon com sua conta de administrador configurada (**\\AzureAdmin**) e a senha (**Contoso!000**).

1. Por padrão, o painel **Gerenciador do Servidor** deve ser exibido.

1. Clique no link **Adicionar funções e recursos** no painel.

	![Adicionar Funções ao Gerenciador de Servidores](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784623.png)

1. Selecione **Avançar** até alcançar a seção **Funções de Servidor**.

1. Selecione os **Serviços de Domínio do Active Directory** e as funções do **Servidor DNS**. Quando solicitado, acrescente os recursos adicionais necessários para essas funções.

	>[AZURE.NOTE]Você receberá um aviso de validação de que não há nenhum endereço IP estático. Se você estiver testando a configuração, clique em continuar. Para cenários de produção, [use o PowerShell para definir o endereço IP estático do computador do controlador de domínio](./virtual-network/virtual-networks-reserved-private-ip.md).

	![Adicionar Caixa de Diálogo de Funções](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784624.png)

1. Clique em **Avançar** até alcançar a seção **Confirmação**. Marque a caixa de seleção **Reiniciar o servidor de destino automaticamente se necessário**.

1. Clique em **Instalar**.

1. Depois que os recursos forem instalados, retorne para o painel **Gerenciador do Servidor**.

1. Selecione a nova opção **AD DS** no painel esquerdo.

1. Clique no link **Mais** na barra de aviso amarela.

	![Caixa de diálogo do AD DS na VM do servidor DNS](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784625.png)

1. Na coluna **Ação** da caixa de diálogo **Todos os Detalhes de Tarefa do Servidor**, clique em **Promover este servidor a um controlador de domínio**.

1. No **Assistente de Configuração dos Serviços de Domínio do Active Directory**, use os seguintes valores:

	|Página|Configuração|
|---|---|
|Configuração de Implantação|**Adicionar uma nova floresta** = Selecionado<br/>**Nome do domínio raiz** = corp.contoso.com|
|Opções de Controlador de Domínio|**Senha** = Contoso!000<br/>**Confirmar Senha** = Contoso!000|

1. Clique em **Avançar** para percorrer as outras páginas do assistente. Na página **Verificação de pré-requisitos**, verifique se a seguinte mensagem é exibida: **Todas as verificações de pré-requisitos passaram com êxito**. Observe que você deve examinar todas as mensagens de aviso aplicáveis, porém é possível continuar com a instalação.

1. Clique em **Instalar**. A máquina virtual **ContosoDC** será reinicializada automaticamente.

## Configurar Contas de Domínio

As próximas etapas configuram as contas do AD (Active Directory) para uso posterior.

1. Faça logon novamente no computador **ContosoDC**.

1. Em **Gerenciador do Servidor**, selecione **Ferramentas** e, em seguida, clique em **Centro Administrativo do Active Directory**.

	![Centro Administrativo do Active Directory](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784626.png)

1. No **Centro Administrativo do Active Directory**, selecione **corp (local)** no painel à esquerda.

1. No painel **Tarefas** à direita, selecione **Novo** e clique em **Usuário**. Use as configurações a seguir:

	|Configuração|Valor|
|---|---|
|**Nome**|Instalar|
|**SamAccountName do usuário**|Instalar|
|**Senha**|Contoso!000|
|**Confirmar senha**|Contoso!000|
|**Outras opções de senha**|Selecionado|
|**A senha nunca expira**|Verificado|

1. Clique em **OK** para criar o usuário **Instalação**. Essa conta será usada para configurar o cluster de failover e o grupo de disponibilidade.

1. Crie dois usuários adicionais com as mesmas etapas: **CORP\\SQLSvc1** e **CORP\\SQLSvc2**. Essas contas serão usadas para instâncias do SQL Server. Em seguida, você precisa conceder **CORP\\Install** as permissões necessárias para configurar o WSFC (Windows Service Failover Clustering).

1. No **Centro Administrativo do Active Directory**, selecione **corp (local)** no painel à esquerda. Em seguida, no painel **Tarefas** à direita, clique em **Propriedades**.

	![Propriedades do usuário CORP](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784627.png)

1. Selecione **Extensões**, e clique no botão **Avançado** na guia **Segurança**.

1. Na caixa de diálogo **Configurações de Segurança Avançadas para corp**. Clique em **Adicionar**.

1. Clique em **Selecione uma entidade**. Em seguida, procure **CORP\\Install**. Clique em **OK**.

1. Selecione as permissões **Ler todas as propriedades** e **Criar objetos de computador**.

	![Permissões de usuário corp](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784628.png)

1. Clique em **OK**, e clique em **OK** novamente. Feche a janela de propriedades corporativas.

Agora que você concluiu a configuração do Active Directory e dos objetos de usuário, você criará três VMs do SQL Server e vai associá-las a esse domínio.

## Criar VMs do SQL Server

Em seguida, crie três VMs, incluindo um nó de cluster WSFC e duas VMs do SQL Server. Para criar cada uma das VMs, volte ao Portal do Azure, clique em **Novo**, **Computação**, **Máquina Virtual** e **Da Galeria**. Em seguida, use os modelos na tabela a seguir para ajudá-lo a criar as VMs.

|Página|VM1|VM2|VM3|
|---|---|---|---|
|Selecionar o sistema operacional da máquina virtual|**Windows Server 2012 R2 Datacenter**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|Configuração de máquina virtual|**VERSION RELEASE DATE** = (mais recente)<br/>**VIRTUAL MACHINE NAME** = ContosoWSFCNode<br/>**TIER** = BASIC<br/>**SIZE** = A2 (dois núcleos)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000|**VERSION RELEASE DATE** = (mais recente)<br/>**VIRTUAL MACHINE NAME** = ContosoSQL1<br/>**TIER** = BASIC<br/>**SIZE** = A3 (quatro núcleos)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000|**VERSION RELEASE DATE** = (mais recente)<br/>**VIRTUAL MACHINE NAME** = ContosoSQL2<br/>**TIER** = BASIC<br/>**SIZE** = A3 (quatro núcleos)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000|
|Configuração de máquina virtual|**CLOUD SERVICE** = Nome DNS do serviço de nuvem exclusivo criado previamente (ex.: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Traseira(10.10.2.0/24)<br/>**STORAGE ACCOUNT** = Use uma conta de armazenamento gerada automaticamente<br/>**AVAILABILITY SET** = Crie um conjunto de disponibilidade<br/>**AVAILABILITY SET NAME** = SQLHADR|**CLOUD SERVICE** = Nome DNS do serviço de nuvem exclusivo criado previamente (ex.: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Traseira(10.10.2.0/24)<br/>**STORAGE ACCOUNT** = Use uma conta de armazenamento gerada automaticamente<br/>**AVAILABILITY SET** = SQLHADR (Você também pode configurar o conjunto de disponibilidade depois da criação da máquina. Todas as três máquinas devem ser atribuídas ao conjunto de disponibilidade SQLHADR.)|**CLOUD SERVICE** = Nome DNS do serviço de nuvem exclusivo criado previamente (ex.: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Traseira(10.10.2.0/24)<br/>**STORAGE ACCOUNT** = Use uma conta de armazenamento gerada automaticamente<br/>**AVAILABILITY SET** = SQLHADR (Você também pode configurar o conjunto de disponibilidade depois da criação da máquina. Todas as três máquinas devem ser atribuídas ao conjunto de disponibilidade SQLHADR.)|
|Opções de máquina virtual|Usar padrões|Usar padrões|Usar padrões|

Depois que as três VMs forem totalmente provisionadas, você precisará ingressá-las no domínio **corp.contoso.com** e conceder direitos administrativos de CORP\\Install às máquinas. Para fazer isso, use as seguintes etapas para cada uma das três VMs.

1. Primeiro, altere o endereço do servidor DNS preferencial. Comece baixando o arquivo RDP (Área de Trabalho Remota) de cada VM em seu diretório local selecionando-a na lista e clicando no botão **Conectar**. Para selecionar uma VM, clique em qualquer lugar exceto na primeira célula na linha, conforme mostrado abaixo.

	![Baixe o arquivo RDP](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664953.jpg)

1. Inicie o arquivo RDP baixado e faça logon na VM usando sua conta de administrador configurada (**BUILTIN\\AzureAdmin**) e a senha (**Contoso!000**).

1. Quando você estiver conectado, deverá ver o painel **Gerenciador do Servidor**. No painel esquerdo, clique em **Servidor Local**.

1. Selecione o link **Endereço IPv4 atribuído pelo DHCP, habilitado para IPv6**.

1. Na janela **Conexões de Rede**, selecione o ícone de rede.

	![Alterar o servidor DNS preferencial da VM](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784629.png)

1. Na barra de comandos, clique em **Alterar as configurações desta conexão** (dependendo do tamanho da janela, talvez você precise clicar na seta dupla à direita para ver esse comando).

1. Clique em **Protocolo IP versão 4 (TCP/IPv4)** e clique em Propriedades.

1. Selecione Usar os seguintes endereços de servidor DNS e especifique **10.10.2.4** em **Servidor DNS preferencial**.

1. O endereço **10.10.2.4** é o endereço atribuído a uma VM na sub-rede 10.10.2.0/24 em uma rede virtual do Azure, e essa VM é **ContosoDC**. Para verificar o endereço IP de **ContosoDC**, use o **nslookup contosodc** no prompt de comando, conforme mostrado abaixo.

	![Use NSLOOKUP para localizar o endereço IP para DC](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664954.jpg)

1. Clique em O**K** e em **Fechar** para confirmar as alterações. Agora é possível adicionar a VM a **corp.contoso.com**.

1. Volte para a janela **Servidor Local** e clique no link **GRUPO DE TRABALHO**.

1. Na seção **Nome do Computador**, clique em **Alterar**.

1. Marque a caixa de seleção **Domínio** e digite **corp.contoso.com** na caixa de texto. Clique em **OK**.

1. Na caixa de diálogo pop-up **Segurança do Windows**, especifique as credenciais para a conta de administrador de domínio padrão (**CORP\\AzureAdmin**) e a senha (**Contoso!000**).

1. Quando você vir a mensagem "Bem-vindo ao domínio corp.contoso.com", clique em **OK**.

1. Clique em **Fechar** e em **Reiniciar Agora** na caixa de diálogo pop-up.

### Adicione o usuário Corp\\Install como um administrador em cada VM:

1. Aguarde até que a VM seja reiniciada e abra o arquivo RDP para fazer logon na VM usando a conta **BUILTIN\\AzureAdmin**.

1. Em **Gerenciador do Servidor**, selecione **Ferramentas** e clique em **Gerenciamento do Computador**.

	![Gerenciamento do computador](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784630.png)

1. Na janela **Gerenciamento do Computador**, expanda **Usuários e Grupos Locais** e selecione **Grupos**.

1. Clique duas vezes no grupo **Administradores**.

1. Na caixa de diálogo **Propriedades de Administradores**, clique no botão **Adicionar**.

1. Insira o usuário **CORP\\Install**, e clique em **OK**. Quando solicitado a fornecer credenciais, use a conta **AzureAdmin** com a senha **Contoso!000**.

1. Clique em **OK** para fechar a caixa do diálogo **Propriedades do Administrador**.

### Adicione o serviço de **Clustering de Failover** a cada VM.

1. No painel **Gerenciador do Servidor**, clique em **Adicionar funções e recursos**.

1. No **Assistente para Adicionar Funções e Recursos**, clique em **Avançar** até chegar à página **Recursos**.

1. Selecione **Clustering de Failover**. Quando solicitado, adicione todos os outros recursos dependentes.

	![Adicionar o Recurso de Cluster de Failover à VM](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784631.png)

1. Clique em **Avançar** e em **Instalar** na página **Confirmação**.

1. Quando a instalação do recurso **Clustering de Failover** for concluída, clique em **Fechar**.

1. Faça logoff da VM.

1. Repita as etapas nesta seção para todos os três servidores - **ContosoWSFCNode**, **ContosoSQL1** e **ContosoSQL2**.

As VMs do SQL Server agora estão provisionadas e em execução, mas estão instaladas com o SQL Server com as opções padrão.

## Criar o Cluster WSFC

Nesta seção, você deve criar o cluster WSFC que hospedará o grupo de disponibilidade que você criará posteriormente. Agora, você deve ter feito o seguinte para cada uma das três VMs que serão usadas no cluster WSFC:

- Totalmente provisionado no Azure

- Ingressou a VM no domínio

- **CORP\\Install** adicionado ao grupo de Administradores local

- Recurso de Clustering de Failover adicionado

Todos são pré-requisitos em cada VM para poder ingressá-la no cluster WSFC.

Além disso, observe que a rede virtual do Azure não se comporta da mesma forma que uma rede local. Você precisa criar o cluster na seguinte ordem:

1. Crie um cluster de nó único em um de nós (**ContosoSQL1**).

1. Modifique o endereço IP do cluster para um endereço IP não usado (**10.10.2.101**).

1. Deixe o nome do cluster online.

1. Adicionar outros nós (**ContosoSQL2** e **ContosoWSFCNode**).

Siga as etapas abaixo para executar essas tarefas que definem totalmente o cluster.

1. Inicie o arquivo RDP para **ContosoSQL1** e faça logon usando a conta de domínio **CORP\\Install**.

1. No painel **Gerenciador do Servidor**, selecione **Ferramentas** e clique em **Gerenciador de Cluster de Failover**.

1. No painel esquerdo, clique com o botão direito do mouse em **Gerenciador de Cluster de Failover** e clique em **Criar um Cluster**, conforme mostrado abaixo.

	![Criar Cluster](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784632.png)

1. No Assistente para Criação de Cluster, crie um cluster de um nó percorrendo as páginas com as configurações a seguir:

	|Página|Configurações|
|---|---|
|Antes de começar|Usar padrões|
|Selecionar Servidores|Digite **ContosoSQL1** em **Digite o nome do servidor** e clique em **Adicionar**|
|Aviso de Validação|Selecione **Não. Eu não preciso de suporte da Microsoft para este cluster e, portanto, não desejo executar testes de validação. Continuar a criar o cluster quando eu clicar em Avançar**.|
|Ponto de Acesso para Administrar o Cluster|Digite **Cluster1** em **Nome do Cluster**|
|Confirmação|Use os padrões, a menos que você esteja usando Espaços de Armazenamento. Consulte a observação após esta tabela.|

	>[AZURE.WARNING]Se você estiver usando [Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739), que agrupam vários discos em pools de armazenamento, desmarque a caixa de seleção **Adicionar todo o armazenamento qualificado ao cluster** na página **Confirmação**. Se você não desmarcar essa opção, os discos virtuais serão desanexados durante o processo de clustering. Como resultado, eles também não aparecerão no Gerenciador ou Explorador de Discos até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell.

1. No painel esquerdo, expanda o **Gerenciador de Cluster de Failover** e clique em **Cluster1.corp.contoso.com**.

1. No painel central, role para baixo até a seção **Recursos Principais do Cluster** e expanda os detalhes **Nome: Clutser1**. Você verá os recursos de **Nome** e de **Endereço IP** no estado **Com Falha**. O recurso de endereço IP não pode ficar online porque o cluster recebeu o mesmo endereço IP que a própria máquina, que é um endereço duplicado.

1. Clique com o botão direito no recurso **Endereço IP** com falha e clique em **Propriedades**.

	![Propriedades do Cluster](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784633.png)

1. Selecione **Endereço IP Estático** e especifique **10.10.2.101** na caixa de texto Endereço. Em seguida, clique em **OK**.

1. Na seção **Recursos Principais do Cluster**, clique com o botão direito do mouse em **Nome: Cluster1** e clique em **Colocar Online**. Em seguida, aguarde até que ambos os recursos estejam online. Quando o recurso de nome de cluster fica online, ele atualiza o servidor DC com uma nova conta de computador do AD. Essa conta AD será usada para executar o serviço clusterizado do grupo de disponibilidade posteriormente.

1. Por fim, você deve adicionar os nós restantes ao cluster. Na árvore do navegador, clique com o botão direito do mouse em **Cluster1.corp.contoso.com** e clique em **Adicionar Nó**, conforme mostrado abaixo.

	![Adicionar Nó ao Cluster](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784634.png)

1. No **Assistente para Adicionar Nó**, clique em **Avançar**. Na página **Selecionar Servidores**, adicione **ContosoSQL2** e **ContosoWSFCNode** à lista digitando o nome do servidor em **Digite o nome do servidor** e clicando em **Adicionar**. Quando terminar, clique em **Avançar**.

1. Na página **Aviso de Validação**, clique em **Não** (em um cenário de produção, você deve executar os testes de validação). Em seguida, clique em **Avançar**.

1. Na página **Confirmação**, clique em **Avançar** para adicionar os nós.

	>[AZURE.WARNING]Se você estiver usando [Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739), que agrupam vários discos em pools de armazenamento, desmarque a caixa de seleção **Adicionar todo o armazenamento qualificado ao cluster**. Se você não desmarcar essa opção, os discos virtuais serão desanexados durante o processo de clustering. Como resultado, eles também não aparecerão no Gerenciador ou Explorador de Discos até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell.

1. Depois que os nós forem adicionados ao cluster, clique em **Concluir**. O Gerenciador de Cluster de Failover agora deve mostrar que o cluster tem três nós e listá-los no contêiner **Nós**.

1. Faça logoff da sessão de área de trabalho remota.

## Preparar instâncias do SQL Server para o Grupo de Disponibilidade

Nesta seção, você fará o seguinte em ambos os **ContosoSQL1** e **contosoSQL2**:

- Adicionar um logon para **NT AUTHORITY\\System** com um conjunto de permissões necessárias para a instância padrão do SQL Server

- Adicionar **CORP\\Install** como uma função de sysadmin à instância padrão do SQL Server

- Abrir o firewall para acesso remoto do SQL Server

- Habilitar o recurso de Grupos de Disponibilidade AlwaysOn

- Alterar a conta de serviço do SQL Server para **CORP\\SQLSvc1** e **CORP\\SQLSvc2**, respectivamente

Essas ações podem ser executadas em qualquer ordem. No entanto, as etapas a seguir as percorrerá em ordem. Siga as etapas para ambos **ContosoSQL1** e **ContosoSQL2**:

1. Se você não tiver feito logoff da sessão de área de trabalho remota para a VM, faça isso agora.

1. Inicie os arquivos RDP para **ContosoSQL1** e **ContosoSQL2** e faça logon como **BUILTIN\\AzureAdmin**.

1. Primeiro, adicione **NT AUTHORITY\\System** aos logons do SQL Server e com as permissões necessárias. Abra o **SQL Server Management Studio**.

1. Clique em **Conectar** para se conectar à instância padrão do SQL Server.

1. No **Pesquisador de Objetos**, expanda **Segurança** e **Logons**.

1. Clique com o botão direito do mouse no logon **NT AUTHORITY\\System** e clique em **Propriedades**.

1. Na página **Protegíveis**, para o servidor local, selecione **Conceder** para as seguintes permissões e clique em **OK**.
	
	- Alterar qualquer grupo de disponibilidade
	
	- Conectar o SQL
	
	- Exibir o estado do servidor

1. Em seguida, adicione **CORP\\Install** como uma função de **sysadmin** à instância padrão do SQL Server. Em **Pesquisador de Objetos**, clique com o botão direito do mouse em **Logons** e clique em **Novo Logon**.

1. Digite **CORP\\Install** em **Nome de Logon**.

1. Na página **Funções de Servidor**, selecione **sysadmin**. Em seguida, clique em **OK**. Depois que o logon for criado, você pode vê-lo expandindo **Logons** no **Pesquisador de Objetos**.

1. Em seguida, crie uma regra de firewall para o SQL Server. Na tela **Iniciar**, abra o **Firewall do Windows com Segurança Avançada**.

1. No painel esquerdo, selecione **Regras de Entrada**. No painel direito, clique em **Nova Regra**.

1. Na página **Tipo de Regra**, selecione o **Programa** e clique em **Avançar**.

1. Na página **Programa**, selecione **Este caminho de programa** e digite **%Arquivos de Programas%\\Microsoft SQL Server\\MSSQL12.MSSQLSERVER\\MSSQL\\Binn\\sqlservr.exe** na caixa de texto (se você estiver seguindo estas instruções, mas usando o SQL Server 2012, o diretório do SQL Server é **MSSQL11.MSSQLSERVER**). Em seguida, clique em **Próximo**.

1. Na página **Ação**, mantenha selecionado **Permitir a conexão** e clique em **Próximo**.

1. Na página **Perfil** aceite as configurações padrão e clique em **Próximo**.

1. Na página **Nome**, especifique um nome de regra como **SQL Server (Regra de Programa)** na caixa de texto **Nome** e clique em **Concluir**.

1. Em seguida, habilite o recurso de **Grupos de Disponibilidade AlwaysOn**. Na tela **Iniciar**, abra o **SQL Server Configuration Manager**.

1. Na árvore do navegador, clique em **Serviços do SQL Server**, clique com o botão direito do mouse no serviço **SQL Server (MSSQLSERVER)** e clique em **Propriedades**.

1. Clique na guia **Alta Disponibilidade AlwaysOn** e selecione **Habilitar Grupos de Disponibilidade AlwaysOn**, conforme mostrado abaixo, e clique em **Aplicar**. Clique em **OK** na caixa de diálogo pop-up e não feche a janela Propriedades por enquanto. Você reiniciará o serviço SQL Server depois de alterar a conta de serviço.

	![Habilitar Grupos de Disponibilidade AlwaysOn](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665520.gif)

1. Em seguida, você pode alterar a conta de serviço do SQL Server. Clique na guia **Logon** e digite **CORP\\SQLSvc1** (para **ContosoSQL1**) ou **CORP\\SQLSvc2** (para **ContosoSQL2**) em **Nome da Conta**, em seguida, insira e confirme a senha e clique em **OK**.

1. Na janela pop-up, clique em **Sim** para reiniciar o serviço do SQL Server. Depois que o serviço do SQL Server for reiniciado, as alterações feitas na janela de propriedades entrarão em vigor.

1. Faça logoff das VMs.

## Criar o Grupo de Disponibilidade

Agora você está pronto para configurar um grupo de disponibilidade. Abaixo está uma descrição do que você deve fazer:

- Crie um novo banco de dados (**MyDB1**) em **ContosoSQL1**

- Faça um backup completo e um backup de log de transações do banco de dados

- Restaure os backups completo e de log para **ContosoSQL2** com a opção **NORECOVERY**

- Crie o grupo de disponibilidade (**AG1**) com confirmação síncrona, failover automático e réplicas secundárias legíveis

### Crie o banco de dados MyDB1 no ContosoSQL1:

1. Se você ainda não estiver saído das sessões de área de trabalho remota para **ContosoSQL1** e **ContosoSQL2**, faça isso agora.

1. Inicie o arquivo RDP para **ContosoSQL1** e faça logon como **CORP\\Install**.

1. No **Gerenciador de Arquivos**, em **C:**, crie um diretório chamado **backup**. Você usará esse uso de diretório para fazer backup e restaurar o banco de dados.

1. Clique com o botão direito do mouse no novo diretório, aponte o mouse para **Compartilhar com** e clique em **Pessoas específicas**, conforme mostrado abaixo.

	![Criar uma pasta de Backup](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665521.gif)

1. Adicione **CORP\\SQLSvc1** e conceda a ele a permissão de **Leitura/Gravação** e, em seguida, adicione **CORP\\SQLSvc2** e conceda a ele a permissão de **Leitura**, conforme mostrado abaixo, e clique **Compartilhar**. Quando o processo de compartilhamento de arquivos for concluído, clique em **Concluído**.

	![Conceder permissões para a pasta de Backup](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665522.gif)

1. Em seguida, crie o banco de dados. No menu **Iniciar**, abra o **SQL Server Management Studio** e clique em **Conectar** para se conectar à instância padrão do SQL Server.

1. No **Pesquisador de Objetos**, clique com o botão direito do mouse em **Bancos de Dados** e em **Novo Banco de Dados**.

1. Em **Nome do banco de dados**, digite **MyDB1**, em seguida, clique em **OK**.

### Faça um backup completo de MyDB1 e restaure-o no ContosoSQL2:

1. Em seguida, faça um backup completo do banco de dados. No **Pesquisador de Objetos**, expanda **Bancos de Dados** e clique com o botão direito do mouse em **MyDB1**, em seguida aponte o mouse para **Tarefas** e clique em **Fazer Backup**.

1. Na seção **Origem**, mantenha o **Tipo de backup** definido como **Completo**. Na seção **Destino**, clique em **Remover** para remover o caminho de arquivo padrão para o arquivo de backup.

1. Na seção **Destino**, clique em **Adicionar**.

1. Na caixa de texto **Nome do arquivo**, digite **\\ContosoSQL1\\backup\\MyDB1.bak**. Em seguida, clique em **OK** e em **OK** novamente para fazer backup do banco de dados. Quando a operação de backup for concluída, clique em **OK** novamente para fechar a caixa de diálogo.

1. Em seguida, faça um backup do log de transações do banco de dados. No **Pesquisador de Objetos**, expanda **Bancos de Dados** e clique com o botão direito do mouse em **MyDB1**, em seguida aponte o mouse para **Tarefas** e clique em **Fazer Backup**.

1. No tipo **Backup**, selecione **Log de Transações**. Mantenha o caminho do arquivo de **Destino** definido para aquele especificado anteriormente e clique em **OK**. Quando a operação de backup for concluída, clique em **OK** novamente.

1. Em seguida, restaure os backups completo e de log de transações em **ContosoSQL2**. Inicie o arquivo RDP para **ContosoSQL2** e faça logon como **CORP\\Install**. Deixe a sessão de área de trabalho remota para **ContosoSQL1** aberta.

1. No menu **Iniciar**, abra o **SQL Server Management Studio** e clique em **Conectar** para se conectar à instância padrão do SQL Server.

1. No **Pesquisador de Objetos**, clique com o botão direito do mouse em **Bancos de Dados** e em **Restaurar Banco de Dados**.

1. Na seção **Origem**, selecione **Dispositivo** e clique no botão **...**.

1. Em **Selecionar dispositivos de backup**, clique em **Adicionar**.

1. No local do arquivo de Backup, digite \\ContosoSQL1\\backup, clique em Atualizar, selecione MyDB1.bak, clique em OK e, em seguida, clique em OK novamente. Agora você deverá ver o backup completo e o backup de log no painel Conjuntos de backup a serem restaurados.

1. Vá para a página de Opções, selecione RESTORE WITH NORECOVERY em Estado de recuperação e, em seguida, clique em OK para restaurar o banco de dados. Quando a operação de restauração for concluída, clique em OK.

### Crie o Grupo de Disponibilidade:

1. Volte para a sessão de área de trabalho remota para **ContosoSQL1**. No **Pesquisador de Objetos** no SSMS, clique com o botão direito do mouse em **Alta Disponibilidade AlwaysOn** e clique em **Assistente de Novo Grupo de Disponibilidade**, conforme mostrado abaixo.

	![Inicie o Assistente de Novo Grupo de Disponibilidade](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665523.gif)

1. Na página **Introdução**, clique em **Avançar**. Na página **Especificar Nome do Grupo de Disponibilidade**, digite **AG1** em **Nome do grupo de disponibilidade** e clique em **Avançar** novamente.

	![Novo assistente de AG, especifique o nome do AG](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665524.gif)

1. Na página **Selecionar Bancos de Dados**, selecione **MyDB1** e clique em **Avançar**. Esse banco de dados atende aos pré-requisitos para um grupo de disponibilidade, pois você fez pelo menos um backup completo na réplica primária pretendida.

	![Novo assistente AG, selecione os bancos de dados](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665525.gif)

1. Na página **Especificar Réplicas**, clique em **Adicionar Réplica**.

	![Novo assistente AG, especifique as réplicas](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665526.gif)

1. A caixa de diálogo **Conectar ao Servidor** é aberta. Digite **ContosoSQL2** em **Nome do servidor** e clique em **Conectar**.

	![Novo assistente AG, conecte ao servidor](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665527.gif)

1. De volta à página **Especificar Réplicas**, você verá agora **ContosoSQL2** listado em **Réplicas Disponíveis**. Configure as réplicas conforme mostrado abaixo. Quando tiver terminado, clique em **Avançar**.

	![Novo assistente de AG, Especificar Réplicas (Completo)](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665528.gif)

1. Na página **Selecionar Sincronização de Dados Inicial**, selecione **Somente ingressar** e clique em **Avançar**. Você já executou a sincronização de dados manualmente quando obteve os backups completos e de transações em **ContosoSQL1** e os restaurou em **ContosoSQL2**. Você pode preferir não realizar as operações de backup e restauração no seu banco de dados e selecionar **Completo** para permitir que o Assistente de Novo Grupo de Disponibilidade execute a sincronização de dados para você. No entanto, isso não é recomendado para grandes bancos de dados que se encontram em algumas empresas.

	![Novo assistente de AG, selecionar sincronização de dados inicial](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665529.gif)

1. Na página **Validação**, clique em **Avançar**. Esta página deve ser semelhante ao que é mostrado abaixo. Há um aviso para a configuração de ouvinte porque você não configurou um ouvinte de grupo de disponibilidade. Você pode ignorar esse aviso, pois este tutorial não configura um ouvinte. Para configurar o ouvinte após a conclusão deste tutorial, consulte [Configurar um ouvinte de ILB para Grupos de Disponibilidade AlwaysOn no Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

	![Novo assistente AG, Validação](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665530.gif)

1. Na página **Resumo**, clique em **Concluir** e aguarde enquanto o assistente configura o novo grupo de disponibilidade. Na página **Progresso**, você pode clicar em **Mais detalhes** para exibir o progresso detalhado. Quando o assistente for concluído, inspecione a página **Resultados** para verificar se o grupo de disponibilidade foi criado com êxito, conforme mostrado abaixo, e clique em **Fechar** para sair do assistente.

	![Novo assistente de AG, Resultados](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665531.gif)

1. No **Pesquisador de Objetos**, expanda **Alta Disponibilidade AlwaysOn** e expanda **Grupos de Disponibilidade**. Agora você poderá ver o novo grupo de disponibilidade neste contêiner. Clique com o botão direito do mouse em **AG1 (Primário)** e clique em **Mostrar Painel**.

	![Mostrar Painel de AG](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665532.gif)

1. O **Painel AlwaysOn** deve ser semelhante ao mostrado abaixo. Você pode ver as réplicas, o modo de failover de cada réplica e o estado de sincronização.

	![Painel de AG](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665533.gif)

1. Retorne ao **Gerenciador do Servidor**, selecione **Ferramentas** e inicie o **Gerenciador de Cluster de Failover**.

1. Expanda **Cluster1.corp.contoso.com** e expanda **Serviços e Aplicativos**. Selecione **Funções** e observe se a função do grupo de disponibilidade **AG1** foi criada. Observe que o AG1 não tem nenhum endereço IP pelo qual os clientes do banco de dados podem se conectar ao grupo de disponibilidade porque você não configurou um ouvinte. Você pode se conectar diretamente ao nó principal para operações de leitura/gravação e o nó secundário para consultas somente leitura.

	![AG no Gerenciador de Cluster de Failover](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665534.gif)

>[AZURE.WARNING]Não tente fazer failover do grupo de disponibilidade no Gerenciador de Cluster de Failover. Todas as operações de failover devem ser executadas no **Painel AlwaysOn** no SSMS. Para obter mais informações, consulte [Restrições do uso do Gerenciador de Cluster de Failover WSFC com Grupos de Disponibilidade](https://msdn.microsoft.com/library/ff929171.aspx).

## Próximas etapas
Agora você implementou com êxito o SQL Server AlwaysOn criando um grupo de disponibilidade no Azure. Para configurar um ouvinte para este grupo de disponibilidade, consulte [Configurar um ouvinte ILB para Grupos de Disponibilidade AlwaysOn no Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

Para obter outras informações sobre como usar o SQL Server no Azure, consulte [SQL Server em Máquinas Virtuais do Azure](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=September15_HO1-->