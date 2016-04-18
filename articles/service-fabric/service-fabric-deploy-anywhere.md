<properties
   pageTitle="Criar cluster do Azure Service Fabric no Windows Server e Linux | Microsoft Azure"
   description="Os clusters do Service Fabric são executados no Windows Server e Linux, o que significa que você poderá implantar e hospedar aplicativos do Service Fabric em qualquer lugar que possa executar o Windows Server ou Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/05/2016"
   ms.author="chackdan"/>

# Criar clusters autônomos do Service Fabric no Windows Server ou Linux
O Azure Service Fabric permite a criação de clusters do Service Fabric em quaisquer VMs ou computadores que estejam executando o Windows Server ou o Linux. Isso significa que você poderá implantar e executar aplicativos do Service Fabric em qualquer ambiente em que tenha um conjunto de computadores Windows Server ou Linux interconectados, esteja ele no local ou com algum provedor de nuvem.

**Observação**: a criação de um cluster no Azure deve ser realizada por meio de um modelo do Azure Resource ou no Portal do Azure. Leia [Criar um cluster do Service Fabric usando um modelo do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) ou [Criar um cluster do Service Fabric no Portal do Azure](service-fabric-cluster-creation-via-portal.md) para obter mais informações.

O Service Fabric fornece um pacote de instalação para você criar esses clusters autônomos do Service Fabric no local. O principal benefício disso é não ficar preso a um fornecedor quando você cria um aplicativo usando o Service Fabric, já que escolhe onde esses aplicativos são executados. Isso também aumenta sua capacidade de atingir uma base maior de clientes, pois os clientes podem ter requisitos variados para os ambientes em que eles querem executar os aplicativos. Por exemplo, os clientes nos setores de saúde e financeiro podem ter diferentes necessidades dos clientes nos setores automobilístico ou turístico.

## Sistemas operacionais com suporte
Você poderá criar clusters em VMs ou computadores que executam estes sistemas operacionais:

* Windows Server 2012 R2
* Windows Server 2016
* Linux

Para saber mais sobre o Windows Server, leia [Criação de clusters do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)

## Criação e configuração de cluster
O Service Fabric fornece um pacote de instalação que você pode baixar. Depois de baixar esse pacote, é preciso fazer alterações em um arquivo de configuração JSON para especificar as configurações do seu cluster. Depois de editar as configurações do cluster, você executa um script de instalação que cria o cluster abrangendo os computadores especificados nas configurações do cluster. Também é possível executar um script para remover um cluster de um conjunto de computadores.

## Qualquer implantação na nuvem vs. implantações locais
O processo de criação de um cluster local do Service Fabric é semelhante ao processo de criação de um cluster em qualquer nuvem de sua escolha com um conjunto de VMs. As etapas iniciais para provisionar as VMs serão administradas pelo provedor de nuvem ou pelo ambiente local que você estiver usando. Depois que você tiver um conjunto de VMs com conectividade de rede habilitada entre elas, as etapas para configurar o pacote do Service Fabric, editar as configurações do cluster e executar os scripts de gerenciamento e criação do cluster serão idênticas. Isso garante que o seu conhecimento e experiência com a operação e o gerenciamento de clusters do Service Fabric poderão ser transferidos quando você optar por novos ambientes de hospedagem.

## Benefícios de criar clusters autônomos do Service Fabric
* Como não há dependência de fornecedor, você pode escolher onde criar o cluster.
* Os aplicativos do Service Fabric, uma vez escritos, podem ser executados em vários ambientes de hospedagem com pouca ou nenhuma alteração.
* O conhecimento sobre a criação de aplicativos do Service Fabric é transferido de um ambiente de hospedagem para outro.
* A experiência operacional da execução e do gerenciamento de clusters do Service Fabric é transferida de um ambiente para outro.
* Amplo alcance de clientes, sem limitação de restrições de ambiente de hospedagem.
* Uma camada extra de confiabilidade e proteção contra interrupções generalizadas, permitindo que você mova os serviços para outro ambiente de implantação se um datacenter ou provedor de nuvem sofrer um blecaute.

## Vantagens dos clusters do Service Fabric no Azure sobre clusters autônomos do Service Fabric criados no local
A execução de clusters do Service Fabric no Azure apresenta vantagens em relação à opção local, de modo que se você não tiver necessidades específicas de local para execução dos clusters, sugerimos que os execute no Azure. No Azure, fornecemos integração a outros recursos e serviços do Azure, o que facilita e torna mais confiável as operações e o gerenciamento do cluster.

* **Portal do Azure:** o Portal do Azure facilita a criação e o gerenciamento de clusters.

* **Gerenciador de Recursos do Azure**: o uso do Gerenciador de Recursos do Azure permite o fácil gerenciamento de todos os recursos usados pelo cluster como uma unidade e simplifica o acompanhamento de custos e faturamento.
* **Cluster do Service Fabric como um Recurso do Azure**: um cluster do Service Fabric é um recurso do ARM, de modo que você pode modelá-lo como faz com outros recursos do ARM no Azure.
* **Integração à infraestrutura do Azure** O Service Fabric é coordenado com a infraestrutura subjacente do Azure para sistema operacional, rede e outras atualizações de modo a melhorar a disponibilidade e a confiabilidade dos seus aplicativos.  
* **Diagnóstico**: no Azure, fornecemos integração ao diagnóstico do Azure e aos Insights Operacionais.
* **Dimensionamento automático**: para os clusters no Azure, fornecemos uma funcionalidade interna de dimensionamento automático devido aos conjuntos de escala da Máquina Virtual. Em ambientes locais e em outros de nuvem, você terá que criar seu próprio recurso de dimensionamento automático ou escalar manualmente usando as APIs que o Service Fabric expõe para colocação de clusters em escala.

## Próximas etapas
Crie um cluster em VMs ou em computadores que estejam executando o Windows Server: [Criação de um cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)

Crie um cluster em VMs ou em computadores que estejam executando o Linux: [Service Fabric no Linux](service-fabric-linux-overview.md)

<!---HONumber=AcomDC_0406_2016-->