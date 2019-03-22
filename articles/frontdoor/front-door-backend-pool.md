---
title: Azure Front Door Service – Back-ends e pools de back-ends | Microsoft Docs
description: Este artigo ajuda você a entender o que são back-ends e pools de back-end para configuração do Front Door.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 228ed5c54a382db7b47d19adacf9e5db398c53ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123684"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Back-ends e pools de back-end no Azure Front Door Service
Este artigo explica os diferentes conceitos sobre como você pode mapear a implantação do seu aplicativo com o Front Door. Também explicaremos o que os termos diferentes na frente significa de configuração de porta em torno de back-end do aplicativo.

## <a name="backend-pool"></a>Pool de back-end
Um pool de back-end no Front Door refere-se ao conjunto de back-ends equivalentes que podem receber o mesmo tipo de tráfego para seu aplicativo. Em outras palavras, é um agrupamento lógico de instâncias do aplicativo em todo o mundo que podem receber o mesmo tráfego e responder com o comportamento esperado. Esses back-ends normalmente são implantados em regiões diferentes ou na mesma região. Além disso, esses back-ends podem estar todos no modo de implantação ativo-ativo ou definidos como uma configuração ativo/passivo.

O pool de back-end também define como os diferentes back-ends devem todos ser avaliados quanto a sua integridade por meio de investigações de integridade e, da mesma forma, como o balanceamento de carga entre os back-ends deve acontecer.

### <a name="health-probes"></a>Investigações de integridade
O Front Door envia solicitações periódicas de investigação HTTP/HTTPS para cada um dos seu back-ends configurado para determinar a proximidade e a integridade de cada um para balancear a carga de suas solicitações do usuário final. As configurações da investigação de integridade para um pool de back-end definem como podemos investigar o status de integridade para seus back-ends de aplicativo. As configurações a seguir estão disponíveis para configuração para balanceamento de carga:

1. **Caminho**: Caminho da URL em que as solicitações de investigação serão enviadas para todos os back-ends no pool de back-end. Por exemplo, se um dos seu back-ends estiver `contoso-westus.azurewebsites.net` e o caminho for definido como `/probe/test.aspx`, ambientes do Front Door, assumindo que o protocolo esteja definido como HTTP, enviarão as solicitações de investigação de integridade para http://contoso-westus.azurewebsites.net/probe/test.aspx. 
2. **Protocolo**: Define se as solicitações de investigação de integridade da porta da frente ao seu back-ends serão enviadas através do protocolo HTTP ou HTTPS.
3. **Intervalo (segundos)**: Este campo define a frequência de investigações de integridade para seu back-ends, ou seja, os intervalos em que cada um dos ambientes da frente enviará uma investigação. Observação: se estiver procurando por failovers mais rápidos, defina esse campo como um valor mais baixo. No entanto, quanto menor o valor, maior o volume de investigação de integridade que seus back-ends receberão. Para obter uma ideia de quanto volume de investigação o Front Door gerará em seus back-ends, vejamos um exemplo. Vamos dizer que o intervalo é definido como 30 segundos e há cerca de 90 ambientes do Front Door ou POPs globalmente. Portanto, cada um dos seus back-ends receberá aproximadamente cerca de 3 a 5 solicitações de investigação por segundo.

Leia [investigações de integridade](front-door-health-probes.md) para obter detalhes.

### <a name="load-balancing-settings"></a>Configurações do balanceamento de carga
As configurações do balanceamento de carga para o pool de back-end definem como avaliamos as investigações de integridade para decidir se o back-end está íntegro ou não íntegro e também como precisamos balancear a carga entre os diferentes back-ends no pool de back-end. As configurações a seguir estão disponíveis para configuração para balanceamento de carga:

1. **O tamanho de exemplo**: Essa propriedade identifica quantas amostras de investigações de integridade que precisamos considerar para avaliação de integridade do back-end.
2. **Tamanho da amostra bem-sucedida**: Esta propriedade define que do 'tamanho da amostra' conforme explicado acima, quantas amostras precisamos verificar se há êxito chamar o back-end íntegro. 
</br>Por exemplo, digamos que para o Front Door você tenha definido a investigação de integridade *intervalo* como 30 segundos, o *tamanho de exemplo* está definido como '5' e o *tamanho da amostra bem-sucedida* está definido como '3'. O que essa configuração significa é que toda vez que avaliamos as investigações de integridade para o back-end, vamos examinar os últimos cinco exemplos, que poderiam abranger os últimos 150 segundos (=5*30 s) e, a menos que haja 3 ou mais dessas investigações bem-sucedidas, declararemos que o back-end não está íntegro. Digamos que havia apenas duas investigações bem-sucedidas e, portanto, marcaremos o back-end como não íntegro. Na próxima vez em que executarmos a avaliação, se encontrarmos 3 bem-sucedidos nas últimas cinco avaliações, marcaremos o back-end como Íntegro novamente.
3. **Sensibilidade de latência (latência adicional)**: O campo de sensibilidade de latência define se deseja que a porta da frente para enviar a solicitação ao back-ends que estão dentro do limite de sensibilidade em termos de medida de latência ou encaminhar a solicitação para o back-end mais próximo. Leia [menor latência com base em método de roteamento](front-door-routing-methods.md#latency) para o Front Door para saber mais.

## <a name="backend"></a>Back-end
Um back-end é equivalente à instância de implantação do aplicativo em uma região. O Front Door oferece suporte a Azure e back-ends do Azure e, portanto, a região aqui não está restrita apenas a regiões do Azure, mas pode também ser seu datacenter local ou uma instância do aplicativo em alguma outra nuvem.

Back-ends, no contexto do Front Door, refere-se ao nome de host ou IP público do seu aplicativo, que pode atender solicitações do cliente. Portanto, os back-ends não devem ser confundidos com a sua camada de banco de dados ou de armazenamento etc, mas devem ser exibidos como o ponto de extremidade público do back-end do seu aplicativo.

Ao adicionar um back-end em um pool de back-end do seu Front Door, você precisa preencher os detalhes a seguir:

1. **Tipo de host de back-end**: O tipo de recurso que você deseja adicionar. O Front Door oferece suporte à descoberta automática dos back-ends do seu aplicativo se eles estiverem no serviço do aplicativo, no serviço de nuvem ou no armazenamento. Se você quiser um recurso diferente no Azure ou até mesmo um back-end não Azure, selecione 'Host Personalizado'. Observação: durante a configuração, as APIs não são validadas se o back-end estiver acessível em ambientes do Front Door, em vez disso, verifique se seu back-end pode ser alcançado pelo Front Door. 
2. **Nome de host de assinatura e o back-end**: Se você não tiver selecionado o tipo de 'Host personalizado' para o host de back-end, será necessário definir o escopo para baixo e selecione seu back-end, escolhendo a assinatura apropriada e o nome de host de back-end correspondente na interface do usuário.
3. **Cabeçalho de host de back-end**: O valor de cabeçalho de Host enviado para o back-end para cada solicitação. Leia [cabeçalho de host de back-end](#hostheader) para obter detalhes.
4. **Prioridade**: você pode atribuir prioridades para seus diferentes back-ends quando desejar usar um back-end de serviço primário para todo o tráfego e fornecer backups caso os back-ends primários ou de backup não estejam disponíveis. Saiba mais sobre a [prioridade](front-door-routing-methods.md#priority).
5. **Peso**: você pode atribuir pesos para seus diferentes back-ends quando desejar distribuir o tráfego entre um conjunto de back-ends, seja uniformemente ou de acordo com os coeficientes de peso. Leia mais sobre [pesos](front-door-routing-methods.md#weighted).


### <a name = "hostheader"></a>Cabeçalho de host de back-end

Solicitações encaminhadas pelo Front Door para um back-end têm um campo de Cabeçalho de host que o back-end usa para recuperar o recurso de destino. O valor para esse campo geralmente vem do URI de back-end e tem o host e a porta. Por exemplo, uma solicitação feita para `www.contoso.com` terá o Cabeçalho de host `www.contoso.com`. Se você estiver configurando seu back-end usando o portal do Azure, o valor padrão que é preenchido para esse campo é o nome do host do back-end. Por exemplo, se seu back-end for `contoso-westus.azurewebsites.net`, no Portal do Azure será o valor preenchido automaticamente para o cabeçalho de host de back-end será `contoso-westus.azurewebsites.net`. 
</br>No entanto, se você estiver usando modelos do Resource Manager ou outro mecanismo e não está configurando esse campo explicitamente, o Front Door envia o nome de host de entrada como o valor do Cabeçalho de host. Por exemplo, se a solicitação foi feita para `www.contoso.com` e seu back-end é `contoso-westus.azurewebsites.net` com o campo de cabeçalho de host de back-end vazio, o Front Door configurará o Cabeçalho de host como `www.contoso.com`.

A maioria dos back-ends de aplicativo (como aplicativos Web, Armazenamento de BLOBs e Serviços de Nuvem) exigem que o cabeçalho do host corresponda ao domínio do back-end. No entanto, o host de front-end que encaminha para o back-end terá um nome de host diferente, como www\.contoso.azurefd.net. Se o back-end que você está configurando requer que o Cabeçalho de host corresponda ao nome do host do back-end, você deve garantir que o “Cabeçalho de host do back-end” também tenha o nome do host do back-end.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configurando o cabeçalho de host do back-end para o back-end
O campo 'Cabeçalho de host do back-end' pode ser configurado para um back-end na seção de pool de back-end.

1. Abra o recurso do Front Door e clique no pool de back-end com o back-end a ser configurado.

2. Adicione um back-end se você não adicionou nenhum ou edite um existente. O campo 'Cabeçalho de host de back-end' pode ser configurado para um valor personalizado ou deixado em branco, o que significa que o nome do host para a solicitação de entrada será usado como o valor do Cabeçalho de host.



## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).