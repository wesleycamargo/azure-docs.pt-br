---
title: "Gerenciar endereços IP reservados (Clássico) usando o PowerShell | Microsoft Docs"
description: "Entender os endereços IP reservados (Clássico) e como gerenciá-los usando o PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: c934f78e514230958fad8b2aa9be4d2e56a3a835
ms.openlocfilehash: 55071ffe1bcc17b2181b4c52f51d28571a2f0eff


---
# <a name="reserved-ip-addresses-classic"></a>Endereços IP reservados (Clássico)

> [!div class="op_single_selector"]
- [portal do Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
- [CLI do Azure](virtual-network-deploy-static-pip-arm-cli.md)
- [Modelo](virtual-network-deploy-static-pip-arm-template.md)
- [PowerShell (Clássico)](virtual-networks-reserved-public-ip.md)

Os endereços IP no Azure recaem em duas categorias: dinâmicos e reservados. Os endereços IP públicos gerenciados pelo Azure são dinâmicos por padrão. Isso significa que o endereço IP usado para um determinado serviço de nuvem (VIP) ou para acessar uma VM ou instância de função diretamente (ILPIP) pode mudar de tempos em tempos, quando recursos forem desligados ou desalocados.

Para evitar que endereços IP sejam alterados, é possível reservar um endereço IP. Os IPs reservados podem ser usados apenas como um VIP, garantindo que o endereço IP do serviço de nuvem permaneça o mesmo, mesmo se os recursos forem desligados ou desalocados. Além disso, você pode converter IPs dinâmicos existentes usados como um VIP para um endereço IP reservado.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Saiba como reservar um endereço IP estático público usando o [Modelo de implantação do Gerenciador de Recursos](virtual-network-ip-addresses-overview-arm.md).

Para saber mais sobre endereços IP no Azure, leia o artigo [Endereços IP](virtual-network-ip-addresses-overview-classic.md).

## <a name="when-do-i-need-a-reserved-ip"></a>Quando eu precisarei de um IP reservado?
* **Você deseja garantir que o IP seja reservado em sua assinatura**. Se você quiser reservar um endereço IP que não será liberado da sua assinatura sob nenhuma circunstância, deverá usar um IP reservado público.  
* **Você deseja que o IP permaneça com seu serviço de nuvem, mesmo nos estados parados ou desalocados (VMs)**. Se você quiser que seu serviço seja acessado usando um endereço IP que não será alterado mesmo quando VMs do serviço de nuvem estejam paradas ou desalocadas.
* **Você deseja garantir que o tráfego de saída do Azure use um endereço IP previsível**. Você pode ter seu firewall local configurado para permitir apenas o tráfego de endereços IP específicos. Ao reservar um IP, você conhecerá o endereço IP de origem e não terá de atualizar suas regras de firewall devido a uma alteração de IP.

## <a name="faq"></a>Perguntas frequentes
1. Posso usar um IP reservado para todos os serviços do Azure?  
   * Os IPs reservados só podem ser usados para VMs e funções de instância de serviço de nuvem exposto através de um VIP.
2. Quantos IPs reservados eu posso ter?  
   * Consulte o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).
3. Há uma cobrança para IPs reservados?
   * Veja [Detalhes sobre preços de endereços IP reservados](http://go.microsoft.com/fwlink/?LinkID=398482) para obter informações sobre preços.
4. Como eu reservo um endereço IP?
   * É possível usar o PowerShell ou a [API REST de Gerenciamento do Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx) ou o [portal do Azure](https://portal.azure.com) para reservar um endereço IP em uma região específica. Esse endereço IP reservado é associado à sua assinatura.
5. Posso usar isso com redes virtuais baseadas em grupos de afinidade?
   * Os IPs reservados têm suporte apenas em redes virtuais regionais. Eles não têm suporte para redes virtuais associadas a grupos de afinidade. Para obter mais informações sobre a associação de uma rede virtual a uma região ou a um grupo de afinidade, veja [Sobre redes virtuais regionais e grupos de afinidade](virtual-networks-migrate-to-regional-vnet.md).

## <a name="manage-reserved-vips"></a>Gerenciar VIPs reservados

Certifique-se de ter instalado e configurado o Azure PowerShell seguindo as etapas do artigo [Instalar e configurar o PowerShell](/powershell/azureps-cmdlets-docs). 

Antes de poder usar IPs reservados, você deverá adicioná-los à sua assinatura. Para criar um IP reservado do pool de endereços IP públicos disponíveis no local *EUA Central* , execute o seguinte comando:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
```

Observe, entretanto, que você não pode especificar qual IP está sendo reservado. Para exibir quais endereços IP estão reservados em sua assinatura, execute o seguinte comando do PowerShell e observe os valores de *ReservedIPName* e *Endereço*:

```powershell
Get-AzureReservedIP
```

Saída esperada:

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

Depois que um IP for reservado, ele permanecerá associado à sua assinatura até você excluí-lo. Para excluir o IP reservado mostrado acima, execute o seguinte comando do PowerShell:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Reservar o endereço IP de um serviço de nuvem existente
É possível reservar o endereço IP de um serviço de nuvem existente adicionando o parâmetro `-ServiceName`. Para reservar o endereço IP de um serviço de nuvem *TestService* no local *EUA Central*, execute o seguinte comando do PowerShell:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Associar um IP reservado a um novo serviço de nuvem
O script a seguir cria um novo IP reservado e o associa a um novo serviço de nuvem chamado *TestService*.

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```

> [!NOTE]
> Quando você cria um IP reservado a ser usado com um serviço de nuvem, você ainda precisará fazer referência à VM usando *VIP:&lt;número de porta>* para comunicação de entrada. Reservar um IP não significa que você pode conectar-se à VM diretamente. O IP reservado é atribuído ao serviço de nuvem no qual a VM foi implantada. Se você quiser se conectar diretamente a uma VM por IP, precisará configurar um IP público em nível de instância. Um IP público em nível de instância é um tipo de IP público (chamado de ILPIP) atribuído diretamente à sua VM. Ele não pode ser reservado. Veja [IP Público em Nível de Instância (ILPIP)](virtual-networks-instance-level-public-ip.md) para obter mais informações.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Remover um IP reservado de uma implantação em execução
Para remover o IP reservado adicionado ao novo serviço criado no script acima, execute o seguinte comando do PowerShell:

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

> [!NOTE]
> Removendo um IP reservado de uma implantação em execução não remove a reserva de sua assinatura. Ele simplesmente libera o IP para ser usado por outro recurso em sua assinatura.
> 

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Associar um IP reservado a uma implantação em execução
Os comandos a seguir criam um novo serviço de nuvem chamado *TestService2* com uma nova VM denominada *TestVM2*, e então associa o IP reservado existente chamado *MyReservedIP* ao serviço de nuvem:

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço
Você também pode associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço (CSCFG). O xml de exemplo abaixo mostra como configurar um serviço de nuvem para usar um VIP reservado denominado *MyReservedIP*:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Próximas etapas
* Entenda como o [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo de implantação clássica.
* Saiba mais sobre [endereços IP privados reservados](virtual-networks-reserved-private-ip.md).
* Saiba mais sobre [endereços ILPIP (IP Público de Nível de Instância)](virtual-networks-instance-level-public-ip.md).




<!--HONumber=Jan17_HO1-->


