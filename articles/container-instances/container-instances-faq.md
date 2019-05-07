---
title: Instâncias de contêiner do Azure - perguntas frequentes
description: Respostas para perguntas frequentes relacionadas ao serviço de instâncias de contêiner do Azure
services: container-instances
author: dkkapur
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 4/25/2019
ms.author: dekapur
ms.openlocfilehash: 5657ac9f10c42c2201641c9af447b7ad2e6a3507
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079009"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Perguntas frequentes sobre as instâncias de contêiner do Azure

Este artigo aborda perguntas frequentes sobre as instâncias de contêiner do Azure.

## <a name="deployment"></a>Implantação

### <a name="how-large-can-my-container-image-be"></a>Quão grande pode ser as minha imagem de contêiner?

O tamanho máximo para uma imagem de contêiner implantáveis em instâncias de contêiner do Azure é de 15 GB. Você poderá implantar imagens maiores dependendo da disponibilidade no momento exata em que você implanta, mas isso não é garantido.

O tamanho da sua imagem de contêiner afeta quanto tempo demora para implantar, geralmente que você deseja manter as imagens de contêiner menor possível.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Como posso acelerar a implantação do meu contêiner?

Como um dos determinantes principais de tempos de implantação é o tamanho da imagem, buscar formas de reduzir o tamanho. Remova camadas não precisa ou reduzir o tamanho das camadas na imagem (selecionando uma imagem de sistema operacional de base mais leve). Por exemplo, se você estiver executando contêineres do Linux, considere usar Alpine como sua imagem de base em vez de um servidor Ubuntu completo. Da mesma forma, para contêineres do Windows, use uma imagem base do Nano Server se possível. 

Você também deve verificar a lista de imagens previamente em cache em imagens de contêiner do Azure, disponível por meio de [listar as imagens armazenadas em cache](/rest/api/container-instances/listcachedimages) API. Você poderá alternar uma camada de imagem para uma das imagens previamente armazenado em cache. 

Ver mais [orientação detalhada](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) na redução do tempo de inicialização do contêiner.

### <a name="what-windows-base-os-images-are-supported"></a>Quais imagens do sistema operacional base Windows têm suporte?

#### <a name="windows-server-2016-base-images"></a>Imagens base do Windows Server 2016

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`, `sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`,  `10.0.14393.x`

> [!NOTE]
> Não há suporte para imagens do Windows com base na versão do canal semestral 1709 ou 1803.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Imagens base do cliente e o Windows Server 2019 (visualização)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`, `10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`, `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`, `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Qual camada de imagem do .NET ou .NET Core devem usar no meu contêiner? 

Use a imagem menor que atendem às suas necessidades. Para o Linux, você pode usar um *tempo de execução alpine* imagem do .NET Core, que tem sido suportada desde o lançamento do .NET Core 2.1. Para Windows, se você estiver usando o .NET Framework completo, em seguida, você precisa usar uma imagem do Windows Server Core (imagem somente em tempo de execução, tais como *4.7.2-windowsservercore-ltsc2016*). Imagens de tempo de execução são menores, mas não dão suporte a cargas de trabalho que exigem o SDK do .NET.

## <a name="availability-and-quotas"></a>Cotas e disponibilidade

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>O número de núcleos e memória devo alocar para Meus contêineres ou o grupo de contêineres?

Isso realmente depende de sua carga de trabalho. Comece pequeno e testar o desempenho para ver como fazer seus contêineres. [Monitorar o uso de recursos de CPU e memória](container-instances-monitor.md)e, em seguida, adicione núcleos ou memória com base no tipo de processos que você implantar no contêiner. 

Verifique também a [disponibilidade de recursos](container-instances-region-availability.md#availability---general) para a região que você estiver implantando em para limites superiores nos núcleos de CPU e memória disponível por grupo de contêineres. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Qual infraestrutura subjacente ACI executar em?

As instâncias de contêiner do Azure tem o objetivo de ser um serviço de contêineres sob demanda sem servidor, portanto, queremos a se concentrar no desenvolvimento de seus contêineres e não se preocupar com a infraestrutura! Para aqueles que estão curiosos ou deseje fazer comparações de desempenho, ACI é executado em conjuntos de VMs do Azure de vários SKUs, principalmente a partir do F e a série D. Esperamos que esta opção para alterar no futuro, à medida que continuamos a desenvolver e otimizar o serviço. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Desejo implantar milhares de núcleos no ACI - posso obter minha cota aumentada?
 
Sim (às vezes). Consulte a [cotas e limites](container-instances-quotas.md) artigo para cotas atuais e os limites podem ser aumentados por solicitação.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Pode implantar com mais de 4 núcleos e 16 GB de RAM?

Ainda não. Atualmente, esses são os limites máximos para um grupo de contêineres. Entre em contato com o suporte do Azure com requisitos específicos ou as solicitações. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Quando a ACI estará em uma região específica?

Disponibilidade de região atual é publicada [aqui](container-instances-region-availability.md#availability---general), e informações atualizadas estão disponíveis por meio de [recursos lista](/rest/api/container-instances/listcapabilities) API. Se você tiver um requisito para uma região específica, entre em contato com o suporte do Azure.

## <a name="features-and-scenarios"></a>Recursos e cenários

### <a name="how-do-i-scale-a-container-group"></a>Como eu dimensiono a um grupo de contêineres?

Atualmente, o dimensionamento não está disponível para contêineres ou grupos de contêineres. Se você precisar executar mais instâncias, use nossa API para automatizar e criar mais solicitações para a criação de grupo de contêiner para o serviço. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Quais recursos estão disponíveis para instâncias em execução em uma rede virtual personalizada?

Você pode implantar grupos de contêineres em uma rede virtual do Azure de sua escolha e delegar IPs privados para os grupos de contêiner para rotear o tráfego na rede virtual entre os recursos do Azure. Implantação de um grupo de contêineres em uma rede virtual está atualmente em visualização, e alguns aspectos desse recurso pode ser alterado antes da disponibilidade geral (GA). Ver [limitações da visualização](container-instances-vnet.md#preview-limitations) para obter informações atualizadas.

## <a name="pricing"></a>Preços

### <a name="when-does-the-meter-start-running"></a>Quando o medidor de iniciar a execução?

Duração do grupo de contêiner é calculada do momento em que podemos iniciar efetuar pull de sua primeira imagem do contêiner (para uma nova implantação) ou seu grupo de contêiner é reiniciado (se já implantado), até que o grupo de contêineres é interrompido. Consulte detalhes em [preços das instâncias de contêiner](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Posso impedir que está sendo cobrado quando meus contêineres são interrompidos?

Medidores de interromper a execução depois que seu grupo de todo o contêiner é interrompido. Desde que um contêiner em seu grupo de contêiner está em execução, podemos manter os recursos, caso você deseje iniciar os contêineres novamente. 

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais](container-instances-overview.md) sobre instâncias de contêiner do Azure.
* [Solucionar problemas comuns de](container-instances-troubleshooting.md) em instâncias de contêiner do Azure.