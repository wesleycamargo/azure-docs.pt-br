---
title: Visão geral da configuração de Gateway de aplicativo do Azure
description: Este artigo fornece informações sobre como configurar os vários componentes no Gateway de aplicativo do Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/04/2019
ms.author: absha
ms.openlocfilehash: 515243cb043bac8e9f28a3c63808e4fbd9b8f525
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57905021"
---
# <a name="application-gateway-configuration-overview"></a>Visão geral da configuração de Gateway de aplicativo

O gateway de aplicativo é composto de vários componentes que podem ser configurados de maneiras diferentes para a realização de diferentes cenários. Este artigo orienta como cada componente deve ser configurado.

![componentes do gateway de aplicativo](./media/configuration-overview/configuration-overview1.png)

A imagem de exemplo acima ilustra a configuração de um aplicativo com ouvintes de 3. Duas primeiras são ouvintes multissites `http://acme.com/*` e `http://fabrikam.com/*`, respectivamente. Ambos estão escutando na porta 80. O ouvinte de terceiro é um ouvinte básico com terminação de SSL de ponta a ponta. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Rede virtual do Azure e a sub-rede dedicada

Gateway de aplicativo é uma implementação dedicada em sua rede virtual. Em sua rede virtual, uma sub-rede dedicada é necessária para o gateway de aplicativo. Você pode ter várias instâncias de uma implantação de gateway de aplicativo fornecido nesta sub-rede. Você também pode implantar outros gateways de aplicativo na sub-rede, mas não é possível implantar qualquer outro recurso na sub-rede do gateway de aplicativo.  

> [!NOTE]   
> Combinar Standard_v2 e o Gateway de Aplicativo Standard na mesma sub-rede não tem suporte.

#### <a name="size-of-the-subnet"></a>Tamanho da sub-rede

No caso de SKU do v1, Gateway de aplicativo consome um endereço IP privado por instância, além de outro endereço IP privado se uma configuração de IP privado de front-end está configurada. Além disso, o Azure reservará os quatro primeiros e o último endereço IP em cada sub-rede para uso interno. Por exemplo, se o Gateway de Aplicativo for definido como três instâncias e nenhum IP de front-end privado, o tamanho de sub-rede /29 ou maior será necessário. Nesse caso, o Gateway de Aplicativo usará três endereços IP. Se você tiver três instâncias e um endereço IP para a configuração de IP de front-end privado, um tamanho de sub-rede /28 ou maior será necessário, visto que quatro endereços IP são necessários.

#### <a name="network-security-groups-supported-on-the-application-gateway-subnet"></a>Grupos de segurança de rede com suporte na sub-rede do Gateway de aplicativo

Grupos de segurança de rede (NSGs) têm suporte para a sub-rede de Gateway de aplicativo com as seguintes restrições: 

- Exceções devem ser feitas para o tráfego de entrada nas portas 65503-65534 para o Application Gateway v1 SKU e portas 65200 - 65535 para o v2 SKU. Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Elas são protegidas (bloqueadas) por certificados do Azure. Sem os certificados apropriados, as entidades externas, incluindo os clientes desses gateways, não podem iniciar nenhuma alteração nesses pontos de extremidade.

- A conectividade de internet de saída não pode ser bloqueada.

- Tráfego de marca AzureLoadBalancer deve ser permitido.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>Acesso do Gateway de aplicativo de lista de permissões para alguns IPs de origem

Esse cenário pode ser realizado usando os NSGs na sub-rede do Gateway de Aplicativo. As seguintes restrições devem ser colocadas na sub-rede na ordem listada de prioridade:

