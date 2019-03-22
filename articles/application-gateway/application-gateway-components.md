---
title: Componentes do Gateway de aplicativo do Azure
description: Este artigo fornece informações sobre os vários componentes no Gateway de aplicativo
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 44c8b331ebb258c39a003c91e0711e6dfb87cb12
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076285"
---
# <a name="application-gateway-components"></a>Componentes do gateway de aplicativo

 Um gateway de aplicativo serve como o único ponto de contato para clientes. Ele distribui o tráfego de aplicativo em vários pools de back-end, como VMs do Azure, conjuntos de dimensionamento de máquinas virtuais, serviços de aplicativos ou servidores no local/externa. Para fazer isso, ele usa vários componentes descritos neste artigo.

![componentes do gateway de aplicativo](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-address"></a>Endereço IP de front-end

Um endereço IP de front-end é o endereço IP associado com o gateway de aplicativo. Você pode configurar o gateway de aplicativo para ter um endereço IP público ou um endereço IP privado ou ambos. Há suporte para apenas um endereço IP público ou um endereço IP privado. Sua rede virtual e o endereço IP público devem estar no mesmo local que o gateway de aplicativo.

Um endereço IP de Frontend é associado a um *ouvinte* após sua criação. 

### <a name="static-vs-dynamic-public-ip-address"></a>Portas estáticas vs dinâmico endereço IP

O SKU v1 dá suporte a endereços IP internos estáticos, mas não oferece suporte a endereços IP públicos estáticos. O VIP poderá ser alterado se o Gateway de Aplicativo for interrompido e iniciado. O nome DNS associado ao Gateway de Aplicativo não é alterado ao longo do ciclo de vida do gateway. Por esse motivo, é recomendado usar um alias do CNAME e apontá-lo para o endereço DNS do Gateway de Aplicativo.

O SKU do Gateway de aplicativo v2 dá suporte a endereços IP públicos estáticos, bem como os endereços IP internos estáticos. 

## <a name="listeners"></a>Ouvintes

Antes de começar a usar o gateway de aplicativo, você deve adicionar um ou mais ouvintes. Um ouvinte é uma entidade lógica que verifica se há solicitações de conexão de entrada e aceita as solicitações se o protocolo, a porta, o host e o endereço IP associado com a correspondência de solicitação com o protocolo, a porta, o host e o endereço IP associado a configuração de ouvinte. Pode haver vários ouvintes anexados a um gateway de aplicativo e elas podem ser usadas para o mesmo protocolo. Depois que o ouvinte detecta solicitações de entrada dos clientes, o Gateway de aplicativo encaminha essas solicitações para os membros no pool de back-end, usando as regras de roteamento de solicitação que você define para o ouvinte que recebeu a solicitação de entrada.

Ouvintes dão suporte a portas e protocolos a seguir:

### <a name="ports"></a>Portas

Isso é a porta na qual o ouvinte está escutando para a solicitação do cliente. Você pode configurar portas, variando de 1 a 65502 para 1 para 65199 e SKU de V1 para V2 SKU.

### <a name="protocols"></a>Protocolos

O gateway de aplicativo dá suporte a quatro protocolos a seguir: HTTP, HTTPS, HTTP/2 e WebSocket

Você especificar explicitamente a escolha entre os protocolos HTTP e HTTPS na configuração de ouvinte. O [suporte para os protocolos de WebSockets e HTTP/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) fornecidos de forma nativa. [Suporte ao WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) é habilitado por padrão. Não há nenhuma configuração configurável pelo usuário para habilitar ou desabilitar seletivamente o suporte ao WebSocket. Você pode usar WebSockets com ouvintes HTTP e HTTPS. O suporte ao protocolo HTTP/2 está disponível para os clientes que se conectam apenas os ouvintes do Gateway de Aplicativo. A comunicação para pools de servidores back-end é sobre HTTP/1.1. Por padrão, o suporte HTTP/2 está desabilitado. Você pode optar por habilitá-lo.

Você pode usar um ouvinte HTTPS para a terminação SSL. Um ouvinte HTTPS descarrega a criptografia e descriptografia funcionam para o gateway de aplicativo para que seus servidores web não estão sobrecarregados por essa sobrecarga. Seus aplicativos, em seguida, são livres para se concentrar na lógica comercial.

### <a name="custom-error-pages"></a>Páginas de erro personalizadas

O gateway de aplicativo permite que você crie páginas de erro personalizadas em vez de exibir páginas de erro padrão. Você pode usar sua própria identidade visual e layout em uma página de erro personalizada. O Gateway de Aplicativo pode exibir uma página de erro personalizada quando uma solicitação não pode acessar o back-end. Para obter mais informações, consulte [páginas de erro personalizado para seu Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Tipos de ouvintes

Há dois tipos de ouvintes:

- **Básico**: Esse tipo de ouvinte escuta em um site único de domínio em que ele tem um único mapeamento de DNS para o endereço IP do gateway de aplicativo. Essa configuração de ouvinte é necessária quando você estiver hospedando um único site por trás de um gateway de aplicativo.
- **Multissite**: Essa configuração de ouvinte é necessária quando você configurar mais de um aplicativo web na mesma instância de gateway de aplicativo. Isso permite que você configure a topologia mais eficiente para suas implantações adicionando até 100 sites da Web para um gateway de aplicativo. Cada site pode ser direcionado para seu próprio pool de back-end. Por exemplo:   Para três subdomínios — abc.contoso.com, xyz.contoso.com e pqr.contoso.com apontando para o endereço IP do gateway de aplicativo. Criar três ouvintes do tipo *multissite* e configurar cada ouvinte para a porta do respectiva e de configuração do protocolo. Para obter mais informações, consulte [hospedagem de vários sites](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Depois de criar um ouvinte, você pode associá-lo com uma regra de roteamento de solicitação que determina como a solicitação recebida no ouvinte deve ser roteada para o back-end.

Os ouvintes são processados na ordem em que eles são mostrados. Por esse motivo, se um ouvinte básico corresponder a uma solicitação de entrada ele a processará primeiro. Ouvintes multissite devem ser configurados antes de um ouvinte básico para assegurar que o tráfego seja roteado para o back-end correto.

## <a name="request-routing-rule"></a>Regra de roteamento de solicitação

Isso é o componente mais importante do gateway de aplicativo e determina como o tráfego em que o ouvinte associado a essa regra é roteado. A regra vincula o ouvinte, pool de servidores de back-end e as configurações de HTTP de back-end. Depois que uma solicitação é aceita por um ouvinte, a regra de roteamento de solicitação determina se a solicitação deve ser encaminhado para o back-end ou redirecionado em outro lugar. Se for determinada que a solicitação ser encaminhada para o back-end, a regra de roteamento de solicitação define qual pool de servidores de back-end deve ser encaminhado para. Além disso, a regra de roteamento de solicitação também determina se os cabeçalhos na solicitação devem ser reescrito. Um ouvinte pode ser anexado a apenas uma regra.

Pode haver dois tipos de regras de roteamento de solicitação:

- **Básica:** Todas as solicitações no ouvinte associado (por exemplo: blog.contoso.com/*) são encaminhadas para o pool de back-end associados usando a configuração de HTTP associada.
- **Path-based:** Esse tipo de regra permite rotear as solicitações no ouvinte associado a um pool de back-end específico com base na URL na solicitação. Se o caminho da URL em uma solicitação corresponde ao padrão de caminho em uma regra com base em caminho, a solicitação é encaminhada usando essa regra. O padrão de caminho é aplicado somente para o caminho da URL, não para seus parâmetros de consulta. Se o caminho da URL de uma solicitação de um ouvinte não corresponde a nenhuma das regras com base no caminho, a solicitação é encaminhada para o *padrão* pool de back-end e o *padrão* configurações de HTTP. Para obter mais informações, consulte [roteamento baseado em URL](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Suporte para redirecionamento

A regra de roteamento de solicitação também permite que você redirecione o tráfego no gateway de aplicativo. Esse é um mecanismo de redirecionamento genérico; portanto, você pode redirecionar bidirecionalmente em qualquer porta definida usando regras. Escolha o destino de redirecionamento a ser outro ouvinte (que pode ajudar a habilitar automático de HTTP para redirecionamento a HTTPS) ou um site externo, escolha o redirecionamento temporário ou permanente ou escolha acrescentando a cadeia de caracteres de consulta e o caminho URI à URL redirecionada. Para obter mais informações, consulte [redirecionar o tráfego em seu Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="rewrite-http-headers"></a>Reescrever cabeçalhos HTTP

Usando as regras de roteamento de solicitação você pode adicionar, remover ou atualizar HTTP (S) de solicitação e cabeçalhos de resposta enquanto a solicitação e pacotes de resposta se mover entre os pools de back-end e de cliente, por meio do gateway de aplicativo. Os cabeçalhos não só podem ser definidos com valores estáticos, mas também a outros cabeçalhos e as variáveis de servidor importantes. Isso ajudará você a realizar vários casos de uso importante, como extrair o endereço IP de clientes, removendo informações confidenciais sobre o back-end, adicionar medidas de segurança adicionais, etc. Para obter mais informações, consulte [cabeçalhos de HTTP de reescrita em seu gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>Configurações de HTTP

As rotas de gateway de aplicativo do tráfego para o back-end (especificados na regra de roteamento de solicitação para o qual as configurações de HTTP são anexadas) de servidores usando o número da porta, protocolo e outras configurações especificadas neste componente. A porta e protocolo usado nas configurações de HTTP determinam se o tráfego entre os servidores de gateway e o back-end do aplicativo é criptografado, assim a realização de SSL de ponta a ponta, ou sem criptografia. Esse componente também é usado para: determinar se uma sessão de usuário deve ser mantido no mesmo servidor usando o [afinidade de sessão baseada em cookie](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity), realizar a remoção normal de membros do pool de back-end usando [conexão Drenagem](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining), associar a investigação personalizada para monitorar a integridade de back-end, defina o intervalo de tempo limite de solicitação, substitua o nome de host e o caminho na solicitação e fornecer a facilidade de um clique para especificar a configuração de back-end para back-end de serviço de aplicativo. 

## <a name="backend-pool"></a>Pool de back-end

O pool de back-end é usado para rotear solicitações para os servidores de back-end, que irá atender à solicitação. Pools de back-end podem ser formados por NICs, dimensionamento de máquinas virtuais conjuntos, pública IP endereços, interno IP endereços, FQDN e multilocatário back-ends, como o serviço de aplicativo do Azure. Membros do pool de back-end de gateway de aplicativo não estão vinculados a um conjunto de disponibilidade. O Gateway de aplicativo pode se comunicar com instâncias fora da rede virtual que está em, portanto, os membros dos pools de back-end podem ser entre clusters, data centers ou fora do Azure, desde que exista conectividade IP. Se você planeja usar IPs internos como membros de pool de back-end, será necessário usar o [Emparelhamento VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ou o [Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Emparelhamento VNet tem suporte e pode ser útil para balanceamento de carga de tráfego em outras redes virtuais. O gateway de aplicativo também pode comunicar com a servidores locais quando eles estão conectados por túneis VPN ou ExpressRoute, desde que o tráfego é permitido.

Você pode criar pools de back-end diferente para diferentes tipos de solicitações. Por exemplo, crie um pool de back-end para solicitações gerais e outro pool de back-end para solicitações para os microsserviços para seu aplicativo.

## <a name="health-probes"></a>Investigações de integridade

Por padrão o gateway de aplicativo monitora a integridade de todos os recursos em seu pool de back-end e remove automaticamente qualquer recurso considerado não íntegro do pool. Ele continua a monitorar as instâncias não íntegras e adiciona de volta ao pool de back-end Íntegro depois que eles se tornarem disponíveis e respondem a investigações de integridade. Além de usar o monitoramento da investigação de integridade padrão, você também pode personalizar a investigação de integridade para atender às necessidades do seu aplicativo. Investigações personalizadas permitem que você tenha um controle mais granular sobre o monitoramento de integridade. Ao usar investigações personalizadas, você pode configurar o intervalo de investigação, a URL e caminho a testar e quantas respostas com falha devem aceitar antes da instância do pool de back-end é marcada como não íntegra. É altamente recomendável que você configure as investigações personalizadas para monitorar a integridade de cada pool de back-end. Para obter mais informações, consulte [monitorar a integridade do seu gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre os componentes do Gateway de aplicativo, você pode:
* [Criar um Gateway de aplicativo no portal do Azure](quick-create-portal.md)
* [Criar um Gateway de aplicativo usando o Azure PowerShell](quick-create-powershell.md)
* [Criar um Gateway de aplicativo usando a CLI do Azure](quick-create-cli.md)
