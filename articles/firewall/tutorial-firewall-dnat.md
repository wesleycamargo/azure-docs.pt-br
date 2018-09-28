---
title: Filtrar tráfego de entrada com o DNAT do Firewall do Azure usando o portal do Azure
description: Neste tutorial, você aprenderá a implantar e configurar o DNAT do Firewall do Azure usando o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 766ad04251fbe404d43734115e41e23ae0a4be28
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982016"
---
# <a name="tutorial-filter-inbound-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Tutorial: filtrar tráfego de entrada com o DNAT do Firewall do Azure usando o portal do Azure

Você pode configurar a Conversão de Endereço de Rede de Destino (DNAT) do Firewall do Azure para converter e filtrar o tráfego de entrada para suas sub-redes. O Firewall do Azure não tem um conceito de regras de entrada e regras de saída. Há regras de aplicativo e regras de rede. Elas são aplicadas a qualquer tráfego que chega ao firewall. As regras de rede são aplicadas primeiro, depois as regras do aplicativo e, em seguida, elas são finalizadas.

>[!NOTE]
>Atualmente, o recurso DNAT do Firewall só está disponível no Azure PowerShell e REST.

Por exemplo, se uma regra de rede for atendida, o pacote não será avaliado por regras de aplicativo. Se não houver qualquer correspondência da regra de rede e se o protocolo do pacote for HTTP/HTTPS, o pacote será avaliado pelas regras de aplicativo. Se ainda assim nenhuma correspondência for encontrada, o pacote será avaliado em relação à [coleção de regras da infraestrutura](infrastructure-fqdns.md). Se ainda não houver correspondências, o pacote será negado por padrão.

Quando você configura o modo DNAT, a ação da coleção de regras da NAT é definida como **Conversão de Endereço de Rede de Destino (DNAT)**. O IP público do firewall e a porta são convertidos em um endereço IP privado e porta. Em seguida, as regras são aplicadas normalmente. As regras de rede primeiro e, em seguida, as regras do aplicativo. Por exemplo, você pode configurar uma regra de rede para permitir o tráfego da área de trabalho remota na porta TCP 3389. A conversão de endereços ocorre primeiro e, em seguida, as regras de rede e de aplicativo são aplicadas usando os endereços convertidos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implantar um firewall
> * Criar uma rota padrão
> * Configurar uma regra de DNAT
> * Configurar uma regra de rede
> * Testar o firewall

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Neste tutorial, você criará duas redes virtuais emparelhadas:
- **VN-Hub** - o firewall está nessa rede virtual.
- **VN-Spoke** - o servidor de carga de trabalho está nessa rede virtual.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
1. Entre no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).
1. Na página inicial do portal do Azure, clique em **Grupos de recursos** e clique em **Adicionar**.
2. Em **Nome do grupo de recursos**, digite **RG-DNAT-Test**.
3. Em **Assinatura**, selecione sua assinatura.
4. Em **Local do grupo de recursos**, selecione um local. Todos os recursos criados depois disso devem estar no mesmo local.
5. Clique em **Criar**.

## <a name="set-up-the-network-environment"></a>Configurar o ambiente de rede
Primeiro, crie as redes virtuais e, em seguida, emparelhe-as.

### <a name="create-the-hub-vnet"></a>Criar a rede virtual do hub
1. Na página inicial do portal do Azure, clique em **Todos os serviços**.
2. Em **Rede**, clique em **Redes virtuais**.
3. Clique em **Adicionar**.
4. Em **Nome**, digite **VN-Hub**.
5. Em **Espaço de endereço**, digite **10.0.0.0/16**.
7. Em **Assinatura**, selecione sua assinatura.
8. Em **Grupo de recursos**, escolha **Usar existente** e **RG-DNAT-Test**.
9. Em **local**, selecione o mesmo local usado anteriormente.
10. Em **Sub-rede**, digite **AzureFirewallSubnet** em **Nome**.

     O firewall estará nessa sub-rede e o nome da sub-rede **precisa** ser AzureFirewallSubnet.
     > [!NOTE]
     > O tamanho mínimo da sub-rede AzureFirewallSubnet é /25.
11. Em **Intervalo de endereços**, digite **10.0.1.0/24**.
12. Use as outras configurações padrão e clique em **Criar**.

### <a name="create-a-spoke-vnet"></a>Criar uma rede virtual do spoke

