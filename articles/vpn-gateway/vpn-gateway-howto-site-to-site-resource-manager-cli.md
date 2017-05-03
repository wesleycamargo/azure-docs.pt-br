---
title: 'Conectar sua rede local a uma rede virtual do Azure: VPN Site a Site: CLI | Microsoft Docs'
description: "Etapas para criar uma conexão IPsec de sua rede local para uma rede virtual do Azure pela Internet pública. Essas etapas o ajudarão a criar uma conexão de Gateway de VPN Site a Site entre locais usando a CLI."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c3563f3a3fa46d40ba02fe97b3b0ebe3c45caddd
ms.lasthandoff: 04/26/2017


---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Criar uma rede virtual com uma conexão VPN Site a Site usando a CLI

Este artigo mostra como usar a CLI do Azure para criar uma conexão de gateway de VPN Site a Site de sua rede local para a rede virtual. As etapas neste artigo se aplicam ao modelo de implantação do Resource Manager. Você também pode criar essa configuração usando uma ferramenta de implantação ou um modelo de implantação diferente, selecionando uma opção diferente na lista a seguir:

> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Clássico - portal do Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Clássico - portal clássico](vpn-gateway-site-to-site-create.md)
> 
>


![Diagrama de conexão Site a Site de Gateway de VPN entre locais](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-connection-diagram.png)

Uma conexão de gateway de VPN Site a Site é usada para conectar a rede local a uma rede virtual do Azure por um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão exige um dispositivo VPN localizado no local que tenha um endereço IP público voltado para o exterior atribuído a ele. Para saber mais sobre os gateways de VPN, veja [Sobre o gateway de VPN](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Verifique se você deseja trabalhar com o modelo de implantação do Resource Manager. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* Um dispositivo VPN compatível e alguém que possa configurá-lo. Para obter mais informações sobre dispositivos VPN compatíveis e a configuração de dispositivo, confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).
* Um endereço IPv4 público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode estar localizado atrás de um NAT.
* Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede local, você precisará trabalhar em conjunto com alguém que possa lhe fornecer os detalhes. Ao criar essa configuração, você deve especificar os prefixos de intervalo de endereços IP que o Azure roteará para seu local. Nenhuma das sub-redes da rede local podem se sobrepor às sub-redes de rede virtual às quais você deseja se conectar. 
* A versão mais recente dos comandos da CLI (2.0 ou posterior). Para saber mais sobre como instalar os comandos do CLI, veja [Instalar a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="example-values"></a>Valores de exemplo

Você pode usar os seguintes valores para criar um ambiente de teste ou fazer referência a esses valores para entender melhor os exemplos neste artigo:

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.12.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.12.0.0/24 
GatewaySubnet           = 10.12.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24 
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1. Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela.

```azurecli
az login
```

Se tiver mais de uma assinatura do Azure, liste as assinaturas para a conta.

```azurecli
Az account list --all
```

Especifique a assinatura que você quer usar.

```azurecli
Az account set --subscription <replace_with_your_subscription_id>
```

## <a name="2-create-a-resource-group"></a>2. Criar um grupo de recursos

O exemplo a seguir cria um grupo de recursos denominado 'TestRG1' no local 'eastus'. Se já tiver um grupo de recursos na região em que deseja criar a rede virtual, você poderá usá-lo.

```azurecli
az group create -n TestRG1 -l eastus
```

## <a name="VNet"></a>3. Criar uma rede virtual

Se você ainda não tiver uma rede virtual, crie uma. Ao criar uma rede virtual, certifique-se de que os espaços de endereço que você especificar não se sobreponham nenhum espaço de endereço que você tenha na rede local. 

O exemplo a seguir cria uma rede virtual chamada 'TestVNet1' e uma sub-rede 'Subrede 1'.

```azurecli
az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.12.0.0/16 -l eastus --subnet-name Subnet1 --subnet-prefix 10.12.0.0/24
```

## 4. <a name="gwsub"></a>Criar a sub-rede de gateway

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

Para essa configuração, você também precisará de uma sub-rede de gateway. O gateway de rede virtual usa uma sub-rede de gateway que contém os endereços IP que são usados pelos serviços de gateway de VPN. Quando você cria uma sub-rede de gateway, ela deve ser nomeada como 'GatewaySubnet'. Se usar outro nome, você criará uma sub-rede, mas o Azure não a tratará como uma sub-rede de gateway.

O tamanho da sub-rede de gateway que você especifica depende da configuração do gateway de VPN que deseja criar. Embora seja possível criar uma sub-rede de gateway tão pequena quanto /29, recomendamos que você crie uma sub-rede maior que inclua mais endereços selecionando /27 ou /28. Usar uma sub-rede de gateway maior permite endereços IP suficientes para acomodar as possíveis configurações futuras.


```azurecli
az network vnet subnet create --address-prefix 10.12.255.0/27 -n GatewaySubnet -g TestRG1 --vnet-name TestVNet1
```

## <a name="localnet"></a>5. Criar o gateway de rede local

O gateway de rede local geralmente se refere ao seu local. Você atribui um nome ao site pelo qual o Azure pode fazer referência a ele e especifica o endereço IP do dispositivo VPN local para o qual você criará uma conexão. Você também pode especificar os prefixos de endereço IP que serão roteados por meio do gateway de VPN para o dispositivo VPN. Os prefixos de endereço que você especifica são os prefixos localizados em sua rede local. Se sua rede local for alterada, você poderá atualizar facilmente os prefixos.

Use os seguintes valores:

* O *--gateway-ip-address* é o endereço IP do dispositivo VPN local. O dispositivo VPN não pode estar localizado atrás de um NAT.
* Os *--local-address-prefixes* são seus espaços de endereços locais.

O exemplo a seguir mostra como adicionar um gateway de rede local com vários prefixos de endereço:

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6. Solicitar um endereço IP público

Solicite um endereço IP público que será alocado para o gateway de VPN da rede virtual. Esse é o endereço IP para o qual você configura seu dispositivo VPN para conexão.

O gateway de rede virtual do modelo de implantação do Gerenciador de Recursos atualmente dá suporte apenas a endereços IP públicos usando o método de Alocação Dinâmica. No entanto, isso não significa que o endereço IP muda. A única vez em que o endereço IP de gateway de VPN é alterado é quando o gateway é excluído e recriado. O endereço IP público do gateway de rede virtual não muda durante o redimensionamento, a redefinição ou a manutenção/atualização interna do gateway de VPN. 

```azurecli
az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7. Criar o gateway de VPN

Crie o gateway de VPN da rede virtual. A criação de um gateway de VPN pode demorar até 45 minutos ou mais para ser concluída.

Use os seguintes valores:

* O *--gateway-type* para uma configuração Site a Site é *Vpn*. O tipo de gateway é sempre específico para a configuração que você está implementando. Para obter mais informações, confira [Tipos de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwtype)
* O *--vpn-type* pode ser *RouteBased* (referido como Gateway Dinâmico em alguns documentos) ou *PolicyBased* (referido como Gateway Estático em alguns documentos). A configuração é específica para os requisitos do dispositivo ao qual você está se conectando. Para obter mais informações sobre tipos de gateway de VPN, confira [Sobre definições de configuração de gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype).
* O *–sku* pode ser Basic, Standard ou HighPerformance. Há limitações de configuração para alguns SKUs. Para obter mais informações, confira [SKUs de gateway](vpn-gateway-about-vpngateways.md#gateway-skus).

Depois de executar esse comando, você não verá comentários nem saída. Demora aproximadamente 45 minutos para criar um gateway.

```azurecli
az network vnet-gateway create -n VNet1GW --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku Standard --no-wait 
```

## <a name="VPNDevice"></a>8. Configurar o dispositivo de VPN

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]
  Para localizar o endereço IP público do gateway de rede virtual, use o exemplo a seguir, substituindo os valores pelos seus próprios. Para facilitar a leitura, a saída é formatada para exibir a lista de IPs públicos em formato de tabela.

  ```azurecli
  az network public-ip list -g TestRG1 -o table
  ```

## <a name="CreateConnection"></a>9. Criar a conexão VPN

Crie a conexão VPN Site a Site entre o gateway de rede virtual e o dispositivo VPN local. Preste atenção especial ao valor de chave compartilhada, que deve corresponder ao valor de chave compartilhada configurado para seu dispositivo VPN.

```azurecli
az network vpn-connection create -n VNet1toSite2 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

Após um instante, a conexão será estabelecida.

## <a name="toverify"></a>10. Verificar a conexão VPN

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)] 

Se quiser usar outro método para verificar a conexão, confira [Verificar uma conexão de gateway de VPN](vpn-gateway-verify-connection-resource-manager.md).

## <a name="common-tasks"></a>Tarefas comuns

### <a name="to-view-local-network-gateways"></a>Para exibir os gateways de rede local

```azurecli
az network local-gateway list --resource-group TestRG1
```

### <a name="modify"></a>Para modificar os prefixos do endereço IP para um gateway de rede local
Se precisar alterar os prefixos do gateway de rede local, use as instruções a seguir. Sempre que você fizer uma alteração, toda a lista de prefixos deverá ser especificada, não apenas os prefixos que você deseja alterar.

- **Se tiver uma conexão especificada**, use o exemplo a seguir. Especifique a lista inteira de prefixos que consiste nos prefixos existentes e nos que você deseja adicionar. Neste exemplo, 10.0.0.0/24 e 20.0.0.0/24 já estão presentes. Adicionamos os prefixos 30.0.0.0/24 e 40.0.0.0/24.

  ```azurecli
  az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 -n VNet1toSite2 -g TestRG1
  ```

- **Se você não tiver uma conexão especificada**, use o mesmo comando que usa para criar um gateway de rede local. Você também pode usar esse comando para atualizar o endereço IP do gateway para o dispositivo VPN. Só use esse comando quando você não tiver uma conexão. Neste exemplo, 10.0.0.0/24, 20.0.0.0/24, 30.0.0.0/24 e 40.0.0.0/24 estão presentes. Especificamos apenas os prefixos que queremos manter. Neste caso, 10.0.0.0/24 e 20.0.0.0/24.

  ```azurecli
  az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
  ```

### <a name="modifygwipaddress"></a>Para modificar o endereço IP do gateway para um gateway de rede local

O endereço IP nessa configuração é o endereço IP do dispositivo VPN para o qual você está criando uma conexão. Se o endereço IP do dispositivo VPN for alterado, você poderá modificar o valor. O endereço IP pode ser alterado, mesmo que haja uma conexão de gateway.

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 -n Site2 -g TestRG1
```

Ao exibir o resultados, verifique se os prefixos de endereço IP estão incluídos.

  ```azurecli
  "localNetworkAddressSpace": { 
    "addressPrefixes": [ 
      "10.0.0.0/24", 
      "20.0.0.0/24", 
      "30.0.0.0/24" 
    ] 
  }, 
  "location": "eastus", 
  "name": "Site2", 
  "provisioningState": "Succeeded",  
  ```

### <a name="to-view-the-virtual-network-gateway-public-ip-address"></a>Para exibir o endereço IP público do gateway de rede virtual

Para localizar o endereço IP público do seu gateway de rede virtual, use o exemplo a seguir. Para facilitar a leitura, a saída é formatada para exibir a lista de IPs públicos em formato de tabela.

```azurecli
az network public-ip list -g TestRG1 -o table
```

### <a name="to-verify-the-shared-key-values"></a>Para verificar os valores de chave compartilhados

Verifique se o valor de chave compartilhado é o mesmo valor usado para a configuração do dispositivo VPN. Caso contrário, execute a conexão novamente usando o valor do dispositivo ou atualize o dispositivo com o valor de retorno. Os valores devem ser correspondentes.

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 -g TestRG1
```

## <a name="next-steps"></a>Próximas etapas

*  Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Para obter informações sobre o BGP, consulte a [Visão Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Para obter informações sobre túneis forçados, confira [Configurar o túnel forçado](vpn-gateway-forced-tunneling-rm.md).
* Para obter uma lista de comandos da CLI do Azure de rede, confira [CLI do Azure](https://docs.microsoft.com/cli/azure/network).
