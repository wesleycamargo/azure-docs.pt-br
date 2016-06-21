<properties
   pageTitle="Visão geral da Service Fabric | Microsoft Azure"
   description="Uma visão geral do Service Fabric no qual os aplicativos são compostos por muitos microsserviços para fornecer dimensionamento e resiliência. O Service Fabric é uma plataforma de sistemas distribuídos usada para criar aplicativos escalonáveis, confiáveis e facilmente gerenciados para a nuvem"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/24/2016"
   ms.author="mfussell"/>

# Visão geral da Malha do Serviço
O Service Fabric é uma plataforma de sistemas distribuídos que torna mais fácil empacotar, implantar e gerenciar microsserviços escalonáveis e confiáveis, bem como resolver desafios significativos no desenvolvimento e gerenciamento de aplicativos em nuvem. Ao usar o Service Fabric, desenvolvedores e administradores podem evitar a resolução de problemas complexos de infraestrutura e, em vez disso, se concentram na implementação de cargas de trabalho essenciais e exigentes sabendo que elas são escalonáveis, confiáveis e gerenciáveis. O Service Fabric representa a plataforma de middleware de última geração para criação e gerenciamento de aplicativos escalonáveis de nuvem de Camada 1 e nível corporativo.

## Aplicativos compostos por microsserviços
O Service Fabric permite criar e gerenciar aplicativos escalonáveis e confiáveis compostos por microsserviços executados a uma densidade bastante alta em um pool compartilhado de computadores (conhecido como um cluster de Service Fabric). Ele fornece um tempo de execução sofisticado para criação de microsserviços distribuídos, escalonáveis, com e sem estado. Ele também fornece recursos abrangentes de gerenciamento de aplicativos para provisionamento, implantação, monitoramento, atualização/aplicação de patch e exclusão de aplicativos implantados.

Por que uma abordagem de microsserviços é importante? Os dois motivos principais são:

1. Eles permitem escalar diferentes partes do seu aplicativo conforme as suas necessidades

2. As equipes de desenvolvimento podem ser mais ágeis na distribuição de alterações, fornecendo assim recursos aos seus clientes mais rápido e com mais frequência.

O Service Fabric é a tecnologia de muitos serviços atuais da Microsoft, como Banco de Dados SQL do Azure, Banco de Dados de Documentos do Azure, Cortana, Power BI, Microsoft Intune, Hubs de Eventos do Azure, Azure IoT, Skype for Business e muitos serviços principais do Azure, apenas para citar alguns.

O Service Fabric foi personalizado para a criação de serviços “nascidos na nuvem” que podem começar pequenos, conforme a necessidade, e expandirem em imensa escala com centenas ou milhares de computadores.

Os serviços em escala da Internet de hoje são criados usando microsserviços. Exemplos de microsserviços são gateways de protocolo, perfis de usuário, carrinhos de compra, processamento de inventário, filas e caches. O Service Fabric é uma plataforma de microsserviços que fornece a cada microsserviço um nome exclusivo que pode ser com ou sem estado.

A Malha do Serviço fornece recursos abrangentes de gerenciamento de ciclo de vida e tempo de execução para aplicativos compostos por esses microsserviços. Ela hospeda microsserviços em contêineres que são implantados e ativados no cluster da Malha do Serviço. Assim como um aumento gigantesco na densidade é possibilitado ao passar de VMs para contêineres, uma ordem de grandeza semelhante na densidade se torna possível ao passar de contêineres para microsserviços. Por exemplo, um único cluster de Banco de Dados SQL do Azure, que é criado no Service Fabric, engloba centenas de computadores que executam dezenas de milhares de contêineres que hospedam um total de centenas de milhares de bancos de dados. (Cada banco de dados é um microsserviço com estado do Service Fabric.) O mesmo se aplica aos Hubs de Eventos e a outros serviços mencionados acima. Por esse motivo o termo "hiperescala" pode ser usado para descrever os recursos do Service Fabric. Se os contêineres fornecem alta densidade, os microsserviços fornecem hiperescala.

Para saber mais sobre a abordagem de microsserviços, leia o artigo [Por que usar uma abordagem de microsserviços para construir aplicativos?](service-fabric-overview-microservices.md)

## Criar clusters de Service Fabric em qualquer lugar
Você pode criar clusters de Service Fabric em muitos ambientes nos quais implantar seus aplicativos. Pode ser no Azure ou localmente, no Windows Server ou no Linux. Além disso, o ambiente de desenvolvimento no SDK é idêntico ao ambiente de produção, sem nenhum emulador envolvido. Em outras palavras, se ele for executado no cluster de desenvolvimento local, ele será implantado no mesmo cluster em outros ambientes.

Para mais informações, leia [Implantar em qualquer lugar no Windows Server ou Linux com o Service Fabric](service-fabric-deploy-anywhere.md)

![Plataforma Service Fabric][Image1]

## Microsserviços da Malha do Serviço com e sem estado

O Service Fabric permite criar aplicativos que consistem em microsserviços. Os microsserviços sem estado (gateways de protocolo, proxies Web, etc.) não mantêm um estado mutável fora de qualquer solicitação e de sua resposta do serviço. As funções de trabalho dos Serviços de Nuvem do Azure são um exemplo de serviço sem estado. Os microsserviços com estado (contas de usuário, bancos de dados, dispositivos, carrinhos de compra, filas, etc.) mantêm um estado mutável e autoritativo além da solicitação e de sua resposta. Os aplicativos em escala da Internet de hoje consistem em uma combinação de microsserviços com e sem estado.