1. Na página inicial do portal do Azure, clique em **Todos os serviços**.
2. Em **Rede**, clique em **Redes virtuais**.
3. Clique em **Adicionar**.
4. Em **Nome**, digite **VN-Spoke**.
5. Em **Espaço de endereço**, digite **192.168.0.0/16**.
7. Em **Assinatura**, selecione sua assinatura.
8. Em **Grupo de recursos**, escolha **Usar existente** e **RG-DNAT-Test**.
9. Em **local**, selecione o mesmo local usado anteriormente.
10. Em **Sub-rede**, em **Nome**, digite **SN-Workload**.

    O servidor estará nessa sub-rede.
1. Em **Intervalo de endereços**, digite **192.168.1.0/24**.
2. Use as outras configurações padrão e clique em **Criar**.

### <a name="peer-the-vnets"></a>Emparelhar as redes virtuais

Agora, emparelhe as duas redes virtuais.

#### <a name="hub-to-spoke"></a>Do hub para o spoke

1. Clique na rede virtual **VN-Hub**.
2. Em **Configurações**, clique em **Emparelhamentos**.
3. Clique em **Adicionar**.
4. Digite **Peer-HubSpoke** para o nome.
5. Escolha **VN-Spoke** para a rede virtual.
7. Clique em **OK**.

#### <a name="spoke-to-hub"></a>Do spoke para o hub

1. Clique na rede virtual **VN-Spoke**.
2. Em **Configurações**, clique em **Emparelhamentos**.
3. Clique em **Adicionar**.
4. Digite **Peer-SpokeHub** para o nome.
5. Escolha **VN-Hub** para a rede virtual.
6. Clique em **Permitir tráfego encaminhado**.
7. Clique em **OK**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma máquina virtual de carga de trabalho e coloque-a na sub-rede **SN-Workload**.

1. Na página inicial do portal do Azure, clique em **Todos os serviços**.
2. Em **Computação**, clique em **Máquinas virtuais**.
3. Clique em **Adicionar**, clique em **Windows Server**, clique em **Windows Server 2016 Datacenter**e clique em **Criar**.

**Noções básicas**

1. Em **Nome**, digite **Srv-Workload**.
5. Digite um nome de usuário e uma senha.
6. Em **Assinatura**, selecione sua assinatura.
7. Em **Grupo de recursos**, clique em **Usar existente** e escolha **RG-DNAT-Test**.
8. Em **local**, selecione o mesmo local usado anteriormente.
9. Clique em **OK**.

**Tamanho**

1. Escolha um tamanho apropriado para uma máquina virtual de teste que executa o Windows Server. Por exemplo, **B2ms** (8 GB de RAM, 16 GB de armazenamento).
2. Clique em **Selecionar**.

**Configurações**

1. Em **Rede**, em **Rede Virtual**, escolha **VN-Spoke**.
2. Em **Sub-rede**, escolha **SN-Workload**.
3. Clique em **Endereço IP público** e, em seguida, clique em **None**.
4. Em **Selecionar portas de entrada públicas**, escolha **Nenhuma porta de entrada pública**. 
2. Deixe as outras configurações padrão e clique em **OK**.

**Resumo**

Revise o resumo e clique em **Criar**. Isso levará alguns minutos para ser concluído.

Após a conclusão da implantação, observe o endereço IP privado da máquina virtual. Ele será usado mais tarde ao configurar o firewall. Clique no nome da máquina virtual e, em **Configurações**, clique em **Rede** para localizar o endereço IP privado.


## <a name="deploy-the-firewall"></a>Implantar o firewall

1. Na página inicial do portal, clique em **Criar um recurso**.
2. Clique em **Rede**e, após **Em destaque**, clique em **Ver todos**.
3. Clique em **Firewall**e clique em **Criar**. 
4. Na página **Criar um Firewall**, use a tabela abaixo para configurar o firewall:
   
   |Configuração  |Valor  |
   |---------|---------|
   |NOME     |FW-DNAT-test|
   |Assinatura     |\<sua assinatura\>|
   |Grupo de recursos     |**Usar existente**: RG-DNAT-Test |
   |Local padrão     |Selecionar o mesmo local usado anteriormente|
   |Escolher uma rede virtual     |**Usar existente**: VN-Hub|
   |Endereço IP público     |**Criar novo**. O endereço IP público deve ser do tipo SKU Standard.|

2. Clique em **Revisar + Criar**.
3. Reveja o resumo e clique em **Criar** para criar o firewall.

   Isso levará alguns minutos para ser implantado.
4. Após a implantação ser concluída, vá para o grupo de recursos **RG-DNAT-Test** e clique no firewall **FW-DNAT-test**.
6. Anote o endereço IP privado. Você o usará mais tarde quando criar a rota padrão.


