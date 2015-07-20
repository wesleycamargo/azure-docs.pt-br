<properties 
   pageTitle="Visão geral da Malha do Serviço" 
   description="Uma visão geral da Malha do Serviço onde aplicativos são compostos por microsserviços. A Malha do Serviço é uma plataforma de sistemas distribuídos usada para criar aplicativos escalonáveis, confiáveis e facilmente gerenciados para a nuvem" 
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
   ms.date="04/14/2015"
   ms.author="mfussell"/>

# Visão geral da Malha do Serviço
A Malha do Serviço é uma plataforma de sistemas distribuídos usada para criar aplicativos escalonáveis, confiáveis e facilmente gerenciados para a nuvem. A Malha do Serviço resolve os desafios significativos do desenvolvimento e gerenciamento de aplicativos em nuvem. Ao usar a Malha do Serviço, desenvolvedores e administradores podem evitar a resolução de problemas complexos de infraestrutura e, em vez disso, se concentram na implementação de cargas de trabalho essenciais e exigentes sabendo que elas são escalonáveis, confiáveis e gerenciáveis. A Malha do Serviço representa a plataforma de middleware de última geração para criação e gerenciamento de serviços dimensionáveis de nuvem de Camada 1 e nível corporativo.

## Aplicativos compostos por microsserviços ##
A Malha do Serviço permite criar e gerenciar aplicativos escalonáveis e confiáveis compostos por microsserviços que são executados em densidade bastante alta em um pool compartilhado de computadores (geralmente conhecido como um cluster da Malha do Serviço). Ela fornece um tempo de execução sofisticado para criação de microsserviços com e sem estado, distribuídos e escalonáveis, bem como recursos abrangentes de gerenciamento de aplicativos para provisionamento, implantação, monitoramento, atualização/aplicação de patch e exclusão de aplicativos implantados.

A Malha do Serviço é a tecnologia de muitos serviços atuais da Microsoft, como os Bancos de Dados SQL do Azure, Banco de Dados de Documentos do Azure, Cortana, Power BI, Microsoft Intune, Hubs de Eventos do Azure, muitos Serviços de núcleo do Azure e Skype for Business, apenas para citar alguns.

A Malha do Serviço foi personalizada para a criação de serviços “nascidos na nuvem” que podem começar pequenos, conforme a necessidade, e se expandir em escala massiva com centenas ou milhares de computadores, criando clusters da Malha do Serviço entre conjuntos de disponibilidade em uma região ou entre regiões.

Os serviços em escala da Internet de hoje são criados usando microsserviços. Exemplos de microsserviços são gateways de protocolo, perfis de usuário, carrinhos de compra, processamento de inventário, filas, caches, etc. A Malha do Serviço é uma plataforma de microsserviços que fornece a cada microsserviço um nome exclusivo que pode ser com ou sem estado.

A Malha do Serviço fornece recursos abrangentes de gerenciamento de ciclo de vida e tempo de execução para aplicativos compostos por esses microsserviços. Ela hospeda microsserviços em contêineres que são implantados e ativados no cluster da Malha do Serviço. Assim como um aumento gigantesco na densidade é possibilitado ao passar de VMs para contêineres, uma ordem de grandeza semelhante na densidade se torna possível ao passar de contêineres para microsserviços. Por exemplo, um único cluster de Banco de Dados SQL do Azure, que é criado na Malha do Serviço, engloba centenas de computadores que executam dezenas de milhares de contêineres que hospedam um total de centenas de milhares de bancos de dados (cada banco de dados é um microsserviço com estado da Malha do Serviço). O mesmo se aplica aos Hubs de Eventos e a outros serviços mencionados acima. Isso porque o termo hiperescala pode ser usado para descrever recursos da Malha do Serviço — se contêineres fornecerem alta densidade, então os microsserviços fornecerão hiperescala.

![Plataforma da Malha do Serviço][Image1]

## Microsserviços da Malha do Serviço com e sem estado

Os microsserviços sem estado (por ex., gateways de protocolo, proxies Web, etc.) não mantêm estado mutável fora de nenhuma solicitação e de sua resposta do serviço. As funções de trabalho dos Serviços de Nuvem do Azure são um exemplo de serviço sem estado. Os microsserviços com estado (por ex., contas de usuário, bancos de dados, dispositivos, carrinhos de compra, filas, etc.) mantêm estado mutável e autoritativo além da solicitação e de sua resposta. Os aplicativos em escala da Internet de hoje consistem em uma combinação de microsserviços com e sem estado.
 
Por que os microsserviços com estado são importantes? Por que não usar serviços sem estado simplesmente para tudo? Por dois motivos:

1) Capacidade de criar serviços OLTP tolerantes à falha, com alta taxa de transferência e baixa latência, como fachadas interativas, pesquisa, sistema de Internet das Coisas (IoT), sistemas comerciais, processamento de cartão de crédito e sistemas de detecção de fraude, gerenciamento de registros pessoais, etc., mantendo código e dados encerrados no mesmo computador.

2) Simplificação de design de aplicativo, pois os microsserviços com estado eliminam a necessidade de filas e caches adicionais que têm sido tradicionalmente exigidos para atender aos requisitos de disponibilidade e latência de um aplicativo puramente sem estado. Uma vez que os serviços com estado são, de forma natural, altamente disponíveis e de baixa latência, isso significa menos partes móveis para gerenciar no seu aplicativo como um todo.

Para obter mais informações sobre padrões e design de aplicativos usando a Malha do Serviço, consulte [Cenários de aplicativos](../service-fabric-application-scenarios)

## Gerenciamento do ciclo de vida de aplicativos
A Malha do Serviço fornece suporte de primeira classe para o ALM (gerenciamento do ciclo de vida de aplicativos) completo de aplicativos em nuvem: do desenvolvimento à implantação, ao gerenciamento diário, à manutenção e à desativação final.

Os recursos do ALM da Malha do Serviço permitem que os administradores de aplicativos/operadores de TI usem fluxos de trabalho simples com pouco envolvimento humano para provisionar, implantar e monitorar aplicativos, bem como aplicar patch neles. Esses fluxos de trabalho internos reduzem enormemente a carga em operações de TI para manter os aplicativos disponíveis continuamente.

A maioria dos aplicativos consiste em uma combinação de microsserviços com e sem estado e outros EXE/tempos de execução que são implantados juntos. Tendo tipos fortes nos aplicativos e os microsserviços empacotados, a Malha do Serviço permite a implantação de várias instâncias de aplicativo, e cada uma delas pode ser gerenciada e atualizada de maneira independente. Vale destacar que a Malha do Serviço é capaz de implantar *qualquer* executável ou tempo de execução e torná-los confiáveis. Por exemplo, ela pode ser usada para implantar ASP.NET 5, node.js, scripts ou qualquer item que componha seu aplicativo.
  
Para obter mais informações sobre o gerenciamento do ciclo de vida de aplicativos, consulte [Ciclo de vida de aplicativos](../service-fabric-application-lifecycle)

## Principais recursos
Ao usar a Malha do Serviço, você pode:

- Desenvolver aplicativos massivamente escalonáveis, que são autorrecuperáveis.

- Desenvolver usando uma abordagem “datacenter no seu computador”. O ambiente de desenvolvimento local usa o mesmo código que é executado nos datacenters do Azure.
 
- Desenvolver aplicativos compostos por microsserviços, executáveis e outras estruturas de aplicativo de sua escolha, como ASP.NET, node.js, etc.

- Desenvolver serviços/microsserviços com e sem estado e torná-los altamente confiáveis.

- Simplificar o design do seu aplicativo usando serviços/microsserviços com estado no lugar de caches e filas.
 
- Implantar aplicativos em segundos.

- Implantar no Azure ou em nuvens locais executando o Windows Server sem mudanças no código. Escreva uma vez e implante em qualquer cluster da Malha do Serviço.

- Implantar aplicativos em densidade mais alta do que as máquinas virtuais, implantando centenas ou milhares de aplicativos por computador.

- Implantar diferentes versões do mesmo aplicativo lado a lado, cada um atualizável de modo independente.
 
- Gerenciar o ciclo de vida dos seus aplicativos com estado sem nenhum tempo de inatividade, incluindo atualizações imediatas ou não.

- Gerenciar aplicativos usando APIs .NET, PowerShell ou interfaces REST.
 
- Atualizar microsserviços e aplicar patch neles dentro dos aplicativos de modo independente.

- Monitorar e diagnosticar a integridade de seus aplicativos e definir políticas para realizar reparos automáticos.

- Ampliar ou reduzir com facilidade o cluster da Malha do Serviço, sabendo que os aplicativos são dimensionados de acordo com os recursos disponíveis.

- Observar o balanceador de recursos de autorrecuperação administrar a redistribuição de aplicativos entre os clusters da Malha do Serviço para recuperar-se de falhas e otimizar a distribuição da carga com base nos recursos disponíveis.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Para obter mais informações: [Visão geral técnica](../service-fabric-technical-overview).

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

 

<!---HONumber=July15_HO2-->