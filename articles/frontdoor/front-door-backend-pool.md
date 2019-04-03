---
title: Pools de back-ends e back-end no serviço de porta da frente do Azure | Microsoft Docs
description: Este artigo descreve quais pools de back-ends e back-end forem na frente da configuração de porta.
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
ms.openlocfilehash: 2372f49c7280ee5c817f3d2f98cc80a196dae5f5
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879192"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Back-ends e pools de back-end no Azure Front Door Service
Este artigo descreve os conceitos sobre como mapear sua implantação de aplicativo com o serviço de porta da frente do Azure. Ele também explica os termos diferentes na configuração de porta da frente em torno de back-ends de aplicativo.

## <a name="backends"></a>Back-ends
Um back-end é igual à instância de implantação do aplicativo em uma região. Serviço de porta da frente dá suporte a ambos o Azure e o back-ends não do Azure, para que a região não está restrito a regiões do Azure. Além disso, ele pode ser seu datacenter local ou em uma instância do aplicativo em outra nuvem.

Back-ends do serviço de porta da frente se referir ao nome de host ou IP público do seu aplicativo, que pode atender às solicitações de cliente. Back-ends não devem ser confundido com a sua camada de banco de dados, camada de armazenamento e assim por diante. Back-ends devem ser exibida como o ponto de extremidade público do seu back-end do aplicativo. Quando você adiciona um back-end em um pool de back-end de porta da frente, você também deve adicionar o seguinte:

- **Tipo de host de back-end**. O tipo de recurso que você deseja adicionar. Porta da frente serviço dá suporte à detecção automática de seu back-ends de aplicativo do serviço de aplicativo, serviço de nuvem ou armazenamento. Se você quiser um recurso diferente no Azure ou até mesmo um back-end não Azure, selecione **host personalizado**.

    >[!IMPORTANT]
    >Durante a configuração, as APIs não validam se o back-end não está acessível de ambientes de porta da frente. Certifique-se de que a porta da frente pode alcançar seu back-end.

- **Nome de host de assinatura e o back-end**. Se você não selecionou **host personalizado** para tipo de host de back-end, selecione seu back-end, escolhendo a assinatura apropriada e o nome de host de back-end correspondente na interface do usuário.

