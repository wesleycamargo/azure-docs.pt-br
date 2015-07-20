<properties 
   pageTitle="Vários VIPs por serviço de nuvem"
   description="Visão geral de vários VIPs e como definir vários VIPs em um serviço de nuvem"
   services="load-balancer"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/01/2015"
   ms.author="telmos" />

# Vários VIPs por serviço de nuvem
Você pode acessar os serviços de nuvem do Azure pela Internet pública usando um endereço IP fornecido pelo Azure. Este endereço IP público é conhecido como um VIP (IP virtual), uma vez que está vinculado ao balanceador de carga do Azure, e não realmente às instâncias VM no serviço de nuvem. Você pode acessar qualquer instância VM dentro de um serviço de nuvem usando um único VIP.

No entanto, há situações em que você terá mais de um VIP como ponto de entrada para o mesmo serviço de nuvem. Por exemplo, o serviço de nuvem pode hospedar vários sites que exigem conectividade SSL usando a porta SLSL padrão de 443, cada site sendo hospedado para um cliente diferente ou locatário. Nesse cenário, você precisa ter outro endereço IP público para cada site. O diagrama a seguir mostra uma típica hospedagem multilocatário na Web, com a necessidade de vários certificados SSL na mesma porta pública.

![Cenário SSL de vários VIPs](./media/load-balancer-multivip/Figure1.png)

No cenário anterior, todos os VIPs usam a mesma porta pública (443) e o tráfego é redirecionado para um ou VMs de balanceamento de carga em uma porta privada exclusiva para o endereço IP interno do serviço de nuvem que hospeda todos os sites.

>[AZURE.NOTE]Outro cenário para o uso de vários VIPs hospeda vários ouvintes do Grupo de Disponibilidade do AlwaysOn do SQL no mesmo conjunto de máquinas virtuais.

VIPs são dinâmicos por padrão, o que significa que o endereço IP real atribuído ao serviço de nuvem pode mudar ao longo do tempo. Para evitar isso, você pode reservar um VIP para o serviço. Para saber mais sobre VIPs reservados, consulte [IP público reservado](../virtual-networks-reserved-public-ip).

>[AZURE.NOTE]Consulte [Preços de endereço IP](http://azure.microsoft.com/pricing/details/ip-addresses/) para obter informações sobre preços em VIPs e IPs reservados.

Você pode usar o PowerShell para verificar os VIPs usados por seus serviços de nuvem, bem como adicionar e remover VIPs, associar um VIP para um ponto de extremidade e configurar o balanceamento de carga em um VIP específico.

## Limitações

Neste momento, a funcionalidade de VIP multi está limitada aos seguintes cenários:

- **IaaS apenas**. Só é possível habilitar o VIP multi para serviços de nuvem que contêm máquinas virtuais. Você não pode usar o VIP multi em cenários de PaaS, com instâncias de função.
- **PowerShell apenas**. Você só pode gerenciar o VIP multi usando o PowerShell.
- **Nenhum suporte ARM**. Não há nenhum suporte para VIP multi no Gerenciador de Recursos do Azure.

>[AZURE.IMPORTANT]Essas limitações são temporárias e podem ser alteradas a qualquer momento. Certifique-se de visitar novamente esta página para verificar as alterações futuras.


## Como adicionar um VIP a um serviço de nuvem
Para adicionar um VIP ao seu serviço, execute o seguinte comando do PowerShell:

    Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

O comando acima exibirá um resultado semelhante ao exemplo a seguir:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## Como remover um VIP de um serviço de nuvem
Para remover o VIP adicionado ao serviço no exemplo acima, execute o seguinte comando PowerShell:

    Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

>[AZURE.IMPORTANT]Você só pode remover um VIP, se ele não tiver pontos de extremidade associados a ele.

## Como recuperar informações de VIP de um serviço de nuvem
Para recuperar VIPs associados a um serviço de nuvem, execute o seguinte script do PowerShell:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

O script acima exibirá um resultado semelhante ao exemplo a seguir:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

Neste exemplo, o serviço de nuvem tem 3 VIPs:

- **Vip1** é o VIP padrão e você sabe disso porque o valor IsDnsProgrammedName está definido como verdadeiro.
- **Vip2** e **Vip3** não são usados porque não têm endereços IP. Eles só serão usados se você associar um ponto de extremidade ao VIP.

>[AZURE.NOTE]Sua assinatura só será cobrada por VIPs extras quando eles estiverem associados a um ponto de extremidade. Para obter mais informações sobre preços, consulte [Preços de endereço IP](http://azure.microsoft.com/pricing/details/ip-addresses/).

## Como associar um VIP a um ponto de extremidade
Para associar um VIP em um serviço de nuvem a um ponto de extremidade, execute o seguinte comando PowerShell:

    Get-AzureVM -ServiceName myService -Name myVM1 `
    | Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 `
    | Update-AzureVM

O comando anterior cria um ponto de extremidade vinculado ao VIP denominado *Vip2* na porta *80*, e o vincula à VM denominada *myVM1* em um serviço de nuvem chamado *myService* usando *TCP* na porta *8080*.

Para verificar a configuração, execute o seguinte comando PowerShell:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

E a saída será semelhante aos resultados abaixo:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## Como habilitar balanceamento de carga em um VIP específico
Você pode associar um único VIP com várias máquinas virtuais para fins de balanceamento de carga. Por exemplo, suponha que você tenha um serviço de nuvem chamado *myService*, e duas máquinas virtuais *myVM1* e *myVM2*. E o serviço de nuvem tem vários VIPs, um deles chamado *Vip2*. Se você quiser garantir que todo o tráfego para a porta *81* no *Vip2* seja equilibrado entre *myVM1* e *myVM2* na porta *8181*, execute o seguinte script do PowerShell:

    Get-AzureVM -ServiceName myService -Name myVM1 `
    | Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
    | Update-AzureVM

    Get-AzureVM -ServiceName myService -Name myVM2 `
    | Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
    | Update-AzureVM

Você também pode atualizar o balanceador de carga para usar um VIP diferente. Por exemplo, se você executar o comando PowerShell a seguir, alterará a configuração do balanceamento de carga para usar um VIP denominado Vip1:

    Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1

## Consulte também

[Visão geral do balanceador de carga para a Internet](load-balancer-internet-overview.md)

[Introdução ao balanceador de carga para a Internet](load-balancer-internet-getstarted.md)

[Visão geral da Rede Virtual](https://msdn.microsoft.com/library/azure/jj156007.aspx)

[APIs REST com IP Reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 

<!---HONumber=July15_HO2-->