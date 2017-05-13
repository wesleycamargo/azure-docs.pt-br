---
title: "Infraestrutura e conectividade para o SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs"
description: "Configure a infraestrutura de conectividade necessária para usar SAP HANA no Azure (Instâncias Grandes)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 1d216d7d22fa671d7d35921d3ef7189c335a2577
ms.contentlocale: pt-br
ms.lasthandoff: 04/28/2017


---

# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Infraestrutura e conectividade para o SAP HANA (instâncias grandes) no Azure 

Após a compra do SAP HANA no Azure (Instâncias Grandes) ser finalizada entre você e a equipe de conta empresarial da Microsoft, as seguintes informações são exigidas pela Microsoft para implantar Unidades de Instâncias Grandes HANA:

- Nome do cliente
- Informações de contatos comerciais (incluindo endereço de email e número de telefone)
- Informações de contatos técnicos (incluindo endereço de email e número de telefone)
- Informações de contatos da rede técnica (incluindo endereço de email e número de telefone)
- Região de implantação do Azure (Oeste dos EUA ou Leste dos EUA a partir de abril de 2017)
- Confirmar SAP HANA no Azure (Instâncias Grandes) SKU (configuração)
- Como já detalhada no documento de visão geral e arquitetura do HANA instâncias grandes, para cada região do Azure que está sendo implantado:
    - Uma/29 intervalo de endereços IP para conexões de ER P2P que conectar redes virtuais do Azure para instâncias grandes HANA
    - Um bloco CIDR /24 usado para o Pool de IPs de Servidores de Instâncias Grandes HANA
- Os valores de intervalo de endereços IP usados no atributo de Espaço de Endereço de rede virtual de cada Rede Virtual do Azure que irão se conectar às instâncias grandes HANA
- Dados para cada sistema de Instâncias Grandes HANA:
  - Nome de host desejado - o ideal é usar o nome de domínio totalmente qualificado.
  - Endereço IP desejado para a unidade de instância grande HANA fora do intervalo de endereços do Pool de IP do servidor - tenha em mente que os 30 primeiros endereços IP no intervalo de endereços do Pool de IP do servidor são reservados para uso interno em instâncias grandes HANA
  - Nome do SID SAP HANA para a instância SAP HANA (necessária para criar os volumes de disco necessários relacionados ao SAP HANA)
  - A groupid que o usuário hana-sidadm tem no sistema operacional Linux é necessária para criar os volumes de disco relacionados ao SAP HANA. A instalação do SAP HANA normalmente cria o grupo de sapsys com uma id de grupo de 1001. O usuário hana-sidadm faz parte do grupo
  - A userid que o usuário hana-sidadm tem no sistema operacional Linux é necessária para criar os volumes de disco relacionados ao SAP HANA.
- ID de assinatura do Azure para a assinatura do Azure no qual o SAP HANA nas instâncias grandes HANA do Azure serão conectadas diretamente

Depois de fornecer as informações, a Microsoft provisiona SAP HANA no Azure (instâncias grandes) e retornará as informações necessárias para vincular suas redes virtuais do Azure para instâncias grandes HANA e acessar as unidades de instância grande HANA.

## <a name="connecting-azure-vms-to-hana-large-instances"></a>Conectar máquinas virtuais do Azure a Grandes Instâncias do HANA

Como já foi mencionado em [visão geral do SAP HANA (instâncias grandes) e arquitetura no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) a implantação mínima de instâncias grandes HANA com a camada de aplicativo SAP no Azure parece como:

