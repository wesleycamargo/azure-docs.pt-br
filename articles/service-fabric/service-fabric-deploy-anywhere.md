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
   ms.date="09/22/2016"
   ms.author="chackdan"/>

# Criar clusters do Service Fabric no Windows Server ou no Linux

O Azure Service Fabric permite a criação de clusters do Service Fabric em qualquer VM ou computador que esteja executando o Windows Server ou o Linux. Isso significa que você pode implantar e executar aplicativos do Service Fabric em qualquer ambiente em que tenha um conjunto de computadores do Windows Server ou do Linux interconectados, sejam eles locais, do Microsoft Azure ou de qualquer provedor de nuvem.

##Criar clusters do Service Fabric no Azure

A criação de um cluster no Azure deve ser realizada por intermédio de um modelo do Resource Manager ou do Portal do Azure. Leia [Create a Service Fabric cluster by using a Resource Manager template](service-fabric-cluster-creation-via-arm.md) (Criar um cluster do Service Fabric usando um modelo do Resource Manager) ou [Create a Service Fabric cluster from the Azure portal](service-fabric-cluster-creation-via-portal.md) (Criar um cluster do Service Fabric no Portal do Azure) para obter mais informações.

## Sistemas operacionais com suporte para clusters no Azure

Você pode criar clusters em VMs que executem estes sistemas operacionais:

* Windows Server 2012 R2
* Windows Server 2016 (depois que ele for anunciado como disponível para o público em geral)
* Linux Ubuntu 16.04 (em preview pública)


##Crie clusters autônomos do Service Fabric localmente ou em qualquer provedor de nuvem

O Service Fabric fornece um pacote de instalação para você criar esses clusters autônomos do Service Fabric localmente ou em qualquer provedor de nuvem

Para obter mais informações sobre como configurar os clusters autônomos do Service Fabric no Windows Server, leia [Service Fabric cluster creation for Windows Server](service-fabric-cluster-creation-for-windows-server.md) (Criação de um cluster do Service Fabric para o Windows Server)

### Qualquer implantação na nuvem vs. implantações locais
O processo de criação de um cluster do Service Fabric local é semelhante ao processo de criação de um cluster em qualquer nuvem escolhida com um conjunto de VMs. As etapas iniciais para provisionar as VMs são administradas pelo provedor da nuvem ou pelo ambiente local que você está usando. Depois que você tiver um conjunto de VMs com conectividade de rede habilitada entre elas, as etapas para configurar o pacote do Service Fabric, editar as configurações do cluster e executar os scripts de gerenciamento e criação do cluster serão idênticas. Isso garante que o seu conhecimento e experiência com a operação e o gerenciamento de clusters do Service Fabric poderão ser transferidos quando você optar por novos ambientes de hospedagem.

### Benefícios de criar clusters autônomos do Service Fabric
* Você é livre para escolher qualquer provedor de nuvem para hospedar o cluster.
* Os aplicativos do Service Fabric, uma vez escritos, podem ser executados em vários ambientes de hospedagem com pouca ou nenhuma alteração.
* O conhecimento sobre a criação de aplicativos do Service Fabric é transferido de um ambiente de hospedagem para outro.
* A experiência operacional da execução e do gerenciamento de clusters do Service Fabric é transferida de um ambiente para outro.
* Amplo alcance de clientes, sem limitação de restrições de ambiente de hospedagem.
* Uma camada extra de confiabilidade e proteção contra interrupções generalizadas, permitindo que você mova os serviços para outro ambiente de implantação se um datacenter ou provedor de nuvem sofrer um blecaute.

## Sistemas operacionais com suporte para clusters autônomos
Você pode criar clusters em VMs ou em computadores que executem estes sistemas operacionais:

* Windows Server 2012 R2
* Windows Server 2016 (depois que ele for anunciado como disponível para o público em geral)
* Linux (em breve)

## Vantagens dos clusters do Service Fabric no Azure sobre os clusters autônomos do Service Fabric criados no local

A execução de clusters do Service Fabric no Azure apresenta vantagens em relação à opção local, de modo que se você não tiver necessidades específicas para onde executar seus clusters, sugerimos que os execute no Azure. No Azure, fornecemos integração a outros recursos e serviços do Azure, o que facilita e torna mais confiável as operações e o gerenciamento do cluster.

* **Portal do Azure:** o Portal do Azure facilita a criação e o gerenciamento de clusters.

* **Gerenciador de Recursos do Azure**: o uso do Gerenciador de Recursos do Azure permite o fácil gerenciamento de todos os recursos usados pelo cluster como uma unidade e simplifica o acompanhamento de custos e faturamento.
* **Cluster do Service Fabric como um Recurso do Azure**: um cluster do Service Fabric é um recurso do ARM, portanto, você pode modelá-lo como faz com outros recursos do ARM no Azure.
* **Integração à infraestrutura do Azure** O Service Fabric é coordenado com a infraestrutura subjacente do Azure para sistema operacional, rede e outras atualizações de modo a melhorar a disponibilidade e a confiabilidade dos seus aplicativos.
* **Diagnóstico**: no Azure, fornecemos a integração no diagnóstico do Azure e no Log Analytics.
* **Dimensionamento automático**: para os clusters no Azure, fornecemos uma funcionalidade interna de dimensionamento automático devido aos conjuntos de escala da Máquina Virtual. Em ambientes locais e outros de nuvem, você precisa criar seu próprio recurso de dimensionamento automático ou dimensionar manualmente usando as APIs que o Service Fabric expõe para dimensionar os clusters.

## Próximas etapas
Crie um cluster em VMs ou em computadores que estejam executando o Windows Server: [Criação de um cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)

Crie um cluster em VMs ou em computadores que estejam executando o Linux: [Service Fabric no Linux](service-fabric-linux-overview.md)

<!---HONumber=AcomDC_0928_2016-->