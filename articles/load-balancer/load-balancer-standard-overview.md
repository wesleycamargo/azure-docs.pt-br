---
title: "Visão geral do Azure Load Balancer Standard | Microsoft Docs"
description: "Visão geral dos recursos do Azure Load Balancer Standard"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: kumud
ms.openlocfilehash: ddcbe895bdaa6eaa49e8ed129fe92b415f2600ef
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Visão geral do Azure Load Balancer Standard (versão prévia)

Juntos, o SKU do Azure Load Balancer Standard e o SKU de IP público Standard, permitem compilar arquiteturas altamente escalonáveis e confiáveis. Os aplicativos que usam o Load Balancer Standard podem usufruir novos recursos. Baixa latência, alta taxa de transferência e escala estão disponíveis para milhões de fluxos para todos os aplicativos de TCP e UDP.

>[!NOTE]
> No momento, o SKU do Load Balancer Standard está em versão prévia. Durante a versão prévia, o recurso pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Uso Complementares do Microsoft Azure para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Use o [SKU do Load Balancer Básico](load-balancer-overview.md) geralmente disponível para os serviços de produção. Os recursos associados a essa versão prévia, [Zonas de disponibilidade](https://aka.ms/availabilityzones) e [Portas HA](https://aka.ms/haports), exigem uma inscrição separada nesse momento. Siga as respectivas instruções para inscrição desses recursos, além de se inscrever na [versão prévia do](#preview-sign-up) Load Balancer Standard.

## <a name="why-use-load-balancer-standard"></a>Por que usar o Load Balancer Standard?

É possível usar o Load Balancer Standard para a variedade completa de data centers virtuais. Desde implantações em pequena escala até grandes e complexas arquiteturas com várias zonas, use o Load Balancer Standard para usufruir os seguintes recursos:

- É possível atingir uma [escala empresarial](#enterprisescale) com o Load Balancer Standard. Esse recurso pode ser usado com qualquer instância VM (máquina virtual) dentro de uma rede virtual, até 1.000 instâncias VM.

- [Novas informações de diagnóstico](#diagnosticinsights) estão disponíveis para ajudá-lo a entender, gerenciar e solucionar problemas deste componente vital de seu data center virtual. Use o Azure Monitor (versão prévia) para mostrar, filtrar e agrupar novas métricas multidimensionais para medidas de integridade do caminho de dados contínuos. Monitore seus dados de front-end para a VM, investigações de integridade do ponto de extremidade, para as tentativas de conexão TCP e para conexões de saída.

- Agora os [Grupos de Segurança de Rede](#nsg) são necessários para qualquer instância VM associada a SKUs do Load Balancer Standard ou SKUs padrão de IP Público. Os NSGs (Grupo de Segurança de Rede) fornecem segurança aprimorada para seu cenário.

- [Portas HA (alta disponibilidade) fornecem alta confiabilidade](#highreliability) e escala para NVAs (soluções de virtualização de rede) e outros cenários de aplicativo. As portas HA balanceiam a carga de todas as portas em um front-end do ILB (Load Balancer interno) do Azure para um pool de instâncias VM.

- Agora as [conexões de saída](#outboundconnections) usam um novo modelo de alocação de porta SNAT (Conversão de Endereços de Rede de Origem) que fornece mais resiliência e escala.

- O [Load Balancer Standard com Zonas de disponibilidade](#availabilityzones) pode ser usado para construir arquiteturas com redundância de zona e zonais. Essas duas arquiteturas podem incluir o balanceamento de carga entre zonas. É possível obter redundância de zona sem dependência nos registros de DNS. Um único endereço IP tem redundância de zona por padrão.  Um único endereço IP poderá acessar qualquer VM em uma rede virtual dentro de uma região em todas as Zonas de disponibilidade.


É possível usar o Load Balancer Standard em uma configuração pública ou interna para dar suporte aos seguintes cenários fundamentais:

- Balancear a carga do tráfego de entrada para instâncias de back-end íntegras.
- Portar o encaminhamento do tráfego de entrada para uma única instância de back-end.
- Converter o tráfego de saída de um endereço IP privado dentro da rede virtual para um endereço IP Público.

### <a name = "enterprisescale"></a>Escala empresarial

 Use o Load Balancer Standard para projetar seu data center virtual de alto desempenho e dar suporte a qualquer aplicativo TCP ou UDP. Use instâncias VM autônomas ou até 1.000 instâncias de conjuntos de dimensionamento de máquina virtual em um pool de back-ends. Continue usando a baixa latência de encaminhamento, ótimo desempenho de taxa de transferência e escala para milhões de fluxos em um serviço do Azure totalmente gerenciado.
 
O Load Balancer Standard pode encaminhar o tráfego para qualquer instância VM em uma rede virtual em uma região. Os tamanhos de pool de back-end podem chegar a 1.000 instâncias com qualquer combinação dos seguintes cenários de VM:

- VMs autônomas sem conjuntos de disponibilidade
- VMs autônomas com conjuntos de disponibilidade
- Conjuntos de dimensionamento de máquinas virtuais, até 1.000 instâncias
- Múltiplos conjuntos de dimensionamento de máquina virtual
- Combinações de VMs e conjuntos de dimensionamento de máquinas virtuais

Não há mais um requisito para conjuntos de disponibilidade. É possível optar por usar conjuntos de disponibilidade para os outros benefícios que eles fornecem.

### <a name = "diagnosticinsights"></a>Informações do diagnóstico

O Load Balancer Standard fornece novos recursos de diagnóstico multidimensionais para configurações públicas e internas do Load Balancer. Essas novas métricas são fornecidas pelo Azure Monitor (versão prévia) e utilizam todos os recursos relacionados, incluindo a capacidade de integrar-se a vários consumidores downstream.

| Métrica | DESCRIÇÃO |
| --- | --- |
| Disponibilidade do VIP | O Load Balancer Standard usa continuamente o caminho de dados de dentro de uma região para o front-end do Load Balancer e até a pilha do SDN compatível com a sua VM. Contanto que instâncias íntegras permaneçam, a medição seguirá o mesmo caminho que o tráfego com balanceamento de carga do seu aplicativo. O caminho de dados usado por seus clientes também é validado. A medição é invisível para seu aplicativo e não interfere com outras operações.|
| Disponibilidade do DIP | O Load Balancer Standard usa um serviço de investigação de integridade distribuído que monitora a integridade do ponto de extremidade do aplicativo de acordo com as definições de configuração. Essa métrica fornece uma exibição agregada ou por ponto de extremidade filtrado de cada ponto de extremidade de instância individual no pool do Load Balancer.  Você pode verificar como o Load Balancer vê a integridade de seu aplicativo conforme indicado pela configuração de sua investigação de integridade.
| Pacotes SYN | O Load Balancer Standard não encerra conexões TCP nem interage com os fluxos de pacotes TCP ou UDP. Fluxos e seus handshakes estão sempre entre a origem e a instância VM. Para solucionar melhor os problemas dos cenários de protocolo TCP, é possível usar contadores de pacotes SYN para entender quantas tentativas de conexão TCP são feitas. A métrica informa o número de pacotes SYN do TCP que foram recebidos.|
| Conexões SNAT | O Load Balancer Standard relata o número de fluxos de saída mascarados para o front-end do endereço IP Público. As portas SNAT são um recurso esgotável. Essa métrica pode dar uma indicação do grau de dependência que seu aplicativo tem do SNAT para fluxos com origem externa.  Contadores para fluxos SNAT de saída bem-sucedidos e com falha são relatados e podem ser usados para solucionar problemas e entender a integridade dos fluxos de saída.|
| Contadores de bytes | O Load Balancer Standard informa os dados processados por front-end.|
| Contadores de pacotes | O Load Balancer Standard informa os pacotes processados por front-end.|

### <a name = "highreliability"></a>Alta confiabilidade

Configure as regras de balanceamento de carga para dimensionar seu aplicativo e ser altamente confiável. É possível configurar regras para portas individuais ou usar as Portas HA para balancear todo o tráfego, independentemente do número da porta TCP ou UDP.  

É possível usar o novo recurso de Portas HA para desbloquear uma variedade de cenários, incluindo alta disponibilidade e escala para NVAs internos. O recurso é útil para outros cenários em que é impraticável ou indesejável especificar portas individuais. As Portas HA fornecem redundância e escala permitindo várias instâncias quantas sejam necessárias. Sua configuração não está mais restrita a cenários ativos/passivos. As configurações do investigação de integridade protegem seu serviço encaminhando o tráfego apenas às instâncias íntegras.

Os fornecedores NVA podem fornecer cenários resilientes e com suporte total do fornecedor para seus clientes. O ponto único de falha é removido e há suporte para várias instâncias ativas para escala. É possível dimensionar duas ou mais instâncias, dependendo dos recursos do seu dispositivo. Contate o fornecedor NVA para obter mais diretrizes para esses cenários.

### <a name = "availabilityzones"></a>Zonas de disponibilidade

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

Aumente a resiliência de seu aplicativo com as Zonas de disponibilidade nas regiões com suporte. No momento, as Zonas de disponibilidade estão em versão prévia em regiões específicas e exigem aceitação adicional.

### <a name="automatic-zone-redundancy"></a>Redundância de zona automática

É possível escolher se o Load Balancer deve fornecer um front-end com redundância de zona ou zonal para cada um dos seus aplicativos. É fácil criar redundância de zona com o Load Balancer Standard. Um único endereço IP de front-end tem redundância de zona automaticamente. Um front-end com redundância de zona é atendido por todas as zonas de disponibilidade em uma região simultaneamente. Um caminho de dados com redundância de zona é criado para conexões de entrada e de saída. A redundância de zona no Azure não exige vários endereços IP e registros DNS. 

A redundância de zona está disponível para front-ends públicos ou internos. Seu Endereço IP Público e o IP privado de front-end do seu Load Balancer interno pode ter redundância de zona.

Use o seguinte script para criar um endereço IP Público com redundância de zona para seu Load Balancer interno. Se você estiver usando modelos do Resource Manager existentes em sua configuração, adicione a seção **sku** a esses modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Use o seguinte script para criar um endereço IP de front-end com redundância de zona para seu Load Balancer interno. Se você estiver usando modelos do Resource Manager existentes em sua configuração, adicione a seção **sku** a esses modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Se seu front-end do IP Público tiver redundância de zona, as conexões de saída realizadas de instâncias VM terão automaticamente redundância de zona. O front-end está protegido contra falha de zona. A alocação da porta SNAT também sobrevive à falha de zona.

#### <a name="cross-zone-load-balancing"></a>Balanceamento de carga entre zonas

O balanceamento de carga entre zonas está disponível dentro de uma região para o pool de back-ends e oferece máxima flexibilidade para suas instâncias VM. Um front-end entrega fluxos para qualquer VM na rede virtual, independentemente da Zona de disponibilidade da instância VM.

Também é possível especificar uma zona particular para suas instâncias de front-end e de back-end, a fim de alinhar o caminho de dados e os recursos com uma zona específica.

As redes virtuais e sub-redes nunca são restritas por uma zona. Basta definir um pool de back-ends com suas instâncias VM desejadas e sua configuração estará concluída.

#### <a name="zonal-deployments"></a>Implantações em zona

Como opção, é possível alinhar o front-end do balanceador de carga a uma zona específica definindo um front-end zonal. Um front-end zonal é atendido apenas pela Zona de disponibilidade única designada. Quando o front-end é combinado com instâncias VM zonais, é possível alinhar os recursos a regiões específicas.

Um endereço IP Público criado em uma zona específica existe sempre apenas nessa zona. Não é possível alterar a zona de um endereço IP público. Para um endereço IP Público que pode ser anexado a recursos em várias zonas, em vez disso, crie um IP Público com redundância de zona.

Use o script a seguir para criar um endereço IP Público zonal na Zona de disponibilidade 1. Se você estiver usando modelos do Resource Manager existentes em sua configuração, adicione a seção **sku** a esses modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Use o script a seguir para criar um front-end do Load Balancer interno na Zona de disponibilidade 1.

Se você estiver usando modelos do Resource Manager existentes em sua configuração, adicione a seção **sku** a esses modelos. Além disso, defina a propriedade **zonas** na configuração do IP de front-end para o recurso filho.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Adicione o balanceamento de carga entre zonas para seu pool de back-ends colocando suas instâncias VM que estão em uma rede virtual no pool.

O recurso de Load Balancer Standard será sempre regional e com redundância de zona quando houver suporte para Zonas de disponibilidade. É possível implantar um endereço IP Público ou um front-end do Load Balancer interno que não tenha uma zona atribuída em nenhuma região. O suporte para Zonas de disponibilidade não afeta a capacidade da implantação. Se uma região receber Zonas de disponibilidade posteriormente, os IPs públicos ou front-ends do Load Balancer interno implantados anteriormente terão redundância de zona automaticamente. Um caminho de dados com redundância de zona não implica 0% de perda de pacotes.

### <a name = "nsg"></a>Grupos de Segurança de Rede

O Load Balancer Standard e o IP Público Standard são totalmente integrados à rede virtual, que requer o uso de NSGs (Grupos de Segurança de Rede). Os NSGs possibilitam adicionar o fluxo de tráfego à lista de permissões. É possível usar NSGs para ganhar controle total sobre o tráfego para a sua implantação. Não é mais necessário aguardar a conclusão de outros fluxos de tráfego.

Associe os NSGs a sub-redes ou a NICs (interfaces de rede) das instâncias VM no pool de back-ends. Use essa configuração com o Load Balancer Standard e o IP Público Standard quando ele for usado como um IP Público no nível da instância. O NSG deve adicionar explicitamente o tráfego que você deseja permitir à lista de permissões para que esse tráfego flua.

Para saber mais sobre NSGs e como aplicá-los para seu cenário, consulte [Grupos de segurança de rede no Azure](../virtual-network/virtual-networks-nsg.md).

### <a name ="outboundconnections"></a>Conexões de saída

O Load Balancer Standard oferece conexões de saída para VMs que estão dentro da rede virtual quando um balanceador de carga usa o SNAT de representação de porta. O algoritmo SNAT da representação de porta oferece maiores robustez e escala.

Quando um recurso público do Load Balancer estiver associado a instâncias VM, cada fonte de conexão de saída será reescrita. A origem foi reescrita do espaço do endereço IP privado da rede virtual para o endereço IP Público de front-end do balanceador de carga.

Quando são usadas conexões de saída com um front-end com redundância de zona, as conexões também têm redundância de zona e as alocações de porta SNAT sobrevivem à falha da zona.

O novo algoritmo no Load Balancer Standard pré-aloca as portas SNAT para a NIC de cada VM. Quando uma NIC é adicionada ao pool, as portas SNAT são pré-alocadas com base no tamanho do pool. A tabela a seguir mostra as pré-alocações de porta para seis níveis de tamanhos do pool de back-ends:

| Tamanho do pool (instâncias VM) | Número pré-alocado de portas SNAT |
| --- | --- |
| 1 – 50 | 1024 |
| 51 – 100 | 512 |
| 101 – 200 | 256 |
| 201 – 400 | 128 |
| 401 – 800 | 64 |
| 801 – 1.000 | 32 |

As portas SNAT não são convertidas diretamente no número de conexões de saída. Uma porta SNAT pode ser reutilizada para vários destinos exclusivos. Para obter detalhes, examine o artigo [Conexões de saída](load-balancer-outbound-connections.md).

Se o tamanho do pool de back-ends aumentar e fizer a transição para um nível mais alto, metade das suas portas alocadas serão recuperadas. As conexões associadas a um tempo limite de porta recuperada e devem ser reestabelecidas. As novas tentativas de conexão têm êxito imediatamente. Se o tamanho do pool de back-ends diminuir e fizer transição para uma camada mais baixa, o número de portas SNAT disponíveis aumentará. Nesse caso, as conexões existentes não são afetadas.

O Load Balancer Standard tem uma opção de configuração adicional que pode ser usada por regra. É possível controlar qual front-end será usado para o SNAT de representação de porta quando vários front-ends estiverem disponíveis.

Quando apenas o Load Balancer Standard atender instâncias VM, as conexões SNAT de saída não estarão disponíveis. É possível restaurar essa capacidade explicitamente atribuindo também as instâncias VM a um balanceador de carga público. Também é possível atribuir IPs públicos diretamente como IPs públicos no nível da instância a cada instância VM. Essa opção de configuração pode ser necessária para alguns cenários de aplicativo e de sistema operacional. 

### <a name="port-forwarding"></a>Encaminhamento de porta

Os Load Balancers Básico e Standard fornecem a capacidade de configurar regras NAT de entrada para mapear uma porta de front-end para uma instância individual de back-end. Ao configurar essas regras, é possível expor pontos de extremidade do Protocolo RDP e pontos de extremidade SSH ou executar outros cenários de aplicativo.

O Load Balancer Standard continua oferecendo a capacidade de encaminhamento de porta por meio de regras NAT de entrada. Quando usadas com front-ends com redundância de zona, as regras NAT de entrada têm redundância de zona e sobrevivem à falha da zona.

### <a name="multiple-front-ends"></a>Vários front-ends

Configure vários front-ends para obter flexibilidade de design em que os aplicativos exigem a exposição de vários endereços IP individuais, como sites TLS ou pontos de extremidade do Grupos de Disponibilidade AlwaysOn do SQL. 

O Load Balancer Standard continua oferecendo vários front-ends em que é necessário expor um ponto de extremidade do aplicativo específico em um endereço IP exclusivo.

Para obter mais informações sobre como configurar vários IPs de front-end, consulte [Multiple IP configuration](load-balancer-multivip-overview.md) (Configuração IP múltiplo).

## <a name = "sku"></a>Sobre SKUs

Os SKUs só estão disponíveis no modelo de implantação do Azure Resource Manager. Esta versão prévia apresenta dois SKUs para o Load Balancer e para os recursos de IP Público: Básico e Standard. Os SKUs diferem em alguns comportamentos intrínsecos, características de desempenho, limitações e capacidades. As máquinas virtuais podem ser usadas com qualquer SKU. Para os recursos do Load Balancer e do IP Público, os SKUs permanecem atributos opcionais. Quando os SKUs são omitidos em uma definição de cenário, o padrão da configuração é usar o SKU Básico.

>[!IMPORTANT]
>O SKU de um recurso não pode ser trocado. Você não pode alterar a SKU de um recurso existente.  

### <a name="load-balancer"></a>Balanceador de carga

O [recurso existente do Load Balancer](load-balancer-overview.md) se torna o SKU Básico e permanece geralmente disponível e inalterado.

O SKU do Load Balancer Standard é novo e está, no momento, em versão prévia. A versão da API de 1º de agosto de 2017 para Microsoft.Network/loadBalancers adiciona a propriedade **sku** à definição do recurso:

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
O Load Balancer Standard tem resiliência de zona automaticamente em regiões que oferecem Zonas de disponibilidade. Se o Load Balancer tiver sido declarado zonal, então ele não terá resiliência de zona automaticamente.

### <a name="public-ip"></a>IP público

O [recurso de IP Público existente](../virtual-network/virtual-network-ip-addresses-overview-arm.md) se torna o SKU Básico e permanece disponível para todos com todas as suas habilidades, características de desempenho e limitações.

O SKU do IP Público Standard é novo e está, no momento, em versão prévia. A versão da API de 1º de agosto de 2017 para Microsoft.Network/publicIPAddresses adiciona a propriedade **sku** à definição do recurso:

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Ao contrário do IP Público Básico, que oferece vários métodos de alocação, o IP Público Standard sempre usa a alocação estática.

O IP Público Standard tem resiliência de zona automaticamente em regiões que oferecem Zonas de disponibilidade. Se o IP Público tiver sido declarado zonal, então ele não terá resiliência de zona automaticamente. Um IP Público zonal não pode ser alterado de uma zona para outra.

## <a name="migration-between-skus"></a>Migração entre SKUs

Os SKUs não são mutáveis. Siga as etapas nesta seção para mover de um SKU de recurso para outro.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrar do SKU Básico para Standard

1. Crie um novo recurso Standard (Load Balancer e IPs Públicos, conforme necessário). Recrie suas regras e definições de investigação.

2. Remova os recursos de SKU Básico (Load Balancer e IPs Públicos, conforme aplicável) de todas as instâncias VM. Certifique-se também de remover todas as instâncias VM de um conjunto de disponibilidade.

3. Anexe todas as instâncias de VM aos novos recursos de SKU Standard.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrar do SKU Standard para o Básico

1. Crie um novo recurso Básico (Load Balancer e IPs Públicos, conforme necessário). Recrie suas regras e definições de investigação. 

2. Remova os recursos de SKU Standard (Load Balancer e IPs Públicos, conforme aplicável) de todas as instâncias VM. Certifique-se também de remover todas as instâncias VM de um conjunto de disponibilidade.

3. Anexe todas as instâncias de VM aos novos recursos de SKU Básico.

>[!IMPORTANT]
>
>Há limitações com relação ao uso dos SKUs Básico e Standard.
>
>As Portas HA e o Diagnóstico do SKU Standard só estão disponíveis no SKU Standard. Não é possível migrar do SKU Standard para o SKU Básico e também manter esses recursos.
>
>SKUs correspondentes devem ser usados para os recursos do Load Balancer e do IP Público. Não é possível ter uma combinação de recursos de SKU Básico e de SKU Standard. Não é possível anexar uma VM, VMs em um Conjunto de disponibilidade ou um conjunto de dimensionamento de máquinas virtuais aos dois SKUs simultaneamente.
>

## <a name="region-availability"></a>Disponibilidade de região

O Load Balancer Standard está disponível atualmente nestas regiões:
- Leste dos EUA 2
- Centro dos EUA
- Norte da Europa
- Centro-Oeste dos EUA
- Europa Ocidental
- Sudeste Asiático

## <a name="sku-service-limits-and-abilities"></a>Limites de serviço e capacidades do SKU

Os [Limites de serviço de rede](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits) do Azure se aplicam de acordo com a região e com a assinatura. 

A tabela a seguir compara os limites e as capacidades dos SKUs Básico e Standard do Load Balancer:

| Balanceador de carga | Basic | Standard |
| --- | --- | --- |
| Tamanho do pool de back-end | até 100 | até 1.000 |
| Limite do pool de back-end | Conjunto de disponibilidade | rede virtual, região |
| Design do pool de back-end | VMs no Conjunto de disponibilidade, conjunto de dimensionamento de máquinas virtuais no Conjunto de disponibilidade | Qualquer instância VM na rede virtual |
| Portas de alta disponibilidade | Sem suporte | Disponível |
| Diagnostics | Limitado, somente público | Disponível |
| Disponibilidade do VIP  | Sem suporte | Disponível |
| Mobilidade rápida de IP | Sem suporte | Disponível |
|Cenários de Zonas de disponibilidade | Somente zonal | Zonal, com redundância de zona, balanceamento de carga entre zonas |
| Algoritmo SNAT de saída | Sob demanda | Pré-alocada |
| Seleção de front-end de SNAT de saída | Não é configurável, vários candidatos | Configuração opcional para reduzir candidatos |
| Grupo de Segurança de Rede | Opcional na sub-rede/NIC | Obrigatório |

A tabela a seguir compara os limites e as capacidades dos SKUs Básico e Standard do IP Público:

| IP público | Basic | Standard |
| --- | --- | --- |
| Cenários de Zonas de disponibilidade | Somente zonal | Redundância de zona (padrão), zonal (opcional) | 
| Mobilidade rápida de IP | Sem suporte | Disponível |
| Disponibilidade do VIP | Sem suporte | Disponível |
| Contadores | Sem suporte | Disponível |
| Grupo de Segurança de Rede | Opcional na NIC | Obrigatório |


## <a name="preview-sign-up"></a>Inscrição na versão prévia

Para participar da versão prévia do SKU do Load Balancer Standard e seu SKU de IP Público Standard complementar, registre sua assinatura.  Registrar sua assinatura oferece acesso do PowerShell ou CLI do Azure 2.0. Para se registrar, siga estas etapas:

>[!NOTE]
>O registro do recurso Standard do Load Balancer pode levar até uma hora para entrar em vigor globalmente. Se desejar usar o Padrão do Load Balancer com [Zonas de disponibilidade](https://aka.ms/availabilityzones) e [Portas de HA](https://aka.ms/haports), uma inscrição separada será necessária para essas visualizações. Siga as respectivas instruções para a inscrição desses recursos.

### <a name="sign-up-by-using-azure-cli-20"></a>Inscrever-se usando a CLI do Azure 2.0

1. Registre o recurso com o provedor:

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. Essa operação pode demorar até 10 minutos para ser concluída. É possível verificar o status da operação com o comando a seguir:

    ```cli
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```
    
    Passe para a próxima etapa quando o estado de registro do recurso retornar 'Registrado':
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowLBPreview",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. Conclua a inscrição da versão prévia registrando novamente sua assinatura com o provedor de recursos:

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-by-using-powershell"></a>Inscrever-se usando o PowerShell

1. Registre o recurso com o provedor:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. Essa operação pode demorar até 10 minutos para ser concluída. É possível verificar o status da operação com o comando a seguir:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    Passe para a próxima etapa quando o estado de registro do recurso retornar 'Registrado':
   
    ```
    FeatureName      ProviderName        RegistrationState
    -----------      ------------        -----------------
    AllowLBPreview   Microsoft.Network   Registered
    ```
    
3. Conclua a inscrição da versão prévia registrando novamente sua assinatura com o provedor de recursos:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
 
## <a name="pricing"></a>Preços

A cobrança do SKU do Load Balancer Standard é baseada em regras configuradas e dados processados. Nenhum encargo é gerado durante o período de versão prévia. Para obter mais informações, examine as páginas de preço do [Load Balancer](https://aka.ms/lbpreviewpricing) e do [IP Público](https://aka.ms/lbpreviewpippricing).

Os clientes continuam a desfrutar do SKU do Load Balancer Básico sem custo adicional.

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam no momento da versão prévia e estão sujeitas a alterações:

- No momento, as instâncias de back-end do Load Balancer não podem ser localizadas em redes virtuais emparelhadas. Todas as instâncias de back-end devem estar na mesma região.
- Os SKUs não são mutáveis. Você não pode alterar a SKU de um recurso existente.
- Os dois SKUs podem ser usados com uma VM autônoma, instâncias VM em um Conjunto de disponibilidade ou um conjunto de dimensionamento de máquinas virtuais. As combinações de VM não podem ser usadas com ambos os SKUs simultaneamente. Não é permitida uma configuração que contém uma combinação de SKUs.
- Usar um Load Balancer Standard interno com uma instância VM (ou qualquer parte de um Conjunto de disponibilidade) desabilita [conexões de saída SNAT padrão](load-balancer-outbound-connections.md). É possível restaurar essa capacidade para uma VM autônoma, instâncias VM em um Conjunto de disponibilidade ou um conjunto de dimensionamento de máquinas virtuais. Também é possível restaurar a capacidade de criar conexões de saída. Para restaurar essas capacidades, atribua simultaneamente um Load Balancer Standard público ou um IP Público Standard como IP Público no nível da instância, à mesma instância VM. Após a conclusão da atribuição, o SNAT de representação de porta para um endereço IP Público é fornecido novamente.
- Talvez seja necessário agrupar instâncias VM em conjuntos de disponibilidade para alcançar a escala de pool de back-ends completa. É possível posicionar até 150 conjuntos de disponibilidade e VMs autônomas em um único pool de back-ends.
- Não há suporte para IPv6.
- No contexto das Zonas de disponibilidade, um front-end não pode ser alternado de zonal para com redundância de zona ou vice-versa. Depois que um front-end é criado como sendo com redundância de zona, ele permanecerá com redundância de zona. Depois que um front-end é criado como zonal, ele permanecerá zonal.
- No contexto das Zonas de disponibilidade, um endereço IP Público zonal não pode ser movido de uma zona para outra.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Load Balancer Básico](load-balancer-overview.md).
- Saiba mais sobre as [Zonas de disponibilidade](../availability-zones/az-overview.md).
- Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) no Azure.