- **Cabeçalho de host de back-end**. O valor de cabeçalho de host enviado para o back-end para cada solicitação. Para obter mais informações, consulte [cabeçalho de host de back-end](#hostheader).

- **Prioridade**. Atribua prioridades aos seu back-ends diferentes quando você deseja usar um back-end do serviço primário para todo o tráfego. Além disso, fornece backups, se o primário ou backup back-ends estiverem disponíveis. Para obter mais informações, consulte [prioridade](front-door-routing-methods.md#priority).

- **Peso**. Atribua pesos ao seu back-ends diferentes para distribuir tráfego entre um conjunto de back-ends, seja uniformemente ou acordo com os coeficientes de peso. Para obter mais informações, consulte [pesos](front-door-routing-methods.md#weighted).

### <a name = "hostheader"></a>Cabeçalho de host de back-end

Solicitações encaminhadas por porta da frente a um back-end incluem um campo de cabeçalho de host que o back-end usa para recuperar o recurso de destino. O valor para esse campo geralmente vem do URI de back-end e tem o host e a porta.

Por exemplo, uma solicitação feita www\.contoso.com terá o www de cabeçalho de host\.contoso.com. Se você usar o portal do Azure para configurar seu back-end, o valor padrão para esse campo é o nome do host do back-end. Se o seu back-end é contoso-westus.azurewebsites.net, no portal do Azure, o valor populado automaticamente para o cabeçalho de host de back-end seja westus.azurewebsites.net contoso. No entanto, se você usar modelos do Azure Resource Manager ou outro método sem definir explicitamente esse campo, o serviço de porta da frente enviará o nome de host de entrada como o valor para o cabeçalho de host. Se a solicitação foi feita para www\.contoso.com e o back-end é contoso-westus.azurewebsites.net que tem um campo de cabeçalho vazio, o serviço de porta da frente definirá o cabeçalho de host como www\.contoso.com.

A maioria dos aplicativos back-ends (aplicativos Web do Azure, armazenamento de BLOBs e serviços de nuvem) exigem o cabeçalho do host corresponda ao domínio de back-end. No entanto, o host de front-end que encaminha para o back-end usará um nome de host diferente, como www\.contoso.azurefd.net.

Se o seu back-end requer o cabeçalho de host para corresponder ao nome de host de back-end, certifique-se de que o cabeçalho de host de back-end inclui o nome de host back-end.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configurando o cabeçalho de host do back-end para o back-end

Para configurar o **cabeçalho de host de back-end** field para um back-end na seção de pool de back-end:

1. Abra o recurso de porta da frente e selecione o pool de back-end com o back-end para configurar.

2. Se você ainda não tiver feito isso, ou edita um já existente, adicione um back-end.

3. Definir o host de back-end campo de cabeçalho para um valor personalizado ou deixe em branco. O nome do host para a solicitação de entrada será usado como o valor do cabeçalho de host.

## <a name="backend-pools"></a>Pools de back-end
Um pool de back-end na frente serviço porta refere-se ao conjunto de back-ends que recebem tráfego semelhante para seu aplicativo. Em outras palavras, é um agrupamento lógico de suas instâncias de aplicativo em todo o mundo que recebem o mesmo tráfego e responder com o comportamento esperado. Esses back-ends são implantados em regiões diferentes ou na mesma região. Todos os back-ends podem estar em modo de implantação ativo/ativo ou o que é definido como a configuração ativa/passiva.

Um pool de back-end define como os back-ends diferentes devem ser avaliados por meio de investigações de integridade. Ele também define como o balanceamento de carga ocorre entre eles.

### <a name="health-probes"></a>Investigações de integridade
Serviço de porta da frente envia solicitações periódicas de investigação HTTP/HTTPS para cada um dos seu back-ends configurado. Solicitações de investigação de determinam a proximidade e a integridade de cada back-end para carregar equilibrar suas solicitações do usuário final. As configurações da investigação de integridade para um pool de back-end definem como podemos sondar o status de integridade do back-ends de aplicativo. As configurações a seguir estão disponíveis para configuração de balanceamento de carga:

- **Caminho**. A URL usada para solicitações de investigação para todos os back-ends no pool de back-end. Por exemplo, se um dos seu back-ends é westus.azurewebsites.net contoso e o caminho é definido como /probe/test.aspx, em seguida, ambientes do serviço de porta da frente, supondo que o protocolo é definido como HTTP, enviará solicitações de investigação de integridade para http\:/ / contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protocolo**. Define se deseja enviar as solicitações de investigação de integridade do serviço de porta da frente ao seu back-ends com o protocolo HTTP ou HTTPS.

- **Intervalo (segundos)**. Define a frequência de investigações de integridade para seu back-ends ou os intervalos em que cada um dos ambientes da frente envia uma investigação.

    >[!NOTE]
    >Para failovers mais rápidos, defina o intervalo como um valor mais baixo. Quanto menor o valor, quanto maior o volume de investigação de integridade recebem seu back-ends. Por exemplo, se o intervalo é definido como 30 segundos com ambientes de porta da frente de 90 ou POPs globalmente, cada back-end receberá sobre solicitações de investigação de 3 a 5 por segundo.

Para obter mais informações, consulte [investigações de integridade](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Configurações de balanceamento de carga
Configurações de balanceamento de carga para o pool de back-end definem como avaliamos as investigações de integridade. Essas configurações determinam se o back-end está íntegro ou não. Eles também verificam como tráfego de balanceamento de carga entre diferentes back-ends no pool de back-end. As configurações a seguir estão disponíveis para configuração de balanceamento de carga:

- **O tamanho de exemplo**. Identifica a quantas amostras de investigações de integridade que precisamos considerar para avaliação de integridade do back-end.

- **Tamanho da amostra bem-sucedida**. Define o tamanho da amostra como mencionado anteriormente, o número de amostras bem-sucedidas necessárias para chamar o back-end íntegro. Por exemplo, suponha que um intervalo de investigação de integridade da frente é de 30 segundos, tamanho da amostra é de 5 segundos e tamanho da amostra bem-sucedida é de 3 segundos. Investigações de cada vez que podemos avaliar a integridade para o back-end, vamos examinar as últimas cinco amostras mais de 150 segundos (5 x 30). Pelo menos três investigações bem-sucedidas são necessárias para declarar o back-end íntegro.

- **Sensibilidade de latência (latência adicional)**. Define se deseja que a porta da frente para enviar a solicitação ao back-ends dentro do intervalo de sensibilidade de medida de latência ou encaminhar a solicitação para o back-end mais próximo.

Para obter mais informações, consulte [menor latência com base em método de roteamento](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Próximas etapas

- [Criar um perfil de Front Door](quickstart-create-front-door.md)
- [Como funciona a porta da frente](front-door-routing-architecture.md)