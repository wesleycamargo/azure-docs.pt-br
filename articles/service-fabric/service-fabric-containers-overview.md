<properties
   pageTitle="Visão Geral do Service Fabric e Contêineres | Microsoft Azure"
   description="Uma visão geral do Service Fabric e o uso de contêineres para implantar aplicativos de microsserviço. Este artigo fornece uma visão geral de como os contêineres podem ser usados e os recursos disponíveis no Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="msfussell"/>

# Preview: Service Fabric e contêineres 

>[AZURE.NOTE] Este recurso está em preview para o Linux e não está disponível no Windows Server no momento. Ele estará em preview para o Windows Server na próxima versão do Service Fabric após o lançamento do Windows Server 2016 GA e com suporte na versão subsequente depois disso.

## Introdução
O Service Fabric é um [orquestrador](service-fabric-cluster-resource-manager-introduction.md) de serviços em um cluster de computadores. Os serviços podem ser desenvolvidos de várias maneiras de usar os [modelos de programação do Service Fabric ](service-fabric-choose-framework.md) para implantar [executáveis de convidado](service-fabric-deploy-existing-app.md). Por padrão, o Service Fabric implanta e ativa esses serviços como processos. Processos fornecem a ativação mais rápida e o uso de densidade mais alto dos recursos no cluster. O Service Fabric também pode implantar serviços nas imagens de contêiner e, de forma importante, você pode misturar os serviços em processos e serviços em contêineres no mesmo aplicativo. Você tem o melhor dos dois mundos dependendo do cenário.

## O que são contêineres?
Contêineres são componentes encapsulados, individualmente implantáveis em execução como instâncias isoladas no mesmo kernel, utilizando a virtualização no nível do sistema operacional. Isso significa que cada aplicativo, seu tempo de execução, suas dependências e suas bibliotecas de sistema são executadas dentro de um contêiner com acesso privado e completo a sua exibição isolada dos constructos do sistema operacional. Junto com a portabilidade, esse nível de isolamento de segurança e de recursos é o principal benefício para o uso de contêineres com o Service Fabric, que, caso contrário, executa serviços em processos.

Contêineres são uma tecnologia de virtualização que virtualiza o sistema operacional subjacente de aplicativos. Eles fornecem um ambiente imutável para que aplicativos sejam executados com graus variados de isolamento. Contêineres são executados diretamente sobre o kernel e tem um modo de exibição isolado do sistema de arquivos e de outros recursos. Em comparação com máquinas virtuais, contêineres têm as seguintes vantagens:

1.  **Tamanho pequeno**: contêineres usam um único espaço de armazenamento e deltas menores para cada camada, portanto são mais eficientes.

2.  **Rápida inicialização**: contêineres não precisam inicializar um SO, portanto podem estar ativos muito mais rapidamente do que VMs, normalmente segundos.

3.  **Portabilidade**: uma imagem de aplicativo em contêiner pode ser portada para execução na nuvem ou localmente, dentro de VMs ou diretamente em computadores físicos.

4.  **Forneça governança de recursos** para limitar os recursos físicos que um contêiner pode consumir em seu host.

## Tipos de contêiner
O Service Fabric dá suporte aos tipos a seguir de contêineres:

### Contêineres de Docker no Linux
O Docker fornece APIs de nível superior para criar e gerenciar contêineres sobre contêineres de kernel do Linux e fornece o Docker Hub como um repositório central para armazenar e recuperar imagens de contêiner.

### Contêineres do Windows Server
O Windows Server 2016 fornece dois tipos diferentes de contêineres que diferem no nível de isolamento fornecido. Contêineres do Windows Server são semelhantes a contêineres do Docker porque têm o isolamento de sistema de arquivos e namespace, mas compartilham o kernel com o host no qual estão sendo executados. No Linux, esse isolamento tradicionalmente foi fornecido por cgroups e namespaces, e contêineres do Windows Server se comportam da mesma forma.

