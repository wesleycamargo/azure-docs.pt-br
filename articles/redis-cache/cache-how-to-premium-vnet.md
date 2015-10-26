<properties 
	pageTitle="Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium" 
	description="Saiba como criar e gerenciar o suporte de Rede Virtual para as instâncias da camada Premium do Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/01/2015" 
	ms.author="sdanie"/>

# Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium
O Cache Redis do Azure apresenta diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho e dos recursos do cache, incluindo a nova camada Premium, atualmente em visualização.

A camada premium do Cache Redis do Azure inclui clustering, persistência e suporte de VNET (rede virtual). Uma VNET é uma representação da sua própria rede na nuvem. Quando uma instância do Cache Redis do Azure é configurada com uma VNET, ela não é publicamente endereçável e pode ser acessada apenas de clientes na VNET. Este artigo descreve como configurar o Suporte de Rede Virtual para uma instância premium do Cache Redis do Azure.

Para obter informações sobre outros recursos de cache premium, veja [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md) e [Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md).

>[AZURE.NOTE]A camada Premium do Cache Redis do Azure está atualmente em visualização.

## Por que a VNET?
A implantação de [VNET (Rede Virtual) do Azure](https://azure.microsoft.com/pt-BR/services/virtual-network/) fornece isolamento e segurança avançados para o Cache Redis do Azure, bem como sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso ao Cache Redis do Azure.

## Suporte de rede virtual
O suporte de VNET (Rede Virtual) é configurado na folha **Novo Cache Redis** durante a criação do cache. Para criar um cache, entre no [portal de visualização do Azure](https://portal.azure.com) e clique em **Novo**->**Dados + Armazenamento**>**Cache Redis**.

![Criar um Cache Redis][redis-cache-new-cache-menu]

Para configurar o suporte de VNET, primeiro selecione um dos caches **Premium** na folha **Escolha o Tipo de preço**.

![Escolha sua camada de preço][redis-cache-premium-pricing-tier]

A integração da VNET e do Cache Redis do Azure é configurada na folha **Rede Virtual**. Aqui, você pode selecionar uma VNET clássica existente. Para usar uma nova VNET, siga as etapas em [Criar uma rede virtual (clássica) usando o portal de visualização do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e volte para a folha **Rede Virtual do Cache Redis** para selecioná-la.

>[AZURE.NOTE]Durante o período de visualização do cache premium, o Cache Redis do Azure funciona com VNETs clássicas. Para obter informações sobre como criar uma VNET clássica, veja [Criar uma rede virtual (clássica) usando o portal de visualização do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

![Rede virtual][redis-cache-vnet]

Clique em **Rede Virtual** na folha **Rede Virtual** para selecionar e configurar sua VNET.

![Rede virtual][redis-cache-vnet-select]

Clique na VNET desejada para selecioná-la.

![Rede virtual][redis-cache-vnet-subnet]

Clique em Sub-rede para selecionar a sub-rede desejada.

![Rede virtual][redis-cache-vnet-ip]

Digite o **Endereço IP estático** desejado e clique em **OK** para salvar a configuração de VNET. Se o IP estático selecionado já estiver sendo usado, uma mensagem de erro será exibida.

Depois que o cache é criado, ele pode ser acessado apenas pelos clientes na mesma VNET.

## Perguntas frequentes sobre a VNET do Cache Redis do Azure

A lista a seguir contém respostas para perguntas frequentes sobre a escala do Cache Redis do Azure.

## Quais são alguns problemas comuns de configuração incorreta no Cache Redis do Azure e nas VNETs?

A lista a seguir contém alguns erros comuns de configuração que podem impedir que o Cache Redis do Azure funcione corretamente.

-	Bloqueio das portas TCP usadas pelos clientes para conexão ao Redis, ou seja, 6379 ou 6380.
-	Bloqueio ou interceptação do tráfego HTTPS de saída da rede virtual. O Cache Redis do Azure usa o tráfego HTTPS de saída para os serviços do Azure, especialmente Armazenamento.
-	Bloqueio da comunicação mútua das VMs de instância de função do Redis na sub-rede. As instâncias de função do Redis devem ter permissão para se comunicar entre si usando TCP em qualquer uma das portas usadas, que podem estar sujeitas à alteração, mas, no mínimo, pode-se considerar como todas as portas usadas no arquivo CSDEF do Redis.
-	Conexão bloqueada do Balanceador de Carga do Azure com as VMs do Redis na porta TCP/HTTP 16001. O Cache Redis do Azure depende da investigação do balanceador de carga padrão do Azure para determinar quais instâncias de função estão ativas. A investigação padrão do balanceador de carga funciona com a execução de ping do Agente Convidado do Azure na porta 16001. Somente as instâncias de função que respondem ao ping serão colocadas em rotação para receber o tráfego encaminhado pelo ILB. Quando nenhuma instância estiver em rotação devido à falha do ping pelo bloqueio das portas, o ILB não aceitará conexões TCP de entrada.
-	Bloqueio do tráfego da Web do aplicativo cliente usado para a validação da chave pública do SSL. Os clientes do Redis (na rede virtual) devem conseguir realizar o tráfego HTTP para a Internet pública para baixar certificados da AC e listas de certificados revogados, a fim de realizar a validação do certificado SSL ao usar a porta 6380 para se conectar ao Redis e fazer autenticação do servidor SSL.
-	Conexão bloqueada do Balanceador de Carga do Azure com as VMs do Redis em um cluster por meio de TCP na porta 1300x (13000, 13001, etc.) ou 1500x (15000, 15001, etc.). As VNets são configuradas no arquivo csdef com uma investigação do balanceador de carga para abrir essas portas. O balanceador de carga do Azure precisa ser permitido pelos NSGs, e os NSGs padrão fazem isso com a marca AZURE\_LOADBALANCER. O balanceador de carga do Azure tem um único endereço IP estático 168.63.126.16. Para saber mais, consulte [O que é um NSG (Grupo de Segurança de Rede)?](../virtual-network/virtual-networks-nsg.md).

## Posso usar VNETs com um cache básico ou standard?

As VNETs podem ser usadas apenas com os caches premium.

## Próximas etapas
Aprenda a usar mais recursos de cache premium.

-	[Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md)
-	[Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md)





  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-vnet/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-vnet/redis-cache-premium-pricing-tier.png

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-select]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-select.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-subnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-subnet.png

<!---HONumber=Oct15_HO3-->