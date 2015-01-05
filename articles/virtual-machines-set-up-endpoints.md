<properties urlDisplayName="Set up endpoints" pageTitle="Configurar pontos de extremidade em uma máquina virtual no Azure" metaKeywords="Azure config setup, configuring vm connection" description="Learn how to setup communication with a virtual machine in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/29/2014" ms.author="kathydav" />

#Como instalar pontos de extremidade em uma máquina virtual

**Observação**: Se você deseja conectar-se a suas máquinas virtuais diretamente pelo nome do host ou instalar conexões entre instalações, consulte [Visão geral da rede virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

Todas as máquinas virtuais criadas no Azure podem se comunicar automaticamente com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual usando um canal de rede privada. No entanto, outros recursos na Internet ou outras redes virtuais requerem pontos de extremidade a tratar do tráfego de rede de entrada para a máquina virtual. 

Quando você cria uma máquina virtual no Portal de gerenciamento, você pode criar esses pontos de extremidade, como área de trabalho remota, Windows PowerShell Remoting ou Secure Shell (SSH). Depois de criar a máquina virtual, você pode criar mais pontos de extremidade, conforme necessário. Você também pode gerenciar o tráfego de entrada na porta pública Configurando regras para a lista de controle de acesso (ACL) rede do ponto de extremidade. Este artigo mostra como fazer ambas as tarefas.

Cada ponto de extremidade tem uma porta pública e uma privada:

- A porta privada é usada internamente pela máquina virtual para escutar o tráfego naquele ponto de extremidade.

- A porta pública é usada pelo balanceador de carga do Azure para se comunicar com a máquina virtual nos recursos externos. Depois de criar um ponto de extremidade, você pode usar a lista de controle de acesso de rede (ACL) para definir regras que ajudam a isolar e controlar o tráfego de entrada na porta pública. Para obter mais informações, consulte [Sobre listas de controle de acesso de rede](http://go.microsoft.com/fwlink/p/?LinkId=303816).

Valores padrão para as portas e o protocolo para esses pontos de extremidade são fornecidas quando os pontos de extremidade são criados por meio do Portal de gerenciamento. Para todos os outros pontos de extremidade, você especifica as portas e protocolos, quando você criar o ponto de extremidade. Os recursos podem se conectar a um ponto de extremidade usando um protocolo TCP ou UDP. O protocolo TCP inclui comunicação HTTP e HTTPS.  

**Importante**: a configuração do firewall é feita automaticamente para as portas associadas com a Área de Trabalho Remota e SSH (Secure Shell) e, na maioria dos casos, para a comunicação remota do Windows PowerShell. +Para portas especificadas para todos os outros pontos de extremidade, nenhuma configuração é feita automaticamente para o firewall no sistema operacional convidado.+Quando você cria um ponto de extremidade, você precisará configurar as portas corretas no firewall para permitir o tráfego que você pretende direcionar o ponto de extremidade.

###Criar um ponto de extremidade###

1. Se você ainda não fez isso, entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual que você deseja configurar.

3. Clique em **Pontos de Extremidade**.A página de pontos de extremidade lista todos os pontos de extremidade para a máquina virtual.

	![Endpoints](./media/virtual-machines-set-up-endpoints/endpointswindows.png)

4.	Clique em **Adicionar**.

	A caixa de diálogo **Adicionar coadministrador** é exibida. Escolha o tipo de ponto de extremidade.Se estiver criando um novo conjunto com carga balanceada, escolha um independente para criar o primeiro ponto de extremidade no conjunto.
	
5. Na **nome**, digite um nome para o ponto de extremidade.

6. No protocolo, especifique **TCP** ou **UDP**.

7. Em **Porta pública** e **Porta Privada**, digite os números de porta que você deseja que a máquina virtual use, conforme necessário. O número de porta pode ser diferente. A porta pública é o ponto de entrada para a comunicação vinda de fora do Azure e é usada pelo balanceador de carga do Azure.Você pode usar as regras de firewall e porta privada na máquina virtual para redirecionar o tráfego de uma maneira que seja apropriado ao seu aplicativo.

8. Clique em **criar um conjunto de balanceamento de carga de** se esse ponto de extremidade será a primeira de um conjunto com balanceamento de carga.  Em seguida, no **configurar o conjunto com balanceamento de carga** página, especifique um nome, protocolo e detalhes de teste.Conjuntos de balanceamento de carga exigem um teste a integridade do conjunto pode ser monitorada.Para obter mais informações, consulte [Balanceamento de carga de máquinas virtuais](http://www.windowsazure.com/pt-br/manage/windows/common-tasks/how-to-load-balance-virtual-machines/).  

9.	Clique na marca de seleção para salvar o ponto de extremidade.

	Agora, você verá o ponto de extremidade listado o **pontos de extremidade** página.

	![Endpoint creation successful](./media/virtual-machines-set-up-endpoints/endpointwindowsnew.png)

###Gerenciar a ACL em um ponto de extremidade##

Siga estas etapas para adicionar, modificar ou remover uma ACL em uma empresa.

**Observação**:se o ponto de extremidade é parte de um conjunto com balanceamento de carga, quaisquer alterações feitas na ACL em um ponto de extremidade são aplicadas a todos os pontos de extremidade no conjunto.

1. Se você ainda não fez isso, entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual que você deseja configurar.

3. Clique em **Pontos de Extremidade**.A página de pontos de extremidade lista todos os pontos de extremidade para a máquina virtual.

    ![ACL list](./media/virtual-machines-set-up-endpoints/EndpointsShowsDefaultEndpointsForVM.png)

4. Selecione o ponto de extremidade apropriado na lista. 

5. Clique em **Gerenciar ACL**.

    O **detalhes especificar ACL** caixa de diálogo aparece.

    ![Specify ACL details](./media/virtual-machines-set-up-endpoints/EndpointACLdetails.png)

6. Use linhas na lista para adicionar, excluir ou editar regras de uma ACL. O valor de sub-rede remota corresponde ao intervalo de endereços IP que você pode permitir ou negar como uma regra. As regras são avaliadas em ordem, começando com a primeira regra e terminando com a última regra. Isso significa que as regras devem ser listadas de menos restritiva a mais restritiva. Para obter exemplos e mais informações, consulte [Sobre listas de controle de acesso de rede](http://go.microsoft.com/fwlink/p/?LinkId=303816).

<!--HONumber=35.1-->