Por que ter microsserviços com estado junto microsserviços sem estado? Os dois motivos principais são:

1. Capacidade de criar serviços OLTP (transação online) tolerantes a falhas, de alta produtividade e baixa latência, como vitrines interativas, pesquisa, sistemas IoT (Internet das Coisas), sistemas comerciais, sistemas de processamento de cartão de crédito e de detecção de fraude, gerenciamento de registros pessoais, etc., mantendo código e dados encerrados no mesmo computador.

2. A simplificação de design de aplicativo, como microsserviços com estado, elimina a necessidade de filas e caches adicionais. Esses últimos têm sido tradicionalmente obrigatórios para atender aos requisitos de disponibilidade e latência de um aplicativo totalmente sem estado. Uma vez que os serviços com estado são, de forma natural, de alta disponibilidade e baixa latência, isso significa menos partes móveis para gerenciar no seu aplicativo como um todo.

Para obter mais informações sobre padrões de aplicativo com o Service Fabric, leia [Cenários de aplicativos](service-fabric-application-scenarios.md) e [Como escolher uma estrutura de modelo de programação](service-fabric-choose-framework.md) para seu serviço

## Gerenciamento do ciclo de vida de aplicativos
O Service Fabric oferece um excelente suporte ao ALM (gerenciamento do ciclo de vida do aplicativo) completo de aplicativos em nuvem, desde o desenvolvimento, passando por implantação, gerenciamento diário, manutenção até o possível encerramento.

Os recursos do ALM da Malha do Serviço permitem que os administradores de aplicativos/operadores de TI usem fluxos de trabalho simples com pouco envolvimento humano para provisionar, implantar e monitorar aplicativos, bem como aplicar patch neles. Esses fluxos de trabalho internos reduzem enormemente a carga em operações de TI para manter os aplicativos disponíveis continuamente.

A maioria dos aplicativos consiste em uma combinação de microsserviços com e sem estado e outros executáveis/tempos de execução que são implantados juntos. Tendo tipos fortes nos aplicativos e microsserviços empacotados, o Service Fabric permite a implantação de várias instâncias de aplicativo, e cada uma delas pode ser gerenciada e atualizada de maneira independente. Vale destacar que o Service Fabric é capaz de implantar *qualquer* executável ou tempo de execução e torná-lo confiável. Por exemplo, ele pode ser usado para implantar ASP.NET Core 1, node.js, Java VM, scripts ou qualquer outro item que componha seu aplicativo.

Para obter mais informações sobre o gerenciamento de ciclo de vida de aplicativos, leia [Ciclo de vida de aplicativos](service-fabric-application-lifecycle.md); sobre a implantação de qualquer código, consulte [Implantar um executável convidado](service-fabric-deploy-existing-app.md)

## Principais recursos
Ao usar a Malha do Serviço, você pode:

- Desenvolver aplicativos massivamente escalonáveis, que são autorrecuperáveis.

- Desenvolva aplicativos compostos por microsserviços usando o modelo de programação do Service Fabric ou simplesmente hospede executáveis convidados e outras estruturas de aplicativo de sua preferência, como ASP.NET Core 1, node.js, etc.

- Desenvolva microsserviços com e sem monitoração de estado e torne-os altamente confiáveis.

- Simplifique o design do seu aplicativo usando microsserviços com monitoramento de estado em vez de caches e filas.

- Implante no Azure ou em nuvens locais que executam o Windows Server ou o Linux sem qualquer mudança no código. Escreva uma vez em qualquer lugar e implante em qualquer cluster do Service Fabric.

- Desenvolver usando uma abordagem "datacenter no seu computador". O ambiente de desenvolvimento local usa o mesmo código que é executado nos datacenters do Azure.

- Implantar aplicativos em segundos.

- Implantar aplicativos em densidade mais alta do que as máquinas virtuais, implantando centenas ou milhares de aplicativos por computador.

- Implantar diferentes versões do mesmo aplicativo lado a lado, cada um atualizável de modo independente.

- Gerenciar o ciclo de vida dos seus aplicativos com estado sem nenhum tempo de inatividade, incluindo atualizações imediatas ou não.

- Gerenciar aplicativos usando APIs .NET, PowerShell ou interfaces REST.

- Atualizar microsserviços e aplicar patch neles dentro dos aplicativos de modo independente.

- Monitorar e diagnosticar a integridade de seus aplicativos e definir políticas para realizar reparos automáticos.

- Escalar vertical ou horizontalmente o cluster do Service Fabric com facilidade, sabendo que os aplicativos são dimensionados de acordo com os recursos disponíveis.

- Observar o balanceador de recursos de autorrecuperação administrar a redistribuição de aplicativos entre os clusters do Service Fabric para se recuperar de falhas e otimizar a distribuição da carga com base nos recursos disponíveis.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

* Para mais informações:
	* [Por que usar uma abordagem de microsserviço para construir aplicativos?](service-fabric-overview-microservices.md)
	* [Visão geral da terminologia](service-fabric-technical-overview.md)
* Configurando o [ambiente de desenvolvimento](service-fabric-get-started.md) do Service Fabric  
* [Como escolher uma estrutura do modelo de programação](service-fabric-choose-framework.md) para seu serviço

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

<!---HONumber=AcomDC_0608_2016-->