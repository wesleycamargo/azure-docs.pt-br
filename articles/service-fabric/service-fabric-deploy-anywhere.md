<properties
   pageTitle="Deploy Anywhere com o Service Fabric do Azure no Windows Server e Linux | Microsoft Azure"
   description="Os clusters do Service Fabric serão executados no Windows Server e Linux, o que significa que você poderá implantar e hospedar aplicativos do Service Fabric em qualquer lugar que possa executar o Windows Server ou Linux."
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
   ms.date="02/10/2016"
   ms.author="chackdan"/>

# “Deploy Anywhere” no Windows Server ou no Linux com o Service Fabric
Com a adição do "Deploy Anywhere", o Service Fabric do Azure permitirá a criação de clusters do Service Fabric em qualquer VM ou computador executando Windows Server ou Linux. Isso significa que você poderá implantar e executar aplicativos do Service Fabric em qualquer ambiente em que tenha um conjunto de computadores Windows Server ou Linux interconectados, esteja ele local ou em qualquer provedor de nuvem.

 O Service Fabric fornece um pacote de instalação para a criação de clusters do Service Fabric. Com o recurso Deploy Anywhere, você tem a vantagem de não depender de um fornecedor quando cria um aplicativo usando o Service Fabric, uma vez que você escolhe onde esses aplicativos são executados. Esse recurso também aumenta o seu potencial para conquistar uma base mais ampla de clientes, pois os clientes podem ter requisitos variados para os ambientes em que eles querem executar os aplicativos. Por exemplo, os clientes nos setores de saúde e financeiro podem ter diferentes necessidades dos clientes nos setores automobilístico ou turístico.

A visualização técnica deste recurso deve ser lançado no primeiro trimestre de 2016.

## Sistemas operacionais com suporte
Você poderá criar clusters em VMs ou computadores que executam estes sistemas operacionais: * Windows Server 2012 * Windows Server 2012 R2 * Windows Server 2016 * Linux

## Linguagens de programação com suporte
Você poderá escrever aplicativos do Service Fabric com estas linguagens de programação: * C# * Java

## Criação e configuração de cluster
O Service Fabric fornece um pacote de instalação que você pode baixar. Depois de baixar este pacote, você precisará fazer alterações em um arquivo de configuração para especificar as configurações de seu cluster. Depois de editar as configurações do cluster, você executará um script de instalação que criará o cluster abrangendo os computadores especificados nas configurações do cluster.

Os detalhes exatos do processo de instalação serão compartilhados quando lançarmos a visualização dessa funcionalidade no primeiro trimestre de 2016.

## Implantações na nuvem vs. implantações locais
O processo de criação de um cluster local do Service Fabric será semelhante ao processo de criação de um cluster em qualquer nuvem de sua escolha com um conjunto de VMs. As etapas iniciais para provisionar as VMs serão administradas pelo ambientes local ou de nuvem que você estiver usando. Depois que você tiver um conjunto de VMs com conectividade de rede habilitada para elas, as etapas para configurar o pacote do Service Fabric, editar as configurações do cluster e executar os scripts de gerenciamento e criação do cluster serão semelhantes. Isso garante que seu conhecimento e experiência com a operação e o gerenciamento de clusters do Service Fabric possam ser transferidos quando você optar por novos ambientes de hospedagem.

## Benefícios do recurso Deploy Anywhere
* Como não há dependência de fornecedor, você pode escolher onde seus aplicativos serão executados.
* Os aplicativos do Service Fabric, uma vez escritos, podem ser executados em vários ambientes de hospedagem com pouca ou nenhuma alteração.
* O conhecimento sobre a criação de aplicativos do Service Fabric é transferido de uma plataforma de hospedagem para outras.
* A experiência operacional da execução de clusters do Service Fabric é transferida de um ambiente de implantação para outros.
* Amplo alcance de clientes, sem limitação de restrições de ambiente de hospedagem.
* Uma camada extra de confiabilidade e proteção contra interrupções generalizadas, permitindo que você mova os serviços para outro ambiente de implantação se um datacenter ou provedor de nuvem sofrer um blecaute.

## Vantagens dos clusters gerenciados pelo Service Fabric no Azure sobre os clusters do Service Fabric criados e gerenciados usando o Deploy Anywhere
A execução de clusters do Service Fabric no Azure apresenta algumas vantagens em relação ao uso da opção Deploy Anywhere, de modo que se você não tiver necessidades específicas de local para execução dos clusters, sugerimos que os execute no Azure. No Azure, fornecemos integração a outros recursos e serviços do Azure, o que facilita as operações e o gerenciamento do cluster.

* **Portal do Azure:** o Portal do Azure facilita a criação e o gerenciamento de clusters.
* **Gerenciador de Recursos do Azure**: o uso do Gerenciador de Recursos do Azure permite o fácil gerenciamento de todos os recursos usados pelo cluster como uma unidade e simplifica o acompanhamento de custos e faturamento.
* O cluster do **Azure Resource** Service Fabric é um recurso do ARM, portanto você pode modelá-lo como outros recursos do ARM no Azure.
* **Diagnóstico**: no Azure, fornecemos integração ao Diagnóstico do Azure e aos Insights Operacionais.
* **Dimensionamento automático**: para os clusters no Azure, forneceremos uma funcionalidade interna de dimensionamento automático. Em outros ambientes que usam o recurso Deploy Anywhere, você precisará criar seu próprio recurso de dimensionamento automático ou dimensionar manualmente usando as APIs expostas pelo Service Fabric para dimensionamento de clusters.

<!---HONumber=AcomDC_0224_2016-->