## <a name="create-a-default-route"></a>Criar uma rota padrão

Para a sub-rede **SN-Workload**, configure a rota de saída padrão para atravessar o firewall.

1. Na página inicial do portal do Azure, clique em **Todos os serviços**.
2. Em **Rede**, clique em **Tabelas de rotas**.
3. Clique em **Adicionar**.
4. Em **Nome**, digite **RT-FWroute**.
5. Em **Assinatura**, selecione sua assinatura.
6. Em **Grupo de recursos**, escolha **Usar existente** e **RG-DNAT-Test**.
7. Em **local**, selecione o mesmo local usado anteriormente.
8. Clique em **Criar**.
9. Clique em **Atualizar** e, em seguida, clique na tabela de rotas **RT-FWroute**.
10. Clique em **Sub-redes** e em **Associar**.
11. Clique em **Rede virtual**e escolha **VN-Spoke**.
12. Em **Sub-rede**, clique em **SN-Workload**.
13. Clique em **OK**.
14. Clique em **Rotas** e em **Adicionar**.
15. Em **Nome da rota**, digite **FW-DG**.
16. Em **Prefixo de endereço**, digite **0.0.0.0/0**.
17. Em **Tipo do próximo salto**, selecione **Solução de virtualização** .

    O Firewall do Azure é, na verdade, um serviço gerenciado, mas a solução de virtualização funciona nessa situação.
1. Em **endereço do próximo salto**, digite o endereço IP privado do firewall anotado anteriormente.
2. Clique em **OK**.


## <a name="configure-a-dnat-rule"></a>Configurar uma regra de DNAT

```azurepowershell-interactive
 $rgName  = "RG-DNAT-Test"
 $firewallName = "FW-DNAT-test"
 $publicip = type the Firewall public ip
 $newAddress = type the private IP address for the Srv-Workload virtual machine 
 
# Get Firewall
    $firewall = Get-AzureRmFirewall -ResourceGroupName $rgName -Name $firewallName
  # Create NAT rule
    $natRule = New-AzureRmFirewallNatRule -Name RL-01 -SourceAddress * -DestinationAddress $publicip -DestinationPort 3389 -Protocol TCP -TranslatedAddress $newAddress -TranslatedPort 3389
  # Create NAT rule collection
    $natRuleCollection = New-AzureRmFirewallNatRuleCollection -Name RC-DNAT-01 -Priority 200 -Rule $natRule
  # Add NAT Rule collection to firewall:
    $firewall.AddNatRuleCollection($natRuleCollection)
  # Save:
    $firewall | Set-AzureRmFirewall
```
## <a name="configure-a-network-rule"></a>Configurar uma regra de rede

1. Abra o **RG-DNAT-Test** e clique no firewall **FW-DNAT-test**.
1. Na página **FW-DNAT-test**, em **Configurações**, clique em **Regras**.
2. Clique em **Adicionar coleção de regras de rede**.

Configure a regra usando a tabela a seguir e, em seguida, clique em **Adicionar**:


|Parâmetro  |Valor  |
|---------|---------|
|NOME     |**RC-Net-01**|
|Prioridade     |**200**|
|Ação     |**Permitir**|

Em **Regras**:

|Parâmetro  |Configuração  |
|---------|---------|
|NOME     |**RL-RDP**|
|Protocolo     |**TCP**|
|Endereços de Origem     |*|
|Endereços de Destino     |Endereço IP privado **Srv-Workload**|
|Portas de destino|**3389**|


## <a name="test-the-firewall"></a>Testar o firewall

1. Conecte uma área de trabalho remota ao endereço IP público do firewall. Você deve estar conectado à máquina virtual **Srv-Workload**.
3. Feche a área de trabalho remota.
4. Altere a ação da coleção de regras de rede **RC-Net-01** para **Negar**.
5. Tente se reconectar ao endereço IP público do firewall. Desta vez, ele não deve funcionar devido à regra **Negar**.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode manter seus recursos de firewall para o próximo tutorial ou, se não forem mais necessários, exclua o grupo de recursos **RG-DNAT-Test** para excluir todos os recursos relacionados ao firewall.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implantar um firewall
> * Criar uma rota padrão
> * Configurar uma regra de DNAT
> * Configurar uma regra de rede
> * Testar o firewall

Em seguida,você pode monitorar os logs do Firewall do Azure.

> [!div class="nextstepaction"]
> [Tutorial: Monitorar os logs do Firewall do Azure](./tutorial-diagnostics.md)
