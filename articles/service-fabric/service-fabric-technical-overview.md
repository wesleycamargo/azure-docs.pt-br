---
title: Aprenda a terminologia do Azure Service Fabric | Microsoft Docs
description: "Uma visão geral da terminologia do Service Fabric. Aborda os principais conceitos de terminologia e os termos usados no restante da documentação."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2017
ms.author: ryanwi
ms.openlocfilehash: 2204fe7393cddb88c18415768a4c6f8494e83d7b
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2017
---
# <a name="service-fabric-terminology-overview"></a>Visão geral da terminologia do Service Fabric
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços escalonáveis e confiáveis. Este artigo apresenta detalhes da terminologia usada pelo Service Fabric para compreensão dos termos usados na documentação.

Os conceitos listados nesta seção também são abordados nos seguintes vídeos da Microsoft Virtual Academy: <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">Conceitos principais</a>, <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965">Conceitos de tempo de design</a> e <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">Conceitos de tempo de execução</a>.

## <a name="infrastructure-concepts"></a>Conceitos de infraestrutura
**Cluster**: um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados.  Os clusters podem ser dimensionados para milhares de máquinas.

**Nó**: um computador ou VM que faz parte de um cluster é chamado de *nó*. Cada nó recebe um nome de nó (uma cadeia de caracteres). Os nós têm características, como propriedades de posicionamento. Cada computador ou VM tem um serviço Windows de início automático, `FabricHost.exe` que começa a ser executado na inicialização e então inicia dois executáveis: `Fabric.exe` e `FabricGateway.exe`. Esses dois executáveis compõem o nó. Para cenários de teste, você pode hospedar vários nós em um único computador ou VM, executando várias instâncias do `Fabric.exe` e do `FabricGateway.exe`.

## <a name="application-concepts"></a>Conceitos de aplicativo
**Tipo de Aplicativo**: o nome/versão atribuído a uma coleção de tipos de serviço. É definido em um arquivo `ApplicationManifest.xml` e inserido em um diretório do pacote de aplicativo. O diretório então é copiado para o repositório de imagens do cluster do Service Fabric. Assim, você pode criar um aplicativo nomeado desse tipo de aplicativo dentro do cluster.

Leia o artigo [Modelo de aplicativo](service-fabric-application-model.md) para obter mais informações.

**Pacote de aplicativos**: um diretório de disco contendo o arquivo `ApplicationManifest.xml` do tipo de aplicativo. Faz referência aos pacotes de serviço de cada tipo de serviço que compõe o tipo de aplicativo. Os arquivos no diretório do pacote de aplicativos são copiados no armazenamento de imagens do cluster do Service Fabric. Por exemplo, um pacote de aplicativos para um tipo de aplicativo de email pode conter referências a um pacote de serviços fila, um pacote de serviços front-end e um pacote de serviços de banco de dados.

**Aplicativo de chamada**: depois de copiar um pacote de aplicativos para o repositório de imagens, você cria uma instância do aplicativo no cluster. Você cria uma instância ao especificar o tipo de aplicativo do pacote de aplicativo usando seu nome ou versão. Cada instância de tipo de aplicativo recebe um nome de URI (Uniform Resource Identifier) que se parece com: `"fabric:/MyNamedApp"`. Em um cluster, você pode criar vários aplicativos nomeados a partir de um tipo de aplicativo único. Você também pode criar aplicativos nomeados a partir de diferentes tipos de aplicativos. Cada aplicativo nomeado é gerenciado e possui controle de versão independente.      

**Tipo de Serviço**: o nome/versão atribuído aos pacotes de códigos, pacotes de dados e pacotes de configuração de um serviço. O tipo de serviço é definido no arquivo `ServiceManifest.xml` e inserido em um diretório do pacote de serviço. O diretório do pacote de serviço então é referenciado por um arquivo `ApplicationManifest.xml` do pacote de aplicativo. Dentro do cluster, depois de criar um aplicativo nomeado, você pode criar um serviço nomeado a partir de um dos tipos de serviço do tipo de aplicativo. O arquivo `ServiceManifest.xml` do tipo de serviço descreve o serviço.

Leia o artigo [Modelo de aplicativo](service-fabric-application-model.md) para obter mais informações.

Há dois tipos de serviço:

* **Sem monitoração de estado:** use um serviço sem estado quando o estado persistente do serviço for armazenado em um serviço de armazenamento externo, como o Armazenamento do Azure, o Banco de Dados SQL do Azure ou Azure Cosmos DB. Use um serviço sem estado quando o serviço não tiver armazenamento persistente. Por exemplo, para um serviço de calculadora no qual os valores são passados para o serviço, um cálculo é realizado usando esses valores e então um resultado é retornado.
* **Com estado**: use um serviço com estado quando quiser que o Service Fabric gerencie o estado de seu serviço por meio de seus modelos de programação Reliable Collections ou Reliable Actors. Ao criar um serviço nomeado, especifique quantas partições você deseja distribuir pelo seu estado para fins de escalabilidade. Especifique também quantas vezes replicar o estado entre os nós (para fins de confiabilidade). Cada serviço nomeado tem uma única réplica primária e várias réplicas secundárias. Você modifica o estado do serviço nomeado ao gravar na réplica primária. O Service Fabric então replica esse estado para todas as réplicas secundárias para manter seu estado em sincronia. O Service Fabric detecta automaticamente quando uma réplica primaria falha e promove uma réplica secundária existente a réplica primária. O Service Fabric cria então uma nova réplica secundária.  