Contêineres do Windows Hyper-V fornecem um grau mais alto de isolamento e segurança, pois cada contêiner não compartilha o kernel do SO entre eles ou com o host. Com este nível mais alto de isolamento de segurança, os Contêineres do Hyper-V são especialmente marcados como cenários de multilocatário e hostis.

A figura a seguir mostra os diferentes tipos de níveis de isolamento e virtualização disponíveis no SO. ![Plataforma Service Fabric][Image1]

## Cenários de uso de contêineres
Alguns exemplos típicos de quando usar contêineres incluem

1. **Deslocamento e comparação IIS**. Se você tiver alguns aplicativos ASP.NET MVC que gostaria de continuar usando, em vez de migrá-los para o núcleo do ASP.NET, coloque-os em um contêiner. Esses aplicativos ASP.NET MVC são dependentes do IIS. Você pode empacotá-los nas imagens de contêiner da imagem do IIS pré-criada e implantá-los com o Service Fabric. Consulte [Imagens de contêiner no Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images) sobre como criar imagens do IIS.


2. **Misturar contêineres e microsserviços do Service Fabric**. Use uma imagem de contêiner existente para parte de seu aplicativo. Por exemplo, você pode usar o [contêiner NGINX](https://hub.docker.com/_/nginx/) para o front-end da web do seu aplicativo e serviços com estado criados com os Reliable Services para obter a computação mais intensiva de back-end. Um exemplo desse cenário inclui aplicativos de jogos


3. **Reduzir o impacto de serviços de "vizinhos ruidosos"**. A capacidade de governança de recursos de contêineres permite que você restrinja o recurso que usa um serviço em um host. Se houver serviços que podem consumir muitos recursos e, portanto, afetam o desempenho dos outros, (por exemplo, uma consulta demorada como operação), considere colocar esses contêineres com governança de recursos.

## Suporte ao Service Fabric para contêineres
Atualmente, o Service Fabric dá suporte à implantação de contêineres de Docker em contêineres do Linux e do Windows Server no Windows Server 2016. O suporte para contêineres do Hyper-V será adicionado em uma versão futura.

No [modelo de aplicativo](service-fabric-application-model.md) do Service Fabric, um contêiner representa um host de aplicativo no qual várias réplicas de serviço são colocadas. Os cenários a seguir têm suporte para contêineres:

1.	**Contêineres convidados**: idênticos ao [cenário de executável convidado](service-fabric-deploy-existing-app.md) em que você pode implantar aplicativos existentes em um contêiner. Por exemplo, nodejs, javascript ou qualquer código (EXEs).


2.	**Serviços sem monitoração de estado dentro de contêineres**: esses são os serviços sem estado usando os modelos de programação dos Reliable Services e dos Reliable Actors. Atualmente, ele só tem suporte no Linux. O suporte para serviços sem monitoração de estado em contêineres do Windows será adicionado na versão futura.

 
3.	**Serviços com estado dentro de contêineres**: esses são os serviços com estado usando o modelo de programação dos Reliable Actors no Linux. Atualmente, os Reliable Services não têm suporte no Linux. O suporte para serviços com estado em contêineres do Windows será adicionado na versão futura.

O Service Fabric tem vários recursos de contêiner que ajudam na compilação de aplicativos que são compostos por microsserviços que estão em contêineres. Eles são chamados de serviços em contêineres. Os recursos incluem;

- Implantação e ativação de imagens de contêiner
- Governança de recursos
- Autenticação do repositório
- Porta do contêiner para o mapeamento da porta de host
- Descoberta e comunicação de contêiner para contêiner
- Capacidade de configurar e definir as variáveis de ambiente

## Próximas etapas
Neste artigo, você aprendeu sobre contêineres, que o Service Fabric é um orquestrador de contêiner e os recursos que o Service Fabric fornece para dar suporte a contêineres. Como uma próxima etapa, passaremos pelos exemplos de cada um dos recursos para mostrar a você como usá-los.

[Implantar um contêiner no Service Fabric](service-fabric-deploy-container.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png

<!---HONumber=AcomDC_0928_2016-->