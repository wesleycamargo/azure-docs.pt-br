<properties 
	pageTitle="Configurar pontos de extremidade em uma máquina virtual no Azure" 
	description="Saiba como configurar pontos de extremidade no portal clássico para permitir a comunicação com uma máquina virtual no Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="kathydav"/>

#Como instalar pontos de extremidade em uma máquina virtual

Todas as máquinas virtuais criadas no Azure podem se comunicar automaticamente com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual usando um canal de rede privada. No entanto, os computadores na Internet ou outras redes virtuais requerem pontos de extremidade para direcionar o tráfego de rede de entrada para uma máquina virtual.

Quando você cria uma máquina virtual no Portal de Gerenciamento, pontos de extremidade para a Área de Trabalho Remota, Comunicação Remota do Windows PowerShell e Secure Shell (SSH) são criados automaticamente. Você pode configurar pontos de extremidade adicionais criando a máquina virtual ou posteriormente, conforme a necessidade.

Cada ponto de extremidade tem uma porta pública e uma privada:

- A porta pública é usada pelo balanceador de carga do Azure para ouvir o tráfego de entrada da Internet com destino à máquina virtual. 
- A porta privada é usada pela máquina virtual para ouvir o tráfego de entrada normalmente com destino a um aplicativo ou serviço em execução na máquina virtual.

Valores padrão para o protocolo IP e as portas TCP ou UDP para protocolos conhecidos são fornecidos quando você cria pontos de extremidade com o Portal de Gerenciamento. Para pontos de extremidade personalizados, você deve especificar o protocolo IP correto (TCP ou UDP) e as portas públicas e privadas. Para distribuir o tráfego de entrada aleatoriamente entre várias máquinas virtuais, você deve criar um conjunto com balanceamento de carga composto de vários pontos de extremidade.

Depois de criar um ponto de extremidade, você pode usar uma lista de controle de acesso (ACL) para definir regras que permitam ou neguem o tráfego de entrada na porta pública do ponto de extremidade com base em seu endereço IP de origem. No entanto, se a máquina virtual estiver em uma rede virtual do Azure, você deverá usar Grupos de Segurança de Rede. Para obter mais informações, consulte [Sobre grupos de segurança de rede](https://msdn.microsoft.com/library/azure/dn848316.aspx).

**Importante**: a configuração do firewall para máquinas virtuais do Azure é feita automaticamente para as portas associadas com a Área de Trabalho Remota e SSH (Secure Shell), e, na maioria dos casos, para Comunicação Remota do Windows PowerShell. Para portas especificadas para todos os outros pontos de extremidade, nenhuma configuração é feita automaticamente para o firewall da máquina virtual. Ao criar um ponto de extremidade para a máquina virtual, você precisará garantir que o firewall da máquina virtual também permita tráfego para o protocolo e a porta privada correspondente à configuração do ponto de extremidade.

##Criar um ponto de extremidade

1.	Se você ainda não fez isso, entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).
2.	Clique em **Máquinas Virtuais** e, em seguida, clique no nome da máquina virtual que você deseja configurar.
3.	Clique em **Pontos de Extremidade**. A página Pontos de Extremidade lista todos os pontos de extremidade atuais para a máquina virtual.

	![Pontos de extremidade](./media/virtual-machines-set-up-endpoints/endpointswindows.png)
 
4.	Na barra de tarefas, clique em **Adicionar**.
5.	Na página **Adicionar um ponto de extremidade a uma máquina virtual**, escolha o tipo de ponto de extremidade. 

	- Se você estiver criando um novo ponto de extremidade que não faz parte de um conjunto com balanceamento de carga ou que seja o primeiro membro de um novo conjunto com balanceamento de carga, escolha **Adicionar um ponto de extremidade autônomo** e clique na seta para a esquerda.
	- Caso contrário, escolha **Adicionar um ponto de extremidade a um conjunto com balanceamento de carga existente**, selecione o nome do conjunto com balanceamento de carga e clique na seta para a esquerda. Na página **Especificar os detalhes do ponto de extremidade**, digite um nome para o ponto de extremidade em **Nome** e clique na marca de seleção para criar o ponto de extremidade.

6.	Na página **Especificar os detalhes do ponto de extremidade**, digite um nome para o ponto de extremidade em **Nome**. Você também pode escolher um nome de protocolo de rede na lista, que preencherá os valores iniciais para **Protocolo**, **Porta Pública** e **Porta Privada**.
7.	Para um ponto de extremidade personalizado, em **Protocolo**, escolha **TCP** ou **UDP**.
8.	Para portas personalizadas, em **Porta Pública**, digite o número de porta para o tráfego de entrada da Internet. Em **Porta Privada**, digite o número da porta em que a máquina virtual está escutando. O número de porta pode ser diferente. Certifique-se de que o firewall na máquina virtual tenha sido configurado para permitir o tráfego correspondente para o protocolo (na etapa 7) e a porta privada.
9.	Se esse ponto de extremidade for o primeiro de um conjunto com balanceamento de carga, clique em **Criar um conjunto com balanceamento de carga** e clique na seta para a direita. Na página **Configurar o conjunto com balanceamento de carga**, especifique um nome de conjunto com balanceamento de carga, um protocolo de investigação e uma porta e o número de sondas enviadas. O balanceador de carga do Azure envia sondas para as máquinas virtuais em um conjunto com balanceamento de carga para monitorar sua disponibilidade. O balanceador de carga do Azure não encaminhará tráfego para as máquinas virtuais que não responderem à investigação. Clique na seta à direita.
10.	Clique na marca de seleção para salvar o ponto de extremidade.

Agora, você verá o ponto de extremidade listado o pontos de extremidade página.

![Criação de ponto de extremidade com êxito](./media/virtual-machines-set-up-endpoints/endpointwindowsnew.png)
 
Para usar um cmdlet do PowerShell do Azure e configurar isso, confira [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

##Gerenciar a ACL em um ponto de extremidade

Para definir o conjunto de computadores que pode enviar tráfego, a ACL em um ponto de extremidade pode restringir o tráfego com base no endereço IP de origem. Siga estas etapas para adicionar, modificar ou remover uma ACL em uma empresa.

> [AZURE.NOTE]se o ponto de extremidade é parte de um conjunto com balanceamento de carga, quaisquer alterações feitas na ACL em um ponto de extremidade são aplicadas a todos os pontos de extremidade no conjunto.

Se a máquina virtual estiver em uma rede virtual do Azure, você deverá usar Grupos de Segurança de Rede. Para obter detalhes, confira [Sobre grupos de segurança de rede](https://msdn.microsoft.com/library/azure/dn848316.aspx).


1.	Se você ainda não tiver feito isso, entre no Portal de Gerenciamento do Azure.
2.	Clique em **Máquinas Virtuais** e, em seguida, clique no nome da máquina virtual que você deseja configurar.
3.	Clique em **Pontos de Extremidade**. A página de pontos de extremidade lista todos os pontos de extremidade para a máquina virtual.

    ![Lista ACL](./media/virtual-machines-set-up-endpoints/EndpointsShowsDefaultEndpointsForVM.png)
 
4.	Selecione o ponto de extremidade apropriado na lista.
5.	Na barra de tarefas, clique em** Gerenciar ACL**. O **detalhes especificar ACL** caixa de diálogo aparece.

    ![Especifique os detalhes da ACL](./media/virtual-machines-set-up-endpoints/EndpointACLdetails.png)
 
6.	Use linhas na lista para adicionar, excluir ou editar regras de uma ACL e alterar sua ordem. O valor de **Sub-rede Remota** é um intervalo de endereços IP para o tráfego de entrada da Internet que o balanceador de carga do Azure permitirá ou negará com base no endereço IP de origem do tráfego. Você deve especificar o intervalo de endereços IP no formato CIDR, também conhecido como formato de prefixo de endereço. Um exemplo é 131.107.0.0/16.

Você pode usar regras para permitir somente o tráfego de computadores específicos correspondentes aos seus computadores na Internet ou negar o tráfego de intervalos de endereços específicos e conhecidos.

As regras são avaliadas em ordem, começando com a primeira regra e terminando com a última regra. Isto significa que as regras devem ser ordenadas das menos restritivas para as mais restritivas. Para obter exemplos e mais informações, consulte [Sobre listas de controle de acesso de rede](http://go.microsoft.com/fwlink/p/?linkid=303816).

Para usar um cmdlet do PowerShell do Azure para configurar isso, confira [Gerenciando Listas de Controle de Acesso (ACLs) para pontos de extremidade usando o PowerShell](https://msdn.microsoft.com/library/azure/dn376543.aspx).

## Recursos adicionais

[Balanceamento de carga para Serviços de infraestrutura do Azure](virtual-machines-load-balance.md)

[Sobre listas de controle de acesso a redes](http://go.microsoft.com/fwlink/p/?linkid=303816)

[Sobre grupos de segurança de rede](https://msdn.microsoft.com/library/azure/dn848316.aspx)

<!---HONumber=58-->