**Réplicas ou instâncias** referem-se ao código (e ao estado para serviços com estado) de um serviço implantado e em execução. Consulte [Réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md).

**Reconfiguração** refere-se ao processo de qualquer alteração no conjunto de réplicas de um serviço. Consulte [Reconfiguration](service-fabric-concepts-reconfiguration.md).

**Pacote de serviços**: um diretório de disco contendo o arquivo `ServiceManifest.xml` do tipo de serviço. Este arquivo faz referência ao código, aos dados estáticos e a pacotes de configuração para o tipo de serviço. Os arquivos no diretório do pacote de serviços são referenciados pelo arquivo `ApplicationManifest.xml` do tipo de aplicativo. Por exemplo, um pacote de serviços pode fazer referência ao código, aos dados estáticos e aos pacotes de configuração que compõem um serviço de banco de dados.

**Serviço nomeado**: depois de criar um aplicativo nomeado, você pode criar uma instância de um de seus tipos de serviço dentro do cluster. Você especifica o tipo de serviço usando seu nome/versão. Cada instância de tipo de serviço recebe um nome de URI com escopo dentro do URI de seu aplicativo nomeado. Por exemplo, se você criar um serviço chamado "MyDatabase" dentro de um aplicativo chamado "MyNamedApp", o URI terá esta aparência: `"fabric:/MyNamedApp/MyDatabase"`. Dentro de um aplicativo nomeado, você pode criar vários serviços nomeados. Cada serviço nomeado pode ter seu próprio esquema de partição e contagens de instância ou réplica.

**Pacote de códigos**: um diretório de disco contendo arquivos executáveis do tipo de serviço, normalmente, arquivos EXE/DLL. Os arquivos no diretório do pacote de códigos são referenciados pelo arquivo `ServiceManifest.xml` do tipo de serviço. Quando você cria um serviço nomeado, o pacote de códigos é copiado para os nós selecionados para executar o serviço nomeado. Então o código começa a ser executado. Há dois tipos de arquivos executáveis de pacote de códigos:

* **Executáveis convidados**: executáveis que são executados “como são” no sistema operacional host (Windows ou Linux). Esses executáveis não criam um vínculo nem fazem referência a arquivos de tempo de execução do Service Fabric e, portanto, não usam nenhum modelo de programação do Service Fabric. Esses executáveis não conseguem usar alguns recursos do Service Fabric, como o serviço de nomenclatura para descoberta de ponto de extremidade. Executáveis convidados não podem relatar métricas de carga específicas para cada instância de serviço.
* **Executáveis do host de serviço**: são executáveis que usam modelos de programação do Service Fabric criando vínculos com arquivos de tempo de execução do Service Fabric, habilitando os recursos do Service Fabric. Por exemplo, uma instância de serviço nomeada pode registrar pontos de extremidade com o Serviço de Nomenclatura do Service Fabric, e também pode relatar métricas de carga.      

**Pacote de dados**: um diretório de disco contendo os arquivos de dados estáticos somente leitura do tipo de serviço, normalmente arquivos de foto, som e vídeo. Os arquivos no diretório do pacote de dados são referenciados pelo arquivo `ServiceManifest.xml` do tipo de serviço. Quando você cria um serviço nomeado, o pacote de dados é copiado para os nós ou os nós selecionados para executar o serviço nomeado. O código começa a ser executado e agora pode acessar os arquivos de dados.

**Pacote de configuração**: um diretório de disco contendo os arquivos de configuração estáticos somente leitura do tipo de serviço, normalmente arquivos de texto. Os arquivos no diretório do pacote de configuração são referenciados pelo arquivo `ServiceManifest.xml` do tipo de serviço. Quando você cria um serviço nomeado, os arquivos no pacote de configuração são copiados para um ou mais nós selecionados para executar o serviço nomeado. O código começa a ser executado e agora pode acessar os arquivos de configuração.

**Contêineres**: por padrão, o Service Fabric implanta e ativa esses serviços como processos. O Service Fabric também pode implantar serviços em imagens de contêiner. Contêineres são uma tecnologia de virtualização que virtualiza o sistema operacional subjacente de aplicativos. Um aplicativo e seu tempo de execução, dependências e bibliotecas do sistema são executados dentro de um contêiner. O contêiner tem acesso privado total à exibição isolada do próprio contêiner dos constructos de sistema operacional. O Service Fabric dá suporte a contêineres do Docker em contêineres Linux e Windows Server. Para obter mais informações, leia [Service Fabric e contêineres](service-fabric-containers-overview.md).

**Esquema de partição**: ao criar um serviço nomeado, você especifica um esquema de partição. Serviços com quantidades substanciais de estado dividem os dados entre as partições, que distribuem o estado pelos nós do cluster. Ao dividir os dados entre partições, você pode dimensionar o estado do serviço nomeado. Dentro de uma partição, serviços nomeados sem monitoração de estado têm instâncias, enquanto serviços nomeados com estado têm réplicas. Geralmente, serviços nomeados sem monitoração de estado têm apenas uma partição, pois não têm estado interno. As instâncias de partição proporcionam disponibilidade. Se uma instância falhar, outras instâncias continuarão funcionando normalmente, então o Service Fabric criará uma nova instância. Serviços nomeados com estado mantêm seu estado dentro de réplicas e cada partição tem sua própria réplica, de modo que o estado é mantido em sincronia. Caso uma réplica falhe, o Service Fabric compila uma nova réplica a partir das réplicas existentes.

Leia o artigo [Particionar os Reliable Services do Service Fabric](service-fabric-concepts-partitioning.md) para saber mais.

## <a name="system-services"></a>Serviços do sistema
Há serviços do sistema que são criados em cada cluster que fornece os recursos de plataforma do Service Fabric.

**Serviço de nomenclatura**: cada cluster do Service Fabric tem um Serviço de Nomenclatura que resolve nomes de serviço para uma localização no cluster. Você gerencia os nomes de serviço e propriedades, como um DNS (Serviço de Nomes de Domínio ) da Internet para o cluster. Os clientes comunicam-se com segurança com qualquer nó no cluster usando o Serviço de Nomenclatura para resolver um nome de serviço e sua localização. Os aplicativos movem-se dentro do cluster. Por exemplo, isso pode ser devido a falhas, balanceamento de recursos ou o redimensionamento do cluster. Você pode desenvolver serviços e clientes que resolvem o local de rede atual. Os clientes obtêm o endereço IP do computador real e a porta em que ele está sendo executado.

Leia [Comunicar-se com os serviços](service-fabric-connect-and-communicate-with-services.md) para obter mais informações sobre as APIs de comunicação do serviço e do cliente que funcionam com o Serviço de Nomenclatura.

**Serviço de Repositório de Imagens**: cada cluster do Service Fabric tem um Serviço de Repositório de Imagens em que os pacotes de aplicativos implantados com versão são mantidos. Copie um pacote de aplicativos para o Repositório de Imagens e registre o tipo de aplicativo nesse pacote de aplicativos. Após o provisionamento do tipo de aplicativo, você cria um aplicativo nomeado com base nele. Você pode cancelar o registro de um tipo de aplicativo do serviço de Repositório de Imagens após a exclusão de todos os seus aplicativos nomeados.

Leia [Entender a configuração ImageStoreConnectionString](service-fabric-image-store-connection-string.md) para saber mais sobre o serviço de Armazenamento de Imagens.

Leia o artigo [Implantar um aplicativo](service-fabric-deploy-remove-applications.md) para saber mais sobre como implantar aplicativos no serviço do Repositório de Imagens.

**Serviço do Gerenciador de Failover**: cada cluster do Service Fabric tem um serviço de Gerenciador de Failover responsável pelas seguintes ações:
   - Executa funções relacionadas a alta disponibilidade e consistência dos serviços.
   - Orquestra upgrades de aplicativo e de cluster.
   - Interage com outros componentes do sistema.

## <a name="built-in-programming-models"></a>Modelos de programação internos
Há dois modelos de programação do .NET Framework disponíveis para compilação de serviços do Service Fabric:

**Reliable Services**: uma API para criar serviços com e sem estado. O serviço com estado armazena seu estado em Reliable Collections, como um dicionário ou uma fila. Você também pode conectar várias pilhas de comunicação, como API Web e WCF (Windows Communication Foundation).

**Reliable Actors**: uma API para criar objetos com e sem estado por meio do modelo de programação de ator virtual. Esse modelo é útil quando você tem muitas unidades independentes de computação ou estado. Esse modelo usa um modelo de threading baseado em turno, assim, é melhor evitar um código que chame outros atores ou serviços, já que um ator individual não pode processar outras solicitações de entrada até que todas as suas solicitações de saída estejam concluídas.

Leia o artigo [Escolher um modelo de programação para seu serviço](service-fabric-choose-framework.md) para saber mais.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Service Fabric:

* [Visão geral da Malha do Serviço](service-fabric-overview.md)
* [Por que usar uma abordagem de microsserviço para construir aplicativos?](service-fabric-overview-microservices.md)
* [Cenários de aplicativos](service-fabric-application-scenarios.md)


