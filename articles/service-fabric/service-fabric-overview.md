---
title: Visão geral da Service Fabric | Microsoft Docs
description: Uma visão geral do Service Fabric no qual os aplicativos são compostos por muitos microsserviços para fornecer dimensionamento e resiliência. O Service Fabric é uma plataforma de sistemas distribuídos usada para criar aplicativos escalonáveis, confiáveis e facilmente gerenciados para a nuvem
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/22/2016
ms.author: mfussell

---
# <a name="overview-of-service-fabric"></a>Visão geral da Malha do Serviço
Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços escalonáveis e confiáveis. O Service Fabric resolve os desafios significativos de desenvolvimento e gerenciamento de aplicativos em nuvem. Desenvolvedores e administradores podem evitar a resolução de problemas complexos de infraestrutura e, em vez disso, se concentram na implementação de cargas de trabalho essenciais e exigentes sabendo que elas são escalonáveis, confiáveis e gerenciáveis. O Service Fabric representa a plataforma de middleware de última geração para criação e gerenciamento de aplicativos escalonáveis de nuvem de Camada 1 e nível corporativo.

## <a name="applications-composed-of-microservices"></a>Aplicativos compostos por microsserviços
O Service Fabric permite criar e gerenciar aplicativos escalonáveis e confiáveis compostos por microsserviços executados a uma densidade bastante alta em um pool compartilhado de computadores (conhecido como um cluster). Ele fornece um tempo de execução sofisticado para criação de microsserviços distribuídos, escalonáveis, com e sem estado. Ele também fornece recursos abrangentes de gerenciamento de aplicativos para provisionamento, implantação, monitoramento, atualização/aplicação de patch e exclusão de aplicativos implantados.

Por que uma abordagem de microsserviços é importante? Os dois motivos principais são:

1. Eles permitem dimensionar diferentes partes do seu aplicativo conforme as suas necessidades.
2. As equipes de desenvolvimento podem ser mais ágeis na distribuição de alterações, fornecendo assim recursos aos seus clientes de forma mais rápida e com mais frequência.

O Service Fabric é a tecnologia de muitos serviços atuais da Microsoft, como Banco de Dados SQL do Azure, Banco de Dados de Documentos do Azure, Cortana, Power BI, Microsoft Intune, Hubs de Eventos do Azure, Azure IoT, Skype for Business e muitos serviços principais do Azure.

O Service Fabric foi personalizado para a criação de serviços “nascidos na nuvem” que podem começar pequenos, conforme a necessidade, e expandirem em imensa escala com centenas ou milhares de computadores.

Os serviços em escala da Internet de hoje são criados por microsserviços. Exemplos de microsserviços são gateways de protocolo, perfis de usuário, carrinhos de compra, processamento de inventário, filas e caches. O Service Fabric é uma plataforma de microsserviços que fornece a cada microsserviço um nome exclusivo que pode ser com ou sem estado.

A Malha do Serviço fornece recursos abrangentes de gerenciamento de ciclo de vida e tempo de execução para aplicativos compostos por esses microsserviços. Ela hospeda microsserviços em contêineres que são implantados e ativados no cluster do Service Fabric. Passar de VMs para contêineres possibilita um aumento de ordem de grandeza na densidade. Da mesma forma, outra ordem de grandeza na densidade se torna possível movendo de contêineres para microsserviços. Por exemplo, um único cluster de Banco de Dados do Azure engloba centenas de computadores que executam dezenas de milhares de contêineres que hospedam um total de centenas de milhares de bancos de dados. Cada banco de dados é um microsserviço com estado do Service Fabric. O mesmo é verdadeiro para outros serviços mencionados anteriormente, motivo pelo qual o termo "hiperescala" é usado para descrever os recursos do Service Fabric. Se os contêineres fornecem alta densidade, os microsserviços fornecem hiperescala.

Para saber mais sobre a abordagem de microsserviços, leia o artigo [Por que usar uma abordagem de microsserviços para construir aplicativos?](service-fabric-overview-microservices.md)

## <a name="create-service-fabric-clusters-anywhere"></a>Criar clusters de Service Fabric em qualquer lugar
Você pode criar clusters do Service Fabric em muitos ambientes, incluindo o Azure ou no local, no Windows Server ou no Linux. Além disso, o ambiente de desenvolvimento no SDK é idêntico ao ambiente de produção, sem nenhum emulador envolvido. Em outras palavras, se ele for executado no cluster de desenvolvimento local, ele será implantado no mesmo cluster em outros ambientes.

Para saber mais sobre a criação de clusters locais, leia sobre [como criar um cluster no Windows Server ou no Linux](service-fabric-deploy-anywhere.md) ou, para o Azure, como criar um cluster [por meio do Portal do Azure](service-fabric-cluster-creation-via-portal.md).

![Plataforma Service Fabric][Image1]

## <a name="stateless-and-stateful-service-fabric-microservices"></a>Microsserviços da Malha do Serviço com e sem estado
O Service Fabric permite criar aplicativos que consistem em microsserviços. Os microsserviços sem estado (gateways de protocolo, proxies Web, etc.) não mantêm um estado mutável fora de qualquer solicitação e de sua resposta do serviço. As funções de trabalho dos Serviços de Nuvem do Azure são um exemplo de serviço sem estado. Os microsserviços com estado (contas de usuário, bancos de dados, dispositivos, carrinhos de compra, filas, etc.) mantêm um estado mutável e autoritativo além da solicitação e de sua resposta. Os aplicativos em escala da Internet de hoje consistem em uma combinação de microsserviços com e sem estado.

Por que ter microsserviços com estado junto microsserviços sem estado? Os dois motivos principais são:

1. A capacidade de criar alta taxa de transferência, baixa latência, processamento de transações online tolerante a falha (OLTP), mantendo o código e o fechamento de dados no mesmo computador. Alguns exemplos são vitrines interativas, pesquisa, sistemas de Internet das coisas (IoT), sistemas de comércio, sistemas de detecção de fraudes e processamento de cartão de crédito e gerenciamento de registros pessoais.
2. Simplificação de design do aplicativo. Os microsserviços com estado eliminam a necessidade de filas e caches adicionais que têm sido tradicionalmente exigidos para atender aos requisitos de disponibilidade e latência de um aplicativo puramente sem estado. Os serviços com estado são, de forma natural, de alta disponibilidade e baixa latência, reduzindo o número de partes móveis para gerenciar em seu aplicativo como um todo.

Para saber mais sobre padrões de aplicativo com o Service Fabric, leia [Cenários de aplicativos](service-fabric-application-scenarios.md) e [Escolhendo uma estrutura de modelo de programação](service-fabric-choose-framework.md) para seu serviço

## <a name="application-lifecycle-management"></a>Gerenciamento do ciclo de vida de aplicativos
O Service Fabric oferece um excelente suporte ao ALM (gerenciamento do ciclo de vida do aplicativo) completo de aplicativos em nuvem, desde o desenvolvimento, passando por implantação, gerenciamento diário, manutenção até o possível encerramento.

Os recursos do ALM da Malha do Serviço permitem que os administradores de aplicativos/operadores de TI usem fluxos de trabalho simples com pouco envolvimento humano para provisionar, implantar e monitorar aplicativos, bem como aplicar patch neles. Esses fluxos de trabalho internos reduzem enormemente a carga em operações de TI para manter os aplicativos disponíveis continuamente.

A maioria dos aplicativos consiste em uma combinação de microsserviços com e sem estado e outros executáveis/tempos de execução que são implantados juntos. Tendo tipos fortes nos aplicativos e microsserviços empacotados, o Service Fabric permite a implantação de várias instâncias de aplicativo. Cada instância é gerenciada e atualizada independentemente. Vale destacar que o Service Fabric é capaz de implantar *qualquer* executável ou tempo de execução e torná-lo confiável. Por exemplo, o Service Fabric implanta ASP.NET Core 1, Node.js, Java VMs, scripts ou qualquer outro item que componha seu aplicativo.

Para saber mais sobre o gerenciamento de ciclo de vida de aplicativos, leia [Ciclo de vida do aplicativo](service-fabric-application-lifecycle.md), e sobre a implantação de qualquer código, confira [Implantar um executável convidado](service-fabric-deploy-existing-app.md)

## <a name="key-capabilities"></a>Principais recursos
Ao usar a Malha do Serviço, você pode:

* Desenvolver aplicativos massivamente escalonáveis, que são autorrecuperáveis.
* Desenvolva aplicativos compostos por microsserviços usando o modelo de programação do Service Fabric. Ou, simplesmente executáveis convidado de host e outras estruturas de aplicativo de sua escolha, como ASP.NET Core 1 ou Node.js.
* Desenvolva microsserviços com e sem monitoração de estado altamente confiáveis.
* Simplifique o design do seu aplicativo usando microsserviços com monitoramento de estado em vez de caches e filas.
* Implante no Azure ou em nuvens locais que executam o Windows Server ou o Linux sem qualquer mudança no código. Escreva uma vez em qualquer lugar e implante em qualquer cluster do Service Fabric.
* Desenvolver usando uma abordagem "datacenter no seu computador". O ambiente de desenvolvimento local usa o mesmo código que é executado nos datacenters do Azure.
* Implantar aplicativos em segundos.
* Implantar aplicativos em densidade mais alta do que as máquinas virtuais, implantando centenas ou milhares de aplicativos por computador.
* Implantar diferentes versões do mesmo aplicativo lado a lado, cada um atualizável de modo independente.
* Gerenciar o ciclo de vida dos seus aplicativos com estado sem nenhum tempo de inatividade, incluindo atualizações imediatas ou não.
* Gerenciar aplicativos usando APIs .NET, PowerShell ou interfaces REST.
* Atualizar microsserviços e aplicar patch neles dentro dos aplicativos de modo independente.
* Monitorar e diagnosticar a integridade de seus aplicativos e definir políticas para realizar reparos automáticos.
* Escalar vertical ou horizontalmente o cluster do Service Fabric com facilidade, sabendo que os aplicativos são dimensionados de acordo com os recursos disponíveis.
* Observe o balanceador de recursos de auto-recuperação administrar a redistribuição de aplicativos no cluster. O Service Fabric se recupera de falhas e otimiza a distribuição a distribuição de carga com base nos recursos disponíveis.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
* Para mais informações:
  * [Por que usar uma abordagem de microsserviço para construir aplicativos?](service-fabric-overview-microservices.md)
  * [Visão geral da terminologia](service-fabric-technical-overview.md)
* Configurando o [ambiente de desenvolvimento](service-fabric-get-started.md)  
* [Como escolher uma estrutura do modelo de programação](service-fabric-choose-framework.md) para seu serviço

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png



<!--HONumber=Oct16_HO2-->


