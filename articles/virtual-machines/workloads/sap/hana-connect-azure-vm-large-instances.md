---
title: Configuração de conectividade de máquinas virtuais para o SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Configuração de conectividade de máquinas virtuais para usar o SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0e09e6dfce5d0ede525f461193866219b7f9429
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167579"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Conectar máquinas virtuais do Azure a Grandes Instâncias do HANA

Como já foi mencionado em [visão geral do SAP HANA (instâncias grandes) e arquitetura no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) a implantação mínima de instâncias grandes HANA com a camada de aplicativo SAP no Azure parece como:

![Rede virtual do Azure conectado ao SAP HANA no Azure (Instâncias Grandes) e local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Examinando melhor o lado da Rede Virtual do Azure, podemos perceber a necessidade de:

- A definição de uma VNet do Azure que será usada para implantar as VMs da camada de aplicativo do SAP.
- Que significa que a Rede Virtual do Azure, uma sub-rede padrão é definido automaticamente que é usada para implantar as VMs em.
- A Rede Virtual do Azure que é criado deve ter pelo menos uma sub-rede VM e uma sub-rede de Gateway de ExpressRoute. Essas sub-redes devem ser atribuídas aos intervalos de endereços IP, conforme especificado e abordado nas próximas seções.

Portanto, vamos examinar mais detalhadamente a criação de Rede Virtual do Azure para instâncias grandes HANA

## <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Criação da Rede Virtual do Azure para instâncias grandes HANA

>[!Note]
>A Rede Virtual do Azure para instância grande HANA deve ser criada usando o modelo de implantação do Azure Resource Manager. Não há suporte para o modelo de implantação mais antigo do Azure, geralmente conhecido como modelo de implantação clássico, na solução Instância Grande do HANA.

A VNet pode ser criada usando o portal do Azure, o PowerShell, o modelo do Azure ou a CLI do Azure (consulte [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). No exemplo a seguir, analisamos uma VNet criada por meio do portal do Azure.

Se examinamos as definições de uma VNet do Azure pelo portal do Azure, vamos dar uma olhada em algumas das definições e como elas se relacionam ao que listamos como diferentes intervalos de endereços IP. Quando falamos sobre o **Espaço de Endereço**, queremos dizer o espaço de endereço que a VNet do Azure tem permissão para usar. Esse espaço de endereço também é o intervalo de endereços usado pela VNet para a propagação de rotas BGP. Esse **Espaço de Endereço** pode ser visto aqui:

![Espaço de Endereço da VNet do Azure exibido no portal do Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

No caso anterior, com 10.16.0.0/16, a VNet do Azure recebeu um intervalo de endereços IP bem grande e amplo para ser usado. Significa que todos os intervalos de endereços IP das sub-redes subsequentes nessa rede virtual podem ter seus intervalos dentro desse ‘Espaço de Endereço'. Geralmente, não recomendamos tal um intervalo de endereços grande para a rede virtual único no Azure. Mas obter um passo adiante, vamos examinar as sub-redes definidas na Rede Virtual do Azure:

![Sub-redes de Rede Virtual do Azure e seus intervalos de endereços IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Como você pode ver, examinamos uma VNet com uma primeira sub-rede VM (aqui chamado “padrão”) e uma sub-rede chamada “GatewaySubnet”.
Na seção a seguir, nos referimos ao intervalo de endereços IP da sub-rede, que foi chamado “padrão” nos gráficos como **intervalo de endereços IP da sub-rede VM do Azure**. Nas seções a seguir, nos referimos ao intervalo de endereços IP da sub-rede de Gateway como **intervalo de endereços IP de sub-rede de Gateway de rede virtual**. 

No caso do demonstrado acima de dois gráficos, verá que o **Espaço de Endereço de rede virtual** aborda ambos, o **intervalo de endereços IP de sub-rede de VM do Azure** e **intervalo de endereços IP de sub-rede de Gateway de rede virtual**. 

Em outros casos em que você precisa conservar ou ser específico com os intervalos de endereços IP, restrinja o **Espaço de Endereço da VNet** de uma VNet aos intervalos específicos que estão sendo usados por cada sub-rede. Nesse caso, você pode definir o **Espaço de Endereço de rede virtual** de uma rede virtual específica como vários intervalos como mostrado aqui:

![Espaço de Endereço de Rede Virtual do Azure com dois espaços](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Nesse caso, o **Espaço de Endereço da VNet** tem dois espaços definidos. Esses dois espaços são idênticos aos intervalos de endereços IP definidos para o **intervalo de endereços IP da sub-rede VM do Azure** e o **intervalo de endereços IP da Sub-rede de Gateway da VNet**.

Você pode usar qualquer padrão de nomenclatura que desejar para essas sub-redes de locatário (sub-redes de VM). No entanto, **sempre deve haver uma, e somente uma, sub-rede de gateway para cada VNet** que se conecta ao circuito do ExpressRoute do SAP HANA no Azure (Instâncias Grandes). **Esta sub-rede de gateway sempre deve ser chamada “GatewaySubnet”** para garantir o posicionamento correto do gateway do ExpressRoute.

> [!WARNING] 
> É fundamental que a sub-rede de gateway seja sempre chamada “GatewaySubnet”.

Várias sub-redes VM podem ser usadas, até mesmo utilizando intervalos de endereços não contíguas. Mas como mencionado anteriormente, esses intervalos de endereços devem ser cobertos pelo **Espaço de Endereço de rede virtual** da rede virtual de forma agregada ou em uma lista dos intervalos exatas de sub-redes de VM e a sub-rede de gateway.

Resumindo o fato importante sobre uma Rede Virtual do Azure que se conecta ao HANA grandes instâncias:

- Você precisa enviar à Microsoft o **Espaço de Endereço da VNet** ao executar uma implantação inicial das Instâncias Grandes do HANA. 
- O **Espaço de Endereço de rede virtual** pode ser um intervalo maior que abrange o intervalo para o intervalo de endereços IP de sub-rede de VM do Azure e o intervalo de endereços IP de sub-rede de Gateway de rede virtual.
- Ou você pode enviar como **Espaço de Endereço de Rede Virtual** vários intervalos que abrangem o IP diferente endereços intervalos de intervalo de endereços IP de sub-rede VM e o intervalo de endereços IP de sub-rede de Gateway de rede virtual.
- O prefixo **Espaço de Endereço de rede virtual** é usada propagação de roteamento do BGP.
- O nome da sub-rede de Gateway deve ser: **“GatewaySubnet”**.
- O **Espaço de Endereço de rede virtual** é usado como um filtro no lado do HANA grande instância para permitir ou impedir que o tráfego para as unidades de instância grande HANA do Azure. Se as informações de roteamento BGP da VNet do Azure e os intervalos de endereços IP configurados para filtragem no lado da Instância Grande do HANA não forem correspondentes, poderão ocorrer problemas na conectividade.
- Há alguns detalhes sobre a sub-rede de Gateway que são discutidas mais para baixo na seção 'Conectando a uma rede virtual para ExpressRoute de instância grande HANA'



## <a name="different-ip-address-ranges-to-be-defined"></a>Diferentes intervalos de endereços IP a serem definidos 

Já apresentamos alguns intervalos de endereços IP necessários para implantar as Instâncias Grandes do HANA nas seções anteriores. Mas há mais alguns intervalos de endereços IP que são importantes. Vamos analisar alguns detalhes adicionais. Os seguintes endereços IP dos quais nem todos precisam ser enviadas para a Microsoft precisam ser definida antes de enviar uma solicitação para a implantação inicial:

- **Espaço de Endereço da VNet:** como já apresentamos anteriormente, é o intervalo de endereços IP atribuído (ou que se pretende atribuir) ao parâmetro do espaço de endereço nas VNets (Redes Virtuais) do Azure que se conectam ao ambiente da Instância Grande do SAP HANA. É recomendável que esse parâmetro do Espaço de Endereço seja um valor de várias linhas composto pelos intervalos da Sub-rede VM do Azure e pelo intervalo da sub-rede de Gateway do Azure, conforme mostrado no gráfico anterior. Esse intervalo não deve sobrepor seu local ou intervalos de endereços do Pool de IP do servidor ou ER P2P. Como obter esses intervalos de endereços IP? A equipe de rede corporativa ou o provedor de serviço deve fornecer um ou vários Intervalos de Endereços IP que não são usados dentro da rede. Exemplo: se a Sub-rede VM do Azure (veja acima) for 10.0.1.0/24 e a Sub-rede de Gateway do Azure (veja abaixo) for 10.0.2.0/28, é recomendável que o Espaço de Endereço da VNet do Azure tenha duas linhas: 10.0.1.0/24 e 10.0.2.0/28. Embora os valores de Espaço de Endereço possam ser agregados, é recomendável correspondê-los aos intervalos de sub-rede, a fim de evitar a reutilização acidental de intervalos de endereços IP não utilizados em espaços de endereço maiores no futuro em outro lugar da rede. **O Espaço de Endereço da VNET é um intervalo de endereços IP, que precisa ser enviado à Microsoft quando uma implantação inicial é solicitada**

- **Intervalo de endereços IP da sub-rede VM do Azure:** esse intervalo de endereços IP, conforme já discutido anteriormente, é aquele que você atribuiu (ou que pretende atribuir) ao parâmetro da sub-rede da VNet do Azure na VNet do Azure que se conecta ao ambiente da Instância Grande do SAP HANA. Este intervalo de endereços IP é usado para atribuir endereços IP para VMs do Azure. Os endereços IP fora desse intervalo têm permissão para se conectar aos servidores da Instância Grande do SAP HANA. Se necessário, podem ser usadas várias sub-redes de VM do Azure. Um bloco CIDR /24 é recomendado pela Microsoft para cada sub-rede de VM do Azure. Este intervalo de endereços deve ser uma parte dos valores usados no Espaço de Endereço de Rede Virtual do Azure. Como obter esse intervalo de endereços IP? A equipe de rede corporativa ou o provedor de serviço deve fornecer um Intervalo de Endereços IP que não estejam sendo usados atualmente na rede.

- **Intervalo de endereços IP de sub-rede de Gateway de rede virtual:** dependendo dos recursos que você planeja usar, o tamanho recomendado seria:
   - Gateway do ExpressRoute de ultra desempenho: bloco de endereço /26 – necessário para a classe do Tipo II de SKUs
   - Coexistência com VPN e ExpressRoute usando um Gateway de ExpressRoute de alto desempenho (ou menor): bloco de endereço /27
   - Todas as outras situações: bloco de endereço /28. Este intervalo de endereços deve ser uma parte dos valores usados nos valores de "Espaço de Endereço de VNET". Este intervalo de endereços deve ser uma parte dos valores usados nos valores de Espaço de Endereço de Rede Virtual do Azure que você precisa para enviar à Microsoft. Como obter esse intervalo de endereços IP? A equipe de rede corporativa ou o provedor de serviço deve fornecer um Intervalo de Endereços IP que não estejam sendo usados atualmente na rede. 

- **Intervalo de endereços para conectividade do ER-P2P:** esse intervalo é o intervalo de IP da conexão P2P do ER (ExpressRoute) da Instância Grande do SAP HANA. Este intervalo de endereços IP deve ser um intervalo de endereços IP CIDR /29. Esse intervalo não deve sobrepor seu local ou outros intervalos de endereços IP do Azure. Esse intervalo de endereços IP é usado para configurar a conectividade do ER do Gateway do ExpressRoute da VNet do Azure com os servidores da Instância Grande do SAP HANA. Como obter esse intervalo de endereços IP? A equipe de rede corporativa ou o provedor de serviço deve fornecer um Intervalo de Endereços IP que não estejam sendo usados atualmente na rede. **Esse intervalo é um intervalo de endereços IP, que precisa ser enviado à Microsoft quando uma implantação inicial é solicitada**
  
- **O intervalo de endereços do Pool do IP do servidor:** este intervalo de endereços IP é usado para atribuir o endereço IP individual para servidores de instância grande HANA. O tamanho recomendado de sub-rede é um /24 CIDR bloquear - mas se necessária pode ser menor em um mínimo de fornecimento de 64 endereços IP. Nesse intervalo, os 30 primeiros endereços IP são reservados para uso pela Microsoft. Verifique se esse fato é considerado ao escolher o tamanho do intervalo. Esse intervalo não deve sobrepor seu local ou outros intervalos de endereços IP do Azure. Como obter esse intervalo de endereços IP? Seu provedor de serviço ou de equipe de rede corporativa deve fornecer um intervalo de endereços IP que não estejam em uso em sua rede. Um bloco CIDR /24 (recomendado) exclusivo para ser usado para atribuir os endereços IP específicos necessários ao SAP HANA no Azure (Instâncias Grandes). **Esse intervalo é um intervalo de endereços IP, que precisa ser enviado à Microsoft quando uma implantação inicial é solicitada**
 
Embora você precise definir e planejar os intervalos de endereços IP acima, nem todos eles precisam ser transmitidos à Microsoft. Para resumir o acima, os intervalos de endereços IP que é necessário nomear à Microsoft são:

- Espaços de Endereço de Rede Virtual do Azure
- Intervalo de endereços para conectividade de ER-P2P
- Intervalo de Endereços do Pool de IPs do Servidor

Adicionar redes virtuais adicionais que precisam se conectar a instâncias grandes HANA, exige que você enviar o novo Espaço de Endereço de Rede Virtual do Azure você está adicionando à Microsoft. 

Veja abaixo um exemplo dos diferentes intervalos e alguns intervalos de exemplo que você precisa configurar e, finalmente, fornecer à Microsoft. Como você pode ver, o valor do Espaço de Endereço da VNet do Azure não é agregado no primeiro exemplo, mas é definido com base nos intervalos do intervalo de endereços IP de sub-rede da primeira VM do Azure e do intervalo de endereços IP de Sub-rede de Gateway da VNet. Usar várias sub-redes VM na VNet do Azure funcione adequadamente, configurando e enviando o IP adicional endereço intervalos das sub-redes de VM adicionais como parte do Espaço de Endereço de Rede Virtual do Azure.

![Intervalos de endereços IP necessários na implantação mínima do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Você também tem a possibilidade de agregar dados que enviar à Microsoft. Nesse caso, o Espaço de Endereço da VNet do Azure só incluirá um espaço. Usando os intervalos de endereços IP usados no exemplo anterior. Esse Espaço de endereço da VNet agregado poderá ser semelhante ao seguinte:

![Segunda possibilidade de intervalos de endereços IP necessários na implantação mínima do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Como você pode ver acima, em vez de dois intervalos menores que definiu o Espaço de Endereço do VNet do Azure, temos um intervalo maior que abrange 4096 endereços IP. Uma definição grande do Espaço de Endereço deixa alguns grandes intervalos não utilizados. Como os valores de Espaço de Endereço de rede virtual são usados para a propagação de rotas BGP, uso de intervalos não usados em locais ou em outro lugar na sua rede pode causar problemas de roteamento. Portanto, é recomendável manter o Espaço de Endereço estreitamente alinhadas com o Espaço de Endereço de sub-rede real usado. Se necessário, sem incorrer em tempo de inatividade para a VNet, você pode sempre adicionar novos valores de Espaço de Endereço posteriormente.
 
> [!IMPORTANT] 
> Todos os intervalos de endereços IP do ER-P2P, do Pool IP do Servidor e do Espaço de Endereço de VNet do Azure **NÃO** devem se sobrepor uns aos outros ou a qualquer outro intervalo usado em outro lugar na rede; cada um deles deve ser discreto e, como os dois gráficos anteriores mostram, não podem ser uma sub-rede de nenhum outro intervalo. Se ocorrerem sobreposições entre intervalos, a Rede Virtual do Azure não poderá se conectar ao circuito de ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Próximas etapas após a definição de intervalos de endereços
Após a definição de intervalos de endereços IP, as seguintes atividades precisam ocorrer:

1. Envie os intervalos de endereços IP para o Espaço de Endereço de Rede Virtual do Azure, a conectividade de ER P2P e o intervalo de endereços do Pool do IP do servidor, junto com outros dados que foi listados no início do documento. Neste momento, você também pode começar a criar a VNet e as Sub-redes VM. 
2. Um circuito do ExpressRoute é criado pela Microsoft entre sua assinatura do Azure e o carimbo de Instâncias Grandes HANA.
3. Uma rede de locatário é criada no carimbo instância grande pela Microsoft.
4. A Microsoft configura a rede na infraestrutura do SAP HANA no Azure (Instâncias Grandes) para aceitar os endereços IP do Espaço de Endereço da VNet do Azure que se comunicam com as Instâncias Grandes do HANA.
5. Dependendo do SKU específico do SAP HANA no Azure (Instâncias Grandes) adquirido, a Microsoft atribui uma unidade de computação em uma rede de locatário, aloca e monta o armazenamento e instala o sistema operacional (SUSE ou Red Hat Linux). Endereços IP para essas unidades são tirados o endereço de Pool de IP do servidor intervalo enviado à Microsoft.

No final do processo de implantação, a Microsoft fornece os seguintes dados para você:
- As informações necessárias para conectar suas Redes Virtuais do Azure ao circuito de ExpressRoute que conecta redes virtuais do Azure a instâncias grandes HANA:
     - Chaves de autorização
     - PeerID de ExpressRoute
- Os dados para acessar Instâncias Grandes HANA depois que você tiver estabelecido o circuito de ExpressRoute e a Rede Virtual do Azure.

Encontre também a sequência de conexão das Instâncias Grandes do HANA no documento [Instalação de ponta a ponta das Instâncias Grandes do SAP HANA](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Muitas das etapas a seguir são mostradas em uma implantação de exemplo nesse documento. 

**Próximas etapas**

- Veja [Conectar uma VNet ao ExpressRoute do HANA em Instância Grande](hana-connect-vnet-express-route.md).