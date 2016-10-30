<properties
   pageTitle="Redefinir um Gateway de VPN do Azure | Microsoft Azure"
   description="Este artigo o orienta sobre como redefinir o Gateway de VPN do Azure. O artigo se aplica a gateways de VPN tanto nos modelos de implantação clássicos quanto nos modelos de implantação do Resource Manager."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc"/>


# <a name="reset-an-azure-vpn-gateway-using-powershell"></a>Redefinir um Gateway de VPN do Azure usando o PowerShell


Este artigo o guia pela redefinição do Gateway de VPN do Azure usando cmdlets do PowerShell. Estas instruções incluem tanto o modelo de implantação clássico quanto o modelo de implantação do Resource Manager.

Redefinir o gateway de VPN do Azure é útil se você perde a conectividade VPN entre locais em um ou mais túneis de VPN S2S. Nessa situação, os dispositivos VPN locais estão funcionando corretamente, mas não são capazes de estabelecer túneis IPsec com os gateways de VPN do Azure. 

Cada gateway de VPN do Azure é composto de duas instâncias VM em execução em uma configuração de modo de espera-ativo. Quando você usa o cmdlet do PowerShell para reiniciar o gateway, ele reinicializa o gateway e aplica novamente as configurações entre instalações a ele. O gateway mantém o endereço IP público que já tem. Isso significa que você não precisa atualizar a configuração do roteador VPN com um novo endereço IP público do gateway de VPN do Azure.  

Quando o comando for emitido, a instância ativa atual do gateway de VPN do Azure será reinicializada imediatamente. Haverá um breve intervalo durante o failover da instância ativa (que está sendo reinicializada) à instância em espera. O intervalo deve ser menor que um minuto.

Se a conexão não é restaurada após a primeira reinicialização, execute o mesmo comando novamente para reiniciar a segunda instância VM (o novo gateway ativo). Se as duas reinicializações são solicitadas uma após a outra, haverá um período um pouco mais longo durante o qual as duas instâncias da VM (ativas e em espera) estão sendo reinicializadas. Isso causará um intervalo maior na conectividade VPN, exigindo até 2 a 4 minutos para que as VMs concluam as reinicializações.

Após duas reinicializações, se você ainda estiver tendo problemas de conectividade entre instalações, abra uma solicitação de suporte no portal do Azure.

## <a name="before-you-begin"></a>Antes de começar

Antes de redefinir o gateway, verifique os principais itens listados abaixo para cada túnel VPN Site a Site (S2S) de IPsec. Qualquer incompatibilidade nos itens resultará na desconexão de túneis VPN S2S. Verificar e corrigir as configurações para seus gateways de VPN do Azure e locais faz com que você evite reinicializações desnecessárias e interrupções para as outras conexões de trabalho nos gateways.

Verifique os itens a seguir antes de redefinir seu gateway:

- Os VIPs (endereços IP da Internet) para o gateway de VPN do Azure e o gateway de VPN local estão configurados corretamente em ambas a políticas de VPN do Azure e de VPN local.
- A chave pré-compartilhada deve ser a mesma em ambos o gateway de VPN do Azure e o gateway de VPN local.
- Se você aplicar a configuração de IPsec/IKE específica, como criptografia, algoritmos de hash e PFS (Perfect Forward Secrecy), verifique se que o gateway de VPN do Azure e o gateway de VPN local têm as mesmas configurações.

## <a name="reset-a-vpn-gateway-using-the-resource-management-deployment-model"></a>Redefinir um Gateway de VPN usando o modelo de implantação do Gerenciamento de Recursos

O cmdlet do PowerShell Resource Manager para redefinir o gateway é `Reset-AzureRmVirtualNetworkGateway`. O exemplo a seguir redefine o gateway de VPN do Azure, "VNet1GW", no grupo de recursos "TestRG1".

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## <a name="reset-a-vpn-gateway-using-the-classic-deployment-model"></a>Redefinir um Gateway de VPN usando o modelo de implantação clássico

O cmdlet do PowerShell para redefinir o gateway de VPN do Azure é `Reset-AzureVNetGateway`. O exemplo a seguir redefine o gateway de VPN do Azure para a rede virtual chamada "ContosoVNet".
 
    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

Resultado:

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK


## <a name="next-steps"></a>Próximas etapas
    
Confira a [Referência de cmdlet do Gerenciamento de Serviços do PowerShell](https://msdn.microsoft.com/library/azure/mt617104.aspx) e a [Referência de cmdlet do PowerShell Resource Manager](http://go.microsoft.com/fwlink/?LinkId=828732) para obter mais informações.









<!--HONumber=Oct16_HO2-->