1. Permitir tráfego de entrada de intervalo de IP/IP de origem.
2. Permitir solicitações de entrada de todas as fontes para portas 65534 65503 para [comunicação de integridade de back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Elas são protegidas (bloqueadas) por certificados do Azure. Sem os certificados apropriados, as entidades externas, incluindo os clientes desses gateways, não poderão iniciar nenhuma alteração nesses pontos de extremidade.
3. Permitir entradas investigações do Azure Load Balancer (marca AzureLoadBalancer) e tráfego de rede virtual entrada (marca VirtualNetwork) no [NSG](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Bloquear todo o outro tráfego de entrada com um Negar todas as regras.
5. Permitir tráfego de saída para a internet para todos os destinos.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Rotas definidas pelo usuário com suporte na sub-rede do Gateway de aplicativo

No caso de SKU do v1, rotas definidas pelo usuário (UDRs) têm suporte na sub-rede do gateway de aplicativo, desde que elas não alteram a comunicação de solicitação/resposta de ponta a ponta.

Por exemplo, é possível configurar uma UDR na sub-rede do gateway de aplicativo para apontar a um dispositivo de firewall para inspeção de pacote, mas é necessário garantir que o pacote possa alcançar a pós-inspeção de destino pretendida. Não fazer isso poderá resultar em uma investigação de integridade ou um comportamento de roteamento de tráfego incorreto. Isso inclui rotas aprendidas ou rotas 0.0.0.0/0 padrão propagadas por ExpressRoute ou Gateways de VPN na rede virtual.

No caso de v2 SKU, UDRs na sub-rede do gateway de aplicativo não têm suporte. Para obter mais informações, confira [Dimensionamento automático e o Gateway de Aplicativo com redundância de zona (versão prévia pública)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations).

## <a name="frontend-ip"></a>IP de front-end

Você pode configurar o gateway de aplicativo para ter um endereço IP público ou um endereço IP privado ou ambos. IP público é necessário quando você estiver hospedando um back-end que é necessário para ser acessado por clientes pela internet por meio de um VIP voltado para a Internet. IP público não é necessário para um ponto de extremidade interno não exposto à Internet, também conhecido como uma carga interno (ILB) balanceador ponto de extremidade. Configurar o gateway como um ILB é útil para aplicativos de linha de negócios internos não expostos à Internet. Isso também é útil para serviços e camadas em um aplicativo multicamada que reside em um limite de segurança não exposto à Internet, mas que ainda exige distribuição de carga round robin, adesão da sessão ou terminação SSL.

Há suporte para apenas um endereço IP público ou um endereço IP privado. Você pode escolher o IP de front-end ao criar o Gateway de aplicativo. 

- No caso de um IP público, você pode optar por criar um novo IP público ou usar um IP público existente no mesmo local que o Gateway de aplicativo. Se você criar um novo endereço IP público, o tipo de endereço IP estiver selecionado (estático ou dinâmico) não pode ser alterado posteriormente. Para obter mais informações, consulte [portas estáticas vs IP público dinâmico](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip) 

- No caso de um endereço IP privado, você pode optar por especificar um endereço IP privado da sub-rede na qual o Gateway de aplicativo é criado. Se não for especificado explicitamente, um endereço IP arbitrário será selecionado automaticamente da sub-rede. Para obter mais informações, consulte [criar um gateway de aplicativo com um ponto de extremidade do (ILB) do balanceador de carga interno.](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)

Um Frontend IP está associado a *ouvinte* que verifica as solicitações de entrada no Frontend IP.

## <a name="listeners"></a>Ouvintes

Um ouvinte é uma entidade lógica que verifica as solicitações de conexão de entrada, usando a porta, protocolo, host e endereço IP. Portanto, quando você configura o ouvinte, que você precisará inserir esses valores de porta, protocolo, host e endereços IP que são as mesmo que os valores correspondentes na solicitação de entrada no gateway. Quando você cria um gateway de aplicativo usando o portal do Azure, você também cria um ouvinte padrão ao escolher o protocolo e porta para o ouvinte. Além disso, você pode escolher se deseja habilitar o suporte de HTTP2 no ouvinte. Depois de criar o Gateway de aplicativo, você pode editar a configuração desse ouvinte padrão (*appGatewayHttpListener*/*appGatewayHttpsListener*) e/ou criar novos ouvintes.

### <a name="listener-type"></a>Tipo de ouvinte

Você pode escolher entre [ouvinte básico ou multissite](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners) durante a criação de um novo ouvinte. 

- Se você estiver hospedando um único site por trás de um gateway de aplicativo, escolha o ouvinte básico. Saiba mais [como criar um gateway de aplicativo com o ouvinte básico](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Se você estiver configurando mais de um aplicativo web ou vários subdomínios do mesmo domínio pai na mesma instância de gateway de aplicativo, em seguida, escolha o ouvinte multissite. Para o ouvinte multissite, além disso você precisará inserir um nome de host. Isso ocorre porque o Gateway de aplicativo depende de cabeçalhos de host HTTP 1.1 para hospedar mais de um site no mesmo endereço IP público e porta.![1551057450710](C:/Users/absha/AppData/Roaming/Typora/typora-user-images/1551057450710.png)


> [!NOTE]
> No caso de SKUs v1, os ouvintes são processados na ordem em que elas são mostradas. Por esse motivo, se um ouvinte básico corresponder a uma solicitação de entrada, ele a processará primeiro. Portanto, os ouvintes multissite devem ser configuradas antes de um ouvinte básico para garantir que o tráfego é roteado para o back-end correto.
>
> No caso de SKUs v2, ouvintes multissite são processadas antes dos ouvintes básicos.

### <a name="frontend-ip"></a>IP de front-end

Escolha o IP de front-end que você pretende associá-lo com este ouvinte. O ouvinte escutará à solicitação de entrada nesse IP.

### <a name="frontend-port"></a>Porta de front-end

Escolha a porta de front-end. Você pode escolher entre as portas existentes ou criar um novo. Você pode escolher qualquer valor entre o [intervalo de portas permitido](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Isso permite que você não apenas para usar portas bem conhecidas, como 80 e 443, mas qualquer permissão porta personalizada adequada para seu uso. Uma porta pode ser usada para ouvintes voltado para o públicos ou privados ouvintes voltado para o público.

### <a name="protocol"></a>Protocolo

Você precisa escolher entre o protocolo HTTP e HTTPS. 

- Se você optar por HTTP, o tráfego entre o gateway de aplicativo cliente e fará o fluxo não criptografados.

- Selecione HTTPS, se você estiver interessado em [terminação de Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) ou [criptografia de SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Se você optar por HTTPS, o tráfego entre o gateway de aplicativo do cliente será criptografado e a conexão SSL no gateway de aplicativo será encerrado.  Se desejar que a criptografia SSL de ponta a ponta, você precisará escolher o protocolo HTTPS ao configurar adicionalmente *configuração de HTTP de back-end*. Isso garantirá que o tráfego é criptografado novamente quando são transmitidos entre o Gateway de aplicativo e o back-end.

  Para configurar a criptografia SSL de ponta a ponta e o término de Secure Sockets Layer (SSL), é necessário um certificado a ser adicionado ao ouvinte de modo a habilitar o Gateway de aplicativo para derivar uma chave simétrica de acordo com a especificação do protocolo SSL. A chave simétrica, em seguida, é usada para criptografar e descriptografar o tráfego enviado para o gateway. O certificado do gateway precisa estar no formato PFX (Troca de Informações Pessoais). Esse formato de arquivo permite exportar a chave privada que é exigida pelo gateway de aplicativo para realizar a criptografia e descriptografia do tráfego. 

#### <a name="supported-certs"></a>Certificados com suporte

Há suporte a certificados autoassinados, certificados de autoridade de certificação, certificados curinga e certificados EV.

### <a name="additional-protocol-support"></a>Suporte a protocolos adicionais

#### <a name="http2-support"></a>Suporte de HTTP2

O suporte ao protocolo HTTP/2 está disponível para os clientes que se conectam apenas os ouvintes do Gateway de Aplicativo. A comunicação para pools de servidores back-end é sobre HTTP/1.1. Por padrão, o suporte HTTP/2 está desabilitado. O snippet de código do Azure PowerShell a seguir mostra como é possível habilitá-lo:

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Suporte ao WebSocket

Suporte ao WebSocket está habilitado por padrão. Não há nenhuma configuração configurável pelo usuário para habilitar ou desabilitar seletivamente o suporte ao WebSocket. Você pode usar WebSockets com ouvintes HTTP e HTTPS. 

### <a name="custom-error-page"></a>Página de erro personalizada

Páginas de erro personalizadas podem ser definidas no nível global, bem como o nível de ouvinte, no entanto, a criação de páginas de erro personalizadas de nível global do portal do Azure não é suportado atualmente. Você pode configurar uma página de erro personalizada para um erro 403 de WAF ou uma página de manutenção 502 no nível de ouvinte. Você também precisará especificar uma URL de blob publicamente acessível para o código de status de erro especificado. Para obter mais informações, consulte [página de erro personalizada criar](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Códigos de erro do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Para configurar uma página de erro personalizada global, use [Azure PowerShell para configuração](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration) 

### <a name="ssl-policy"></a>Política SSL

Você pode centralizar o gerenciamento de certificados SSL e reduzir a sobrecarga de criptografia e descriptografia de um farm de servidores back-end. Esse tratamento SSL centralizado também permite que você especifique uma política SSL central adequada aos seus requisitos de segurança organizacional.  Você pode escolher entre padrão, predefinidos e política personalizada do SSL. 

Você pode configurar a política de SSL para controlar versões de protocolo SSL. Você pode configurar o gateway de aplicativo para negar TLS1.0, TLS1.1 e TLS1.2. SSL 2.0 e 3.0 já estão desabilitados por padrão e não são configuráveis. Para obter mais informações, consulte [visão geral da política de SSL do Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Depois de criar um ouvinte, associá-lo com uma regra de roteamento de solicitação que determina como a solicitação recebida no ouvinte deve ser roteada para o back-end.

## <a name="request-routing-rule"></a>Regra de roteamento de solicitação

Ao criar o gateway de aplicativo usando o portal do Azure, você pode criar uma regra padrão (*rule1*), que se associa o ouvinte padrão (*appGatewayHttpListener*) com o pool de back-end padrão (*appGatewayBackendPool*) e configurações de HTTP de back-end padrão (*appGatewayBackendHttpSettings*). Depois de criar o gateway de aplicativo, você pode editar a definição de regra padrão e/ou criar novas regras.

### <a name="rule-type"></a>Tipo de regra

Você pode escolher entre [regra básica ou baseada em caminho](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule) ao criar uma nova regra. 

- Se você deseja encaminhar todas as solicitações no ouvinte associado (por exemplo: blog.contoso.com/*) a um pool de back-end único, escolha o ouvinte básico. 
- Escolha o ouvinte baseado em caminho se você quiser rotear solicitações com o caminho da URL específica para pools de back-end específico. O padrão de caminho é aplicado somente para o caminho da URL, não para seus parâmetros de consulta.


> [!NOTE]
>
> No caso de SKUs v1, correspondência de padrão de solicitação de entrada é processada na ordem em que os caminhos são listados no mapa de caminho de URL da regra com base no caminho. Por esse motivo, se uma solicitação corresponde ao padrão em dois ou mais caminhos no mapa de caminho de URL, em seguida, o caminho que é listado primeiro será correspondido e a solicitação será encaminhada para o back-end associado a esse caminho.
>
> No caso de SKUs v2, uma correspondência exata mantém a maior prioridade sobre a ordem na qual os caminhos são listados no mapa de caminho de URL. Para que o motivo, se uma solicitação corresponde ao padrão em dois ou mais caminhos, em seguida, a solicitação será encaminhada para o back-end associada com esse caminho que corresponda exatamente com a solicitação. Se o caminho na solicitação de entrada corresponder exatamente a qualquer caminho no mapa de caminho de URL, em seguida, correspondência de padrão de solicitação de entrada é processada na ordem em que os caminhos são listados no mapa de caminho de URL da regra com base no caminho.

### <a name="associated-listener"></a>Ouvinte associado

Você precisa associar um ouvinte para a regra para que o *regra de roteamento de solicitação* associado com o *ouvinte* é avaliada para determinar a *pool de back-end* ao qual o solicitação deve ser roteada.

### <a name="associated-backend-pool"></a>Associado back-end do Pool

Associe o pool de back-end que contém os destinos de back-end que atenderá às solicitações recebidas pela escuta. No caso de uma regra básica, apenas um pool de back-end é permitido, pois todas as solicitações no ouvinte associado serão encaminhadas para esse pool de back-end. No caso de uma regra com base em caminho, adicione vários pools de back-end correspondente a cada caminho de URL. As solicitações que correspondam ao caminho de URL inserido aqui, será encaminhado para o pool de back-end correspondente. Além disso, adicione um pool de back-end padrão, pois as solicitações que não correspondem a qualquer caminho de URL inserido nesta regra serão encaminhadas para ele.

### <a name="associated-backend-http-setting"></a>Configuração de HTTP de back-end associado

Adicione uma configuração de HTTP de back-end para cada regra. As solicitações serão roteadas de Gateway de aplicativo para os destinos de back-end usando o número da porta, protocolo e outras configurações especificadas nesta configuração. No caso de uma regra básica, apenas uma configuração de HTTP de back-end é permitida, pois todas as solicitações no ouvinte associado serão encaminhadas para os destinos de back-end correspondente usando essa configuração de HTTP. No caso de uma regra com base em caminho, adicione várias configurações de HTTP de back-end correspondente a cada caminho de URL. As solicitações que correspondam ao caminho de URL inserido aqui, será encaminhado para os destinos correspondentes do back-end usando as configurações de HTTP correspondente para cada caminho de URL. Além disso, adicione uma configurações de HTTP padrão, pois as solicitações que não correspondem a qualquer caminho de URL inserido nesta regra serão encaminhadas para o pool de back-end padrão usando as configurações de HTTP padrão.

### <a name="redirection-setting"></a>Configuração de redirecionamento

Se o redirecionamento é configurado para uma regra básica, todas as solicitações no ouvinte associado serão redirecionadas para o destino de redirecionamento, permitindo que o redirecionamento global. Se o redirecionamento está configurado para uma regra com base em caminho, as solicitações somente em uma área específica do site, por exemplo do carrinho de compras área denotada por /Cart/ *, será redirecionado para o destino de redirecionamento, permitindo que o redirecionamento baseado em caminho. 

Para obter informações sobre a funcionalidade de redirecionamento, consulte [visão geral do redirecionamento](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

- #### <a name="redirection-type"></a>Tipo de redirecionamento

  Escolha o tipo de redirecionamento necessário do: Permanentes, temporário, encontrado ou ver outros.

- #### <a name="redirection-target"></a>Destino de redirecionamento

  Você pode escolher entre um site externo ou de outro ouvinte como destino de redirecionamento. 

  - ##### <a name="listener"></a>Ouvinte

    Ajuda escolhendo ouvinte como o destino de redirecionamento no redirecionamento de um ouvinte para outro ouvinte no gateway. Essa configuração é necessária quando você deseja habilitar HTTP para redirecionamento a HTTPS, ou seja, tráfego de redirecionamento do ouvinte fonte Verificando as solicitações HTTP de entrada para o ouvinte de destino, verificando as solicitações de HTTPS de entrada. Você também pode escolher a cadeia de caracteres de consulta e o caminho na solicitação original a ser incluído na solicitação encaminhada para o destino de redirecionamento.![componentes do gateway de aplicativo](./media/configuration-overview/configure-redirection.png)

    Para obter mais informações sobre HTTP para redirecionamento a HTTPS, consulte [redirecionamento de HTTP para HTTP usando o portal](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal), [redirecionamento de HTTP para HTTP usando o PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell), [redirecionamento de HTTP para HTTP usando a CLI](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

  - ##### <a name="external-site"></a>Site externo

    Escolha o site externo quando você deseja redirecionar o tráfego no ouvinte associado à regra, portanto, ser redirecionado para um site externo. Você pode escolher a cadeia de caracteres de consulta na solicitação original a ser incluído na solicitação encaminhada para o destino de redirecionamento. Você não pode encaminhar o caminho na solicitação original para o site externo.

    Para obter mais informações sobre o redirecionamento para o site externo, consulte [redirecionar o tráfego para o site externo usando o PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell) e [https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-http-header-setting"></a>Reescreva a configuração do cabeçalho HTTP

Esse recurso permite que você adicionar, remover ou atualizar os cabeçalhos de solicitação e resposta HTTP durante a solicitação e pacotes de resposta se mover entre os pools de back-end e de cliente.    Você pode configurar esse recurso somente por meio do PowerShell. Suporte do portal e CLI ainda não está disponível. Para obter mais informações, consulte [cabeçalhos HTTP reescrever](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) visão geral e [reescrita do cabeçalho HTTP configurar](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration).

## <a name="http-settings"></a>Configurações de HTTP

O gateway de aplicativo roteia o tráfego para os servidores de back-end usando a configuração especificada neste componente. Depois que você cria uma configuração de HTTP, você precisa associá-la com um ou mais regras de roteamento de solicitação.

### <a name="cookie-based-affinity"></a>Afinidade com base em cookie

Esse recurso é útil quando você deseja manter uma sessão de usuário no mesmo servidor. Usando cookies gerenciados pelo gateway, o gateway de aplicativo pode direcionar o tráfego seguinte de uma sessão de usuário para o mesmo servidor para processamento. Isso é importante em casos em que o estado de sessão é salvo localmente no servidor para uma sessão de usuário. Se o aplicativo não pode lidar com a afinidade de baseada em cookies, em seguida, você não poderá usar esse recurso. Para usar a afinidade de sessão baseada em cookies, você deve garantir que os clientes devem oferecer suporte a cookies. 

### <a name="connection-draining"></a>Descarregamento de conexão

O descarregamento de conexão ajuda você a efetuar a remoção normal de membros do pool de back-end durante atualizações de serviço planejadas. Essa configuração pode ser aplicada a todos os membros de um pool de back-end durante a criação da regra. Uma vez habilitada, o gateway de aplicativo garante que todas as instâncias de cancelar o registro de um pool de back-end não recebe novas solicitações, permitindo que solicitações existentes ser concluída dentro de um limite de tempo configurado. Isso se aplica a instâncias de back-end removidas explicitamente do pool de back-end por uma chamada à API e a instâncias de back-end relatadas como não íntegras, conforme determinado por investigações de integridade.

### <a name="protocol"></a>Protocolo

O gateway de aplicativo dá suporte a protocolos HTTP e HTTPS para rotear solicitações para os servidores de back-end. Se o protocolo HTTP é escolhido, em seguida, fluxos de tráfego não criptografados para os servidores de back-end. Nesses casos em que a comunicação não criptografada para os servidores de back-end não é uma opção aceitável, você deve escolher o protocolo HTTPS. Essa configuração, combinado com a escolha de protocolo HTTPS no ouvinte da permite que você habilite [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Que permite transmitir com segurança dados confidenciais para o back-end criptografado. Cada servidor de back-end no pool de back-end com SSL de ponta a ponta habilitado deve ser configurado com um certificado para permitir uma comunicação segura.

### <a name="port"></a>Porta

Essa é a porta que os servidores de back-end estão escutando o tráfego proveniente do Gateway de aplicativo. Você pode configurar as portas que variam de 1 a 65535.

### <a name="request-timeout"></a>Tempo limite da solicitação

O número de segundos que o gateway de aplicativo espera receber a resposta do pool de back-end antes de retornar um erro de "Conexão atingiu o tempo limite".

### <a name="override-backend-path"></a>Substituir caminho de back-end

Essa configuração permite que você configure um caminho de encaminhamento personalizado opcional a ser usado quando a solicitação é encaminhada para o back-end. Isso irá copiar qualquer parte do caminho de entrada que corresponde ao caminho personalizado especificado na **substituir o caminho de back-end** campo para o caminho encaminhado. Consulte a tabela a seguir para entender como funciona o recurso.

- Quando as configurações de HTTP é anexado a uma regra de roteamento de solicitação básico:

  | Solicitação original  | Substituir caminho de back-end | Solicitação encaminhada ao back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | / Substituir/home /              |
  | / home/secondhome / | /override/            | substituição/home/secondhome /   |

- Quando as configurações de HTTP é anexado a uma regra de roteamento de solicitação com base em caminho:

  | Solicitação original           | Regra de caminho       | Substituir caminho de back-end | Solicitação encaminhada ao back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | / Substituir/home /              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | substituição/home/secondhome /   |
  | /Home/                     | /pathrule*      | /override/            | / Substituir/home /              |
  | / home/secondhome /          | /pathrule*      | /override/            | substituição/home/secondhome /   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | / Substituir/secondhome /        |

### <a name="use-for-app-service"></a>Usar para o Serviço de aplicativo

Esse é um atalho de interface do usuário que seleciona as duas configurações necessárias para o back-end de serviço de aplicativo - permite escolher o nome do host do endereço de back-end e cria uma nova investigação personalizada. O motivo por que a primeira opção é feita é explicado na seção de **Escolher nome de host do endereço de back-end** configuração. Um novo teste é criado em que o cabeçalho de investigação também é selecionado do endereço de back-end do membro.

### <a name="use-custom-probe"></a>Usar sonda personalizada

Essa configuração é usada para associar uma [investigação personalizada](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) com essa configuração de HTTP. Você pode associar apenas uma investigação personalizada uma configuração de HTTP. Se você não associar explicitamente uma investigação personalizada, em seguida [investigação padrão](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) será usado para monitorar a integridade do back-end. É recomendável que você crie uma investigação personalizada para que o controle mais granular sobre o monitoramento de integridade do seu back-ends.

> [!NOTE]   
> Investigação personalizada não será iniciado o monitoramento da integridade do pool de back-end, a menos que a configuração de HTTP correspondente é explicitamente associada um ouvinte.

### <a name="pick-host-name-from-backend-address"></a>Escolher o nome de host do endereço de back-end

Esse recurso define dinamicamente os *host* cabeçalho na solicitação para o nome do host do pool de back-end usando um endereço IP ou nome de domínio totalmente qualificado (FQDN). Isso é útil nos cenários em que o nome de domínio do back-end é diferente do nome DNS do gateway de aplicativo e o back-end se baseia em um cabeçalho de host específico ou a extensão SNI para resolver para o ponto de extremidade correto, como no caso de serviços de multilocatário como o back-end. Como o serviço de aplicativo é um serviço de multilocatário usando um espaço compartilhado com um único endereço IP, um serviço de aplicativo pode ser acessado somente com os nomes de host definido nas configurações de domínio personalizado. Por padrão, é o nome de domínio personalizado *example.azurewebsites.net*. Portanto, se você quiser acessar o serviço de aplicativo usando o gateway de aplicativo com qualquer um nome de host não explicitamente registrado no serviço de aplicativo ou com o FQDN do gateway de aplicativo, você precisa substituir o nome do host na solicitação original para o nome de host do serviço de aplicativo, por Habilitando **Escolher nome de host do endereço de back-end** configuração.

Se você possuir um domínio personalizado e mapear o nome DNS personalizado existente para o serviço de aplicativo, não é necessário habilitar essa configuração.

> [!NOTE]   
> Essa configuração não é necessária para o ambiente de serviço de aplicativo (ASE), pois o ASE é uma implementação dedicada. 

### <a name="host-name-override"></a>Substituição do nome de host

Essa capacidade substitui o *host* cabeçalho na solicitação de entrada no gateway de aplicativo para o nome de host que você especificar aqui. Por exemplo, se www\.contoso.com é especificado como o **nome do Host** definindo a solicitação original https://appgw.eastus.cloudapp.net/path1 será alterado para https://www.contoso.com/path1 quando a solicitação é encaminhada para o servidor de back-end. 

## <a name="backend-pool"></a>Pool de back-end

Um pool de back-end pode ser apontado para quatro tipos de membros de back-end: uma máquina virtual específica, conjunto de dimensionamento de máquina virtual, um endereço IP/FQDN ou um serviço de aplicativo. Cada pool de back-end pode apontar para vários membros do mesmo tipo. Não há suporte para a apontando para os membros de tipos diferentes no mesmo pool de back-end. 

Depois de criar um pool de back-end, você precisa associá-lo a uma ou mais regras de roteamento de solicitação. Você também precisará configurar investigações de integridade para cada pool de back-end no gateway de aplicativo. Quando uma condição de regra de roteamento de solicitação é atendida, o gateway de aplicativo encaminha o tráfego para os servidores íntegros (conforme determinado por investigações de integridade) no pool de back-end correspondente.

## <a name="health-probes"></a>Investigações de integridade

Mesmo que o gateway de aplicativo monitora a integridade de todos os recursos em seu back-end por padrão, é altamente recomendável que criar uma investigação personalizada para cada configuração de HTTP de back-end para ter um controle mais granular sobre o monitoramento de integridade. Para saber como configurar a investigação de integridade personalizadas, consulte [configurações de investigação de integridade personalizadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]   
> Depois de criar uma investigação de integridade personalizadas, você precisa associá-la a uma configuração de HTTP de back-end. Investigação personalizada não será iniciado o monitoramento da integridade do pool de back-end, a menos que a configuração de HTTP correspondente é explicitamente associada um ouvinte.

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre os componentes do Gateway de aplicativo, você pode:

- [Criar um Gateway de aplicativo no portal do Azure](quick-create-portal.md)
- [Criar um Gateway de aplicativo usando o PowerShell](quick-create-powershell.md)
- [Criar um Gateway de aplicativo usando a CLI do Azure](quick-create-cli.md)