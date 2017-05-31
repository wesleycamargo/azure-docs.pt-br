---
title: "Visão Geral do Service Fabric e contêineres | Microsoft Docs"
description: "Uma visão geral do Service Fabric e o uso de contêineres para implantar aplicativos de microsserviço. Este artigo fornece uma visão geral de como os contêineres podem ser usados e dos recursos disponíveis no Service Fabric."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/16/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: b8334d0ca0d1460edad9b0ef399e9b3428b1ef8d
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017


---
# <a name="service-fabric-and-containers"></a>Service Fabric e contêineres
> [!NOTE]
> Esse recurso está em versão prévia para Linux. 
>   

## <a name="introduction"></a>Introdução
O Azure Service Fabric é um [orquestrador](service-fabric-cluster-resource-manager-introduction.md) de serviços entre um cluster de computadores, com anos de uso e otimização em grande escala dos serviços da Microsoft. Os serviços podem ser desenvolvidos de várias maneiras, desde usando os [modelos de programação do Service Fabric ](service-fabric-choose-framework.md) até implantando [executáveis convidados](service-fabric-deploy-existing-app.md). Por padrão, o Service Fabric implanta e ativa esses serviços como processos. Processos fornecem a ativação mais rápida e o uso de densidade mais alto dos recursos em um cluster. O Service Fabric também pode implantar serviços em imagens de contêiner. É importante observar que você pode misturar serviços em processos e serviços em contêineres no mesmo aplicativo. 

## <a name="containers-and-service-fabric-roadmap"></a>Mapa dos contêineres e do Service Fabric
Nas próximas versões, o Service Fabric continuará a adicionar suporte extensivo para contêineres no Windows e no Linux, incluindo melhorias em redes, restrições de recursos, segurança, diagnósticos, drivers de volume e suporte de ferramentas, especialmente no Visual Studio, para que a experiência de usar imagens de contêiner para implantar serviços seja de primeira classe. Assim, você tem a opção de escolher qualquer contêiner para empacotar o código existente (por exemplo, aplicativos IIS MVC) ou os modelos de programação do Service Fabric e, como o Service Fabric os trata da mesma forma, é possível combiná-los em seus aplicativos, proporcionando flexibilidade na implantação de códigos. Você tem o melhor dos dois mundos, dependendo do cenário.

## <a name="what-are-containers"></a>O que são contêineres?
Contêineres são componentes encapsulados e implantados individualmente executados como instâncias isoladas no mesmo kernel para beneficiarem-se da virtualização que um sistema operacional oferece. Isso significa que cada aplicativo e seu tempo de execução, suas dependências e suas bibliotecas de sistema são executados dentro de um contêiner com acesso privado e completo à exibição isolada própria do contêiner dos constructos do sistema operacional. Junto com a portabilidade, esse nível de isolamento de segurança e de recursos é o principal benefício para o uso de contêineres com o Service Fabric, que, caso contrário, executa serviços em processos.

Contêineres são uma tecnologia de virtualização que virtualiza o sistema operacional subjacente de aplicativos. Os contêineres fornecem um ambiente imutável para que aplicativos sejam executados com graus variados de isolamento. Os contêineres são executados sobre o kernel e têm uma exibição isolada do sistema de arquivos e outros recursos. Em comparação a máquinas virtuais, contêineres têm as seguintes vantagens:

* **Tamanho pequeno**: contêineres usam um único espaço de armazenamento e deltas menores para cada camada para aumentar a eficiência.
* **Tempo de inicialização rápido**: contêineres não precisam inicializar um sistema operacional, assim, podem estar ativos e disponíveis muito mais rapidamente (em geral, em segundos) do que as máquinas virtuais.
* **Portabilidade**: uma imagem de aplicativo em contêiner pode ser portada para execução na nuvem, local, dentro de máquinas virtuais ou diretamente em computadores físicos.
* **Governança de recursos**: um contêiner pode limitar os recursos físicos que ele pode consumir em seu host.

## <a name="container-types"></a>Tipos de contêiner
O Service Fabric dá suporte aos tipos de contêineres a seguir.

### <a name="docker-containers-on-linux"></a>Contêineres de Docker no Linux
O docker oferece APIs de alto nível para criar e gerenciar contêineres sobre contêineres de kernel do Linux. O Hub do Docker é um repositório central para armazenar e recuperar imagens de contêiner.

Para um passo a passo sobre como fazer isso, leia [Implantar um contêiner do Docker no Service Fabric](service-fabric-deploy-container-linux.md).

### <a name="windows-server-containers"></a>Contêineres do Windows Server
O Windows Server 2016 fornece dois tipos diferentes de contêineres que diferem no nível de isolamento fornecido. Contêineres do Windows Server e contêineres do Docker são semelhantes porque ambos têm isolamento de sistema de arquivos e namespace, mas compartilham o kernel com o host no qual estão sendo executados. No Linux, esse isolamento tradicionalmente foi fornecido por cgroups e namespaces, e contêineres do Windows Server se comportam da mesma forma.

Contêineres do Windows Hyper-V fornecem mais segurança e isolamento porque nenhum contêiner compartilha o kernel do sistema operacional com outros contêineres ou com o host. Com esse nível mais alto de isolamento de segurança, os contêineres do Hyper-V são indicados para cenários hostis e multilocatários.

Para um passo a passo sobre como fazer isso, leia [Implantar um contêiner do Windows no Service Fabric](service-fabric-deploy-container.md).

A figura a seguir mostra os diferentes tipos de virtualização e níveis isolamento disponíveis no sistema operacional.
![Plataforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Cenários de uso de contêineres
Estes são exemplos típicos em que um contêiner é uma boa opção:

* **Comparar e deslocar IIS**: se você tiver aplicativos [ASP.NET MVC](https://www.asp.net/mvc) existentes que deseje continuar usando, coloque-os em um contêiner, em vez de migrá-los para ASP.NET Core. Esses aplicativos ASP.NET MVC dependem do IIS (Serviços de Informações da Internet). Você pode empacotá-los em imagens de contêiner da imagem do IIS pré-criada e implantá-los com o Service Fabric. Consulte [Imagens de Contêiner no Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images) para obter informações sobre como criar imagens do IIS.
* **Combinar contêineres e microsserviços do Service Fabric**: use uma imagem existente do contêiner para parte do seu aplicativo. Por exemplo, é possível usar o [contêiner NGINX](https://hub.docker.com/_/nginx/) para o front-end da Web do seu aplicativo e serviços com estado para obter a computação mais intensiva de back-end.
* **Reduzir o impacto de serviços "vizinhos ruidosos"**: você pode usar a capacidade de governança de recursos de contêineres para restringir os recursos que um serviço usa em um host. Se os serviços consumirem muitos recursos e afetarem o desempenho dos outros (como uma operação demorada, parecida com uma consulta), considere colocar esses serviços em contêineres com governança de recursos.

## <a name="service-fabric-support-for-containers"></a>Suporte ao Service Fabric para contêineres
Atualmente, o Service Fabric dá suporte à implantação de contêineres de Docker em contêineres do Linux e do Windows Server no Windows Server 2016. O suporte para contêineres do Hyper-V será adicionado em uma versão futura.

No [modelo de aplicativo](service-fabric-application-model.md)do Service Fabric, um contêiner representa um host de aplicativo no qual várias réplicas de serviço são colocadas. Os cenários a seguir têm suporte para contêineres:

* **Contêineres convidados**: esse cenário é idêntico ao [cenário de executável convidado](service-fabric-deploy-existing-app.md) em que você pode implantar aplicativos existentes em um contêiner. Exemplos incluem Node. js, JavaScript ou qualquer código (executáveis).
* **Serviços sem estado dentro de contêineres**: são serviços sem estado que usam os modelos de programação de Reliable Services e Reliable Actors. Atualmente, ele só tem suporte no Linux. O suporte para serviços sem monitoração de estado em contêineres do Windows será adicionado na versão futura.
* **Serviços com estado dentro de contêineres**: são serviços com que usam o modelo de programação Reliable Actors no Linux. Atualmente, os Reliable Services não têm suporte no Linux.  O suporte para serviços com estado em contêineres do Windows será adicionado na versão futura.

O Service Fabric tem vários recursos de contêiner que ajudam a compilar de aplicativos compostos por microsserviços em contêineres. Eles são chamados de serviços em contêineres. As funcionalidade s incluem:

* Implantação e ativação de imagem de contêiner.
* Governança de recursos.
* Autenticação do repositório.
* Porta do contêiner para o mapeamento da porta de host.
* Descoberta e comunicação de contêiner para contêiner.
* Capacidade de configurar e definir as variáveis de ambiente.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu sobre contêineres, que o Service Fabric é um orquestrador de contêiner e que o Service Fabric tem recursos que dão suporte a contêineres. Como uma próxima etapa, apresentaremos exemplos de cada um dos recursos para mostrar como usá-los.

[Implantar um contêiner do Windows no Service Fabric no Windows Server 2016](service-fabric-deploy-container.md)

[Implantar um contêiner do Docker no Service Fabric no Linux](service-fabric-deploy-container-linux.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png