![Rede virtual do Azure conectado ao SAP HANA no Azure (Instâncias Grandes) e local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Examinando melhor o lado da Rede Virtual do Azure, podemos perceber a necessidade de:

- A definição de uma Rede Virtual do Azure que será usado para implantar as VMs de camada do aplicativo SAP em.
- Que significa que a Rede Virtual do Azure, uma sub-rede padrão é definido automaticamente que é usada para implantar as VMs em.
- A Rede Virtual do Azure que é criado deve ter pelo menos uma sub-rede VM e uma sub-rede de Gateway de ExpressRoute. A eles devem ser atribuídos os intervalos de endereços IP conforme especificado e discutido abaixo.

Portanto, vamos examinar mais detalhadamente a criação de Rede Virtual do Azure para instâncias grandes HANA

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Criação da Rede Virtual do Azure para instâncias grandes HANA

>[!Note]
>A Rede Virtual do Azure para instância grande HANA deve ser criada usando o modelo de implantação do Azure Resource Manager. Não há suporte para o modelo de implantação do Azure mais antigo, geralmente conhecido como ASM, com a solução de instância grande HANA.

A rede virtual pode ser criada usando o Portal do Azure, PowerShell, o modelo do Azure ou CLI do Azure (confira [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). O exemplo a seguir analisamos uma rede virtual criada por meio do Portal do Azure.

Se, examinamos as definições de uma Rede Virtual do Azure pelo Portal do Azure, vamos dar uma olhada em algumas das definições e como aqueles relacionam à qual lista abaixo. Como estamos falando sobre o **Espaço de Endereço** significa o Espaço de Endereço de Rede Virtual do Azure tem permissão para usar. Isso também é o intervalo de endereços que usará a rede virtual para a propagação de rotas BGP. Esse **Espaço de Endereço** pode ser visto aqui:

![Espaço de Endereço da Rede Virtual do Azure exibido no Portal do Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

No caso acima, com 10.16.0.0/16, a Rede Virtual do Azure foi fornecida um intervalo de endereços IP bastante grande e amplo para usar. Significa que todos os intervalos de endereços IP das sub-redes subsequentes nessa rede virtual podem ter seus intervalos dentro desse ‘Espaço de Endereço'. Geralmente, não recomendamos tal um intervalo de endereços grande para a rede virtual único no Azure. Mas obter um passo adiante, vamos examinar as sub-redes definidas na Rede Virtual do Azure:

![Sub-redes de Rede Virtual do Azure e seus intervalos de endereços IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Como você pode ver vamos examinar uma rede virtual com uma sub-rede VM primeiro (aqui chamado de 'default') e uma sub-rede chamada 'GatewaySubnet'.
Na seção a seguir, nos referimos ao IP do intervalo de endereços de sub-rede que foi chamado 'default' nos gráficos como **intervalo de endereços IP de sub-rede de VM do Azure**. Nas seções a seguir, nos referimos ao intervalo de endereços IP da sub-rede de Gateway como **intervalo de endereços IP de sub-rede de Gateway de rede virtual**. 

No caso do demonstrado acima de dois gráficos, verá que o **Espaço de Endereço de rede virtual** aborda ambos, o **intervalo de endereços IP de sub-rede de VM do Azure** e **intervalo de endereços IP de sub-rede de Gateway de rede virtual**. 

Em outros casos onde você precisa economizar ou ser muito específicos com seus intervalos de endereços IP, você pode restringir o **Espaço de Endereço de rede virtual** de uma rede virtual para os intervalos específicos que está sendo usado por cada sub-rede. Nesse caso, você pode definir o **Espaço de Endereço de rede virtual** de uma rede virtual específica como vários intervalos como mostrado aqui:

![Espaço de Endereço de Rede Virtual do Azure com dois espaços](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Nesse caso, o **Espaço de Endereço de Rede Virtual** tem dois espaços definidos. Esses são idênticos aos intervalos de endereços IP definidos para **intervalo de endereços IP de sub-rede de VM do Azure** e **intervalo de endereços IP de sub-rede de Gateway de rede virtual**.

Você pode usar qualquer padrão de nomenclatura que desejar para essas sub-redes de locatário (sub-redes de VM). No entanto, **sempre deve haver uma e apenas uma sub-rede de gateway para cada rede virtual** que se conecta ao SAP HANA no circuito de ExpressRoute do Azure (instâncias grandes), e **esta sub-rede de gateway deve sempre ser denominada “GatewaySubnet”** para garantir a colocação correta do gateway de ExpressRoute.

> [!WARNING] 
> É importante que a sub-rede de gateway seja sempre denominada “GatewaySubnet”.

Várias sub-redes VM podem ser usadas, até mesmo utilizando intervalos de endereços não contíguas. Mas como mencionado anteriormente, esses intervalos de endereços devem ser cobertos pelo **Espaço de Endereço de rede virtual** da rede virtual de forma agregada ou em uma lista dos intervalos exatas de sub-redes de VM e a sub-rede de gateway.

Resumindo o fato importante sobre uma Rede Virtual do Azure que se conecta ao HANA grandes instâncias:

- Você precisará enviar à Microsoft o **Espaço de Endereço de rede virtual** ao executar uma implantação inicial do HANA grandes instâncias. 
- O **Espaço de Endereço de rede virtual** pode ser um intervalo maior que abrange o intervalo para o intervalo de endereços IP de sub-rede de VM do Azure e o intervalo de endereços IP de sub-rede de Gateway de rede virtual.
- Ou você pode enviar como **Espaço de Endereço de Rede Virtual** vários intervalos que abrangem o IP diferente endereços intervalos de intervalo de endereços IP de sub-rede VM e o intervalo de endereços IP de sub-rede de Gateway de rede virtual.
- O prefixo **Espaço de Endereço de rede virtual** é usada propagação de roteamento do BGP.
- O nome da sub-rede de Gateway deve ser: **"GatewaySubnet"**.
- O **Espaço de Endereço de rede virtual** é usado como um filtro no lado do HANA grande instância para permitir ou impedir que o tráfego para as unidades de instância grande HANA do Azure. Se as informações de roteamento de BGP de Rede Virtual do Azure e os intervalos de endereços IP configurados para filtragem no lado do HANA grande instância não coincidirem, surgirão problemas na conectividade.
- Há alguns detalhes sobre a sub-rede de Gateway que são discutidas mais para baixo na seção 'Conectando a uma rede virtual para ExpressRoute de instância grande HANA'



### <a name="different-ip-address-ranges-to-be-defined"></a>Diferentes intervalos de endereços IP a serem definidos 

Nós já apresentamos alguns intervalos de endereços IP necessários para implantar instâncias grandes do HANA acima. Mas há alguns mais intervalos de endereços IP que são importantes. Vamos analisar alguns detalhes adicionais. Os seguintes endereços IP dos quais nem todos precisam ser enviadas para a Microsoft precisam ser definida antes de enviar uma solicitação para a implantação inicial:

- **Espaço de endereço de rede virtual:** como já apresentada acima, é o intervalo de endereços IP atribuídos (ou planeja atribuir) para o parâmetro de Espaço de Endereço nas redes virtuais do Azure (VNet) conectar-se ao ambiente de instância grande do SAP HANA. É recomendável que esse parâmetro do Espaço de Endereço é um valor de várias linha composto os intervalos de sub-rede de VM do Azure e o intervalo de sub-rede de Gateway do Azure, conforme mostrado no elemento gráfico acima. Esse intervalo não deve sobrepor seu local ou intervalos de endereços do Pool de IP do servidor ou ER P2P. Como fazer isso? Seu provedor de serviço ou equipe de rede corporativa deve fornecer um intervalo de endereços IP que não é usada dentro de sua rede. Exemplo: se a sua sub-rede de VM do Azure (veja acima) é 10.0.1.0/24 e sua sub-rede de Gateway do Azure (veja abaixo) é 10.0.2.0/28, em seguida, o Espaço de Endereço de Rede Virtual do Azure é recomendado para ser duas linhas; 10.0.1.0/24 e 10.0.2.0/28. Embora os valores de Espaço de Endereço podem ser agregados é recomendável combiná-los para os intervalos de sub-rede para evitar a reutilização acidental no futuro em outro lugar na sua rede. **É um intervalo de endereços IP que precisa ser enviado à Microsoft quando uma implantação inicial é solicitada**

- **Intervalo de endereços IP do Azure VM sub-rede:** este IP intervalo de endereços, conforme discutido anteriormente, é aquele que você atribuiu (ou planeja atribuir) para o parâmetro de sub-rede da Rede Virtual do Azure em sua Rede Virtual do Azure que se conectar ao ambiente de instância grande do SAP HANA. Este intervalo de endereços IP é usado para atribuir endereços IP para VMs do Azure. Esse intervalo poderão se conectar aos servidores de instância grande do SAP HANA. Se necessário, podem ser usadas várias sub-redes de VM do Azure. Um bloco CIDR /24 é recomendado pela Microsoft para cada sub-rede de VM do Azure. Este intervalo de endereços deve ser uma parte dos valores usados no Espaço de Endereço de Rede Virtual do Azure. Como fazer isso? Seu provedor de serviço ou de equipe de rede corporativa deve fornecer um intervalo de endereços IP que não estejam em uso em sua rede.

- **Intervalo de endereços IP de sub-rede de Gateway de rede virtual:** dependendo dos recursos que você planeja usar, o tamanho recomendado seria:
   - Gateway de ExpressRoute de excelente desempenho: bloco de endereço /26
   - Coexistência com VPN e ExpressRoute usando um Gateway de ExpressRoute de alto desempenho (ou menor): bloco de endereço /27
   - Todas as outras situações: bloco de endereço /28. Este intervalo de endereços deve ser uma parte dos valores usados nos valores de "Espaço de endereço de rede virtual". Este intervalo de endereços deve ser uma parte dos valores usados nos valores de Espaço de Endereço de Rede Virtual do Azure que você precisa para enviar à Microsoft. Como fazer isso? Seu provedor de serviço ou de equipe de rede corporativa deve fornecer um intervalo de endereços IP que não estejam em uso em sua rede. 

- **Intervalo para conectividade de ER P2P de endereços:** é o intervalo IP para sua conexão de ExpressRoute de instância grande (ER) do SAP HANA P2P. Este intervalo de endereços IP deve ser um intervalo de endereços IP CIDR /29. Esse intervalo não deve sobrepor seu local ou outros intervalos de endereços IP do Azure. Isso é usado para configurar a conectividade de ER em seu Gateway de ExpressRoute de Rede Virtual do Azure para os servidores de instância grande do SAP HANA. Como fazer isso? Seu provedor de serviço ou de equipe de rede corporativa deve fornecer um intervalo de endereços IP que não estejam em uso em sua rede. **É um intervalo de endereços IP que precisa ser enviado à Microsoft quando uma implantação inicial é solicitada**
  
- **O intervalo de endereços do Pool do IP do servidor:** este intervalo de endereços IP é usado para atribuir o endereço IP individual para servidores de instância grande HANA. O tamanho recomendado de sub-rede é um /24 CIDR bloquear - mas se necessária pode ser menor em um mínimo de fornecimento de 64 endereços IP. Desse intervalo, os 30 primeiros endereços IP serão reservados para uso pela Microsoft. Certifique-se de que isso sea considerado quando o tamanho do intervalo for escolhido. Esse intervalo não deve sobrepor seu local ou outro IP Azure endereços. Como fazer isso? Seu provedor de serviço ou de equipe de rede corporativa deve fornecer um intervalo de endereços IP que não estejam em uso em sua rede. Um bloco CIDR /24 (recomendado) exclusivo para ser usado para atribuir os endereços IP específicos necessários ao SAP HANA no Azure (Instâncias Grandes). **É um intervalo de endereços IP que precisa ser enviado à Microsoft quando uma implantação inicial é solicitada**
 
Embora você precise definir e planejar os intervalos de endereços IP acima, nem todos eles precisam ser transmitidos à Microsoft. Para resumir o acima, os intervalos de endereços IP que é necessário nomear à Microsoft são:

- Espaços de Endereço de Rede Virtual do Azure
- Intervalo de endereços para conectividade de ER-P2P
- Intervalo de Endereços do Pool de IPs do Servidor

Adicionar redes virtuais adicionais que precisam se conectar a instâncias grandes HANA, exige que você enviar o novo Espaço de Endereço de Rede Virtual do Azure você está adicionando à Microsoft. 

Abaixo está um exemplo de alguns intervalos de exemplo e os intervalos diferentes conforme necessário configurar e, eventualmente, fornecer à Microsoft. Como você pode ver, os valores para o Espaço de Endereço de Rede Virtual do Azure não é agregado no primeiro exemplo, mas é definido de intervalos de intervalo de endereços IP de sub-rede primeira VM do Azure e o intervalo de endereços IP de sub-rede de Gateway de rede virtual. Usar várias sub-redes VM na VNet do Azure funcione adequadamente, configurando e enviando o IP adicional endereço intervalos das sub-redes de VM adicionais como parte do Espaço de Endereço de Rede Virtual do Azure.

![Intervalos de endereços IP necessários na implantação mínima do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Você também tem a possibilidade de agregar dados que enviar à Microsoft. Nesse caso o Espaço de Endereço de Rede Virtual do Azure só inclui um espaço. Usando os intervalos de endereços IP usados no exemplo acima. Isso seria semelhante ao seguinte:

![Segunda possibilidade de intervalos de endereços IP necessários na implantação mínima do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Como você pode ver acima, em vez de dois intervalos menores que definiu o Espaço de Endereço do VNet do Azure, temos um intervalo maior que abrange 4096 endereços IP. Uma definição grande do Espaço de Endereço deixa alguns grandes intervalos não utilizados. Como os valores de Espaço de Endereço de rede virtual são usados para a propagação de rotas BGP, uso de intervalos não usados em locais ou em outro lugar na sua rede pode causar problemas de roteamento. Portanto, é recomendável manter o Espaço de Endereço estreitamente alinhadas com o Espaço de Endereço de sub-rede real usado. Você sempre pode adicionar novos valores de Espaço de Endereço posteriormente se necessário, out incorrer em tempo de inatividade na rede virtual.
 
> [!IMPORTANT] 
> Cada endereço IP do Espaço de Endereço de rede virtual do intervalo de ER-P2P, Pool de IP do servidor do Azure deve **não** se sobrepõem uns com os outros ou qualquer outro intervalo usados em outro lugar na rede; cada um deve ser discreta e os dois elementos gráficos acima mostram não uma sub-rede de qualquer outro intervalo. Se ocorrerem sobreposições entre intervalos, a Rede Virtual do Azure não poderá se conectar ao circuito de ExpressRoute.

### <a name="next-steps-after-address-ranges-have-been-defined"></a>Próximas etapas após a definição de intervalos de endereços
Após a definição de intervalos de endereços IP as seguintes atividades precisam ocorrer:

1. Envie os intervalos de endereços IP para o Espaço de Endereço de Rede Virtual do Azure, a conectividade de ER P2P e o intervalo de endereços do Pool do IP do servidor, junto com outros dados que foi listados no início do documento. Neste momento você também pode começar a criar a rede virtual e as sub-redes de VM. 
2. Um circuito do ExpressRoute é criado pela Microsoft entre sua assinatura do Azure e o carimbo de Instâncias Grandes HANA.
3. Uma rede de locatário é criada no carimbo instância grande pela Microsoft.
4. A Microsoft vai configurar a rede na infraestrutura do SAP HANA no Azure (Instâncias Grandes) para aceitar os endereços IP do seu Espaço de Endereço de Rede Virtual do Azure que irá se comunicar com o HANA em Instâncias Grandes.
5. Dependendo do SKU específico do SAP HANA no Azure (Instâncias Grandes) adquirido, a Microsoft atribuirá uma unidade de computação em uma rede de locatário, alocar e montar o armazenamento e instalar o sistema operacional (SUSE ou Red Hat Linux). Endereços IP para essas unidades são tirados o endereço de Pool de IP do servidor intervalo enviado à Microsoft.

No final do processo de implantação, a Microsoft fornece os seguintes dados para você:
- As informações necessárias para conectar suas Redes Virtuais do Azure ao circuito de ExpressRoute que conecta redes virtuais do Azure a instâncias grandes HANA:
     - Chaves de autorização
     - PeerID de ExpressRoute
- Os dados para acessar Instâncias Grandes HANA depois que você tiver estabelecido o circuito de ExpressRoute e a Rede Virtual do Azure.


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Conectar uma Rede Virtual a ExpressRoute de Instância Grande do HANA

Como você definiu todos os intervalos de endereço IP e agora tem os dados de volta da Microsoft, você pode começar a conectar a rede virtual que você criou antes do HANA grandes instâncias. Depois de criar a rede virtual do Azure, um gateway de ExpressRoute deve ser criado na rede virtual para vincular a rede virtual ao circuito de ExpressRoute que se conecta ao locatário do cliente no carimbo de instância grande.

> [!NOTE] 
> Esta etapa pode levar até 30 minutos para ser concluída, pois o novo gateway é criado na assinatura do Azure designada e depois conectado à rede virtual do Azure especificada.

Se já existir um gateway, verifique se ele é um gateway de ExpressRoute ou não. Caso contrário, o gateway deve ser excluído e recriado como um gateway de ExpressRoute. Se um gateway de ExpressRoute já estiver estabelecido, uma vez que a rede virtual do Azure já está conectada ao circuito de ExpressRoute para conectividade local, vá para a seção Vinculação de redes virtuais abaixo.

- Use o (novo) [Portal do Azure](https://portal.azure.com/) ou o PowerShell para criar um gateway de VPN de ExpressRoute conectado à sua rede virtual.
  - Se você usar o Portal do Azure, adicione um novo **Gateway de rede Virtual** e, em seguida, selecione **ExpressRoute** como o tipo de gateway.
  - Se você escolher o PowerShell, primeiro baixe e use a versão mais recente do [SDK do Azure PowerShell](https://azure.microsoft.com/downloads/) para garantir uma experiência ideal. Os comandos a seguir criam um gateway de ExpressRoute. O texto é precedido por um _$_ que são variáveis definidas pelo usuário que precisam ser atualizadas com as informações específicas.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Neste exemplo, a SKU de gateway HighPerformance foi usada. As opções são HighPerformance ou UltraPerformance como as únicas SKUs de gateway que são suportadas para SAP HANA no Azure (Instâncias Grandes).

### <a name="linking-vnets"></a>Como vincular redes virtuais

Agora que a rede virtual do Azure tem um gateway de ExpressRoute, você usa as informações de autorização fornecidas pela Microsoft para conectar o gateway de ExpressRoute para o SAP HANA no circuito de ExpressRoute do Azure (Instâncias Grandes) criado para essa conectividade. Isso pode ser feito usando o Portal do Azure ou o PowerShell. O Portal é recomendável, porém instruções do PowerShell são da seguinte maneira. 

- Faça o seguinte para cada gateway de rede virtual usando um AuthGUID diferente para cada conexão. As duas primeiras entradas exibidas abaixo fazem parte das informações fornecidas pela Microsoft. Além disso, o AuthGUID é específico para cada rede virtual e seu gateway. Significa que você precisa obter outro AuthID para o circuito de ExpressRoute que conecta HANA grandes instâncias no Azure se você quiser adicionar outra Rede Virtual do Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Talvez seja necessário executar essa etapa mais de uma vez, se você quiser conectar o gateway para vários circuitos de ExpressRoute que estão associados à sua assinatura. Por exemplo provavelmente, você precisará conectar o mesmo Gateway de rede virtual ao circuito de ExpressRoute que se conecta a rede virtual à sua rede local.

## <a name="adding-more-ip-addresses-or-subnets"></a>Como adicionar mais endereços IP ou sub-redes

Use o Portal do Azure, PowerShell ou CLI ao adicionar mais endereços IP ou sub-redes.

Nesse caso, a recomendação é adicionar o novo intervalo de endereços IP como novo intervalo para o Espaço de Endereço de rede virtual em vez de gerar um novo intervalo agregado. Em ambos os casos, você precisa enviar essa alteração para a Microsoft para permitir a conectividade fora desse novo intervalo de endereços IP para as unidades de instância grande HANA no seu cliente. Você pode abrir uma solicitação de suporte do Azure para adicioná-la. Após receber a confirmação, realize as próximas etapas.

Para criar uma sub-rede adicional no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e para criar a partir do PowerShell, confira [Criar uma rede virtual usando o PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="adding-vnets"></a>Como adicionar redes virtuais

Depois de conectar inicialmente uma ou mais redes virtuais do Azure, talvez você queira adicionar mais que acessam SAP HANA no Azure (Instâncias Grandes). Primeiro, envie uma solicitação de suporte do Azure. Nessa solicitação inclua as informações específicas identificando a implantação específica do Azure e os intervalos de Espaço de Endereço IP para do Espaço de Endereço de Rede Virtual do Azure. O SAP HANA no Gerenciamento de Serviços do Azure fornece as informações necessárias que você precisa para conectar as redes virtuais adicionais e a ExpressRoute. Para cada rede virtual, você precisará uma chave de autorização exclusiva para se conectar ao circuito de ExpressRoute para HANA grandes instâncias.

Etapas para adicionar uma nova rede virtual do Azure:

1. Conclua a primeira etapa no processo de integração e confira a seção _Criar rede virtual do Azure_ acima.
2. Conclua a segunda etapa no processo de integração e confira a seção _Criar sub-rede de gateway_ acima.
3. Abra uma solicitação de suporte do Azure com informações sobre a nova rede virtual e solicite uma nova Chave de Autorização para conectar suas redes virtuais adicionais ao circuito de ExpressRoute de instância grande HANA.
4. Quando notificado de que a autorização foi concluída, use as informações de autorização fornecidas pela Microsoft para concluir a terceira etapa no processo de integração e confira a seção _Vinculando redes virtuais_ acima.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Como aumentar a largura de banda do circuito de ExpressRoute

Consulte com o SAP HANA no Gerenciamento de Serviços do Azure. Se você for aconselhado a aumentar a largura de banda do SAP HANA no circuito de ExpressRoute do Azure (Instâncias Grandes), crie uma solicitação de suporte do Azure. (Você pode solicitar um aumento para uma largura de banda do circuito único até um máximo de 10 Gbps). Em seguida você receberá uma notificação após a operação ser concluída. Nenhuma ação adicional é necessária para habilitar essa velocidade superior no Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Como adicionar um circuito de ExpressRoute adicional

Consulte com o SAP HANA no Gerenciamento de Serviços do Azure, se você estiver ciente de que um circuito de ExpressRoute adicional é necessário. Crie uma solicitação de suporte do Azure para criar um novo circuito de ExpressRoute (e para obter informações de autorização para se conectar a ela). O espaço de endereço que será usado nas redes virtuais deve ser definido antes de fazer a solicitação, para que o SAP HANA, no Gerenciamento de Serviços do Azure, forneça autorização.

Depois que o novo circuito for criado e o SAP HANA na configuração de Gerenciamento de Serviços do Azure estiver concluído, você receberá uma notificação com as informações que você precisa para continuar. Siga as etapas fornecidas acima para criar e conectar-se à nova rede virtual para este circuito adicional. Você não poderá se conectar às redes virtuais do Azure para este circuito adicional se eles já estiverem conectados a outro circuito de ExpressRoute de SAP HANA no Azure (Instância Grande).

## <a name="deleting-a-subnet"></a>Como excluir uma sub-rede

Para remover uma sub-rede de rede virtual, o Portal do Azure, o PowerShell ou a CLI podem ser usados. Caso o IP de Rede Virtual do Azure endereço intervalo/Azure Espaço de Endereço de rede virtual foi um intervalo agregado, não há nenhum acompanhamento para você com a Microsoft. Exceto que você precisa estar ciente de que a rede virtual ainda está se propagando Espaço de Endereço de rota BGP que inclui a sub-rede excluída. Se você tiver definido o IP de Rede Virtual do Azure endereço intervalo/Azure Espaço de Endereço de rede virtual como vários intervalos de endereços IP que um deles foi atribuído à sua sub-rede excluído, você deve excluir que fora do seu Espaço de Endereço de rede virtual e informar subsequentemente SAP HANA em gerenciamento de serviços do Azure para removê-lo do SAP HANA no Azure (instâncias grandes) tem permissão para se comunicar com os intervalos.

Enquanto ainda não for específico, as diretrizes dedicadas no Azure.com sobre a remoção de sub-redes, o processo de remoção de sub-redes é o inverso do processo para adicioná-los. Confira o artigo do portal do Azure [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações sobre a criação de sub-redes.

## <a name="deleting-a-vnet"></a>Como excluir uma rede virtual

Ao excluir uma rede virtual, use o Portal do Azure, PowerShell ou CLI. O SAP HANA no Gerenciamento de Serviços do Azure remove as autorizações existentes no SAP HANA no circuito de ExpressRoute do Azure (Instâncias Grandes) e remove o intervalo de endereços IP de Rede Virtual do Aure/Espaço de Endereço de Rede Virtual do Aure para a comunicação com Instâncias Grandes HANA.

Depois que a rede virtual tiver sido removida, abra uma solicitação de suporte do Azure para fornecer os intervalos de endereço IP a serem removidos.

Enquanto ainda não for específico, as diretrizes dedicadas no Azure.com sobre a remoção de redes virtuais, o processo de remoção de redes virtuais é o inverso do processo para adicioná-los, que é descrito acima. Confira os artigos [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Criar uma rede virtual usando o PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações sobre a criação de redes virtuais.

Para garantir que tudo seja removido, exclua os itens a seguir:

- A conexão de ExpressRoute, o Gateway da Rede Virtual, o IP Público do Gateway de Rede Virtual e a Rede Virtual

## <a name="deleting-an-expressroute-circuit"></a>Como excluir um circuito de ExpressRoute

Para remover um SAP HANA adicional no circuito de ExpressRoute do Azure (Instâncias Grandes), abra uma solicitação de suporte do Azure com o SAP HANA no Gerenciamento de Serviços do Azure e solicite que o circuito seja excluído. Na assinatura do Azure, você pode excluir ou manter a rede virtual conforme necessário. No entanto, você deve excluir a conexão entre o circuito de ExpressRoute de instâncias grandes HANA e o gateway de rede virtual vinculado.

Se você também quiser remover uma rede virtual, siga as diretrizes sobre Como excluir uma rede virtual na seção acima.



