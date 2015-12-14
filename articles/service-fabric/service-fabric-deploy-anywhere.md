<properties
   pageTitle="Deploy Anywhere com o Service Fabric (Windows Server e Linux) | Microsoft Azure"
   description="Os clusters do Service Fabric serão executados no Windows Server e no Linux, permitindo que os aplicativos do Service Fabric sejam implantados e hospedados em qualquer lugar em que você possa executar o Windows Server e o Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunalds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/19/2015"
   ms.author="kunalds"/>

# “Deploy Anywhere” no Windows Server ou no Linux com o Service Fabric
O Service Fabric permite a criação de clusters do Service Fabric em quaisquer VMs ou máquinas que estejam executando o Windows Server ou o Linux. Isso permite a implantação e a execução de aplicativos do Service Fabric em qualquer ambiente, seja local ou em qualquer provedor de nuvem no qual você tenha um conjunto de computadores com Windows Server ou Linux interconectados. O Service Fabric fornecerá um pacote de instalação para a criação de clusters do Service Fabric. Uma vantagem importante do recurso "Deploy Anywhere" é que não há qualquer bloqueio de fornecedor quando você compila um aplicativo usando o Service Fabric, desde que você escolha onde esses aplicativos serão executados. Isso também aumenta a possibilidade de alcançar um número maior clientes, já que clientes diferentes podem ter necessidades diferentes com relação aos ambientes nos quais desejam executar seus aplicativos, por exemplo, clientes do setor financeiro e de saúde podem ter necessidades diferentes quando comparados a um negócio automotivo ou de viagens. A visualização técnica deste recurso deve ser lançado no primeiro trimestre de 2016.

## Sistemas operacionais com suporte
Você poderá criar clusters em VMs ou em computadores que executam estes sistemas operacionais: * Windows Server 2012 R2 * Windows Server 2016 * Linux

## Linguagens de programação com suporte
Você poderá escrever aplicativos do Service Fabric com qualquer uma dessas linguagens de programação: * C# * Java

## Criação e configuração de cluster
O Service Fabric fornecerá um pacote de instalação que será baixado do Centro de Download da Microsoft. Depois de baixar o pacote, você precisará fazer alterações em um arquivo de configuração para especificar as configurações de seu cluster. Após as edições das configurações do cluster, você executará um script de instalação que criará o cluster abrangendo as máquinas que você especificou nas configurações do cluster. Os detalhes exatos do processo de instalação serão compartilhados quando lançarmos a visualização dessa funcionalidade no primeiro trimestre de 2016.

## Implantações na nuvem versus Implantações locais
O processo de criação de um cluster local do Service Fabric será semelhante ao processo de criação de um cluster em qualquer nuvem de sua escolha na qual você tenha um conjunto de VMs. As etapas iniciais para o provisionamento das VMs serão regidas pelo ambiente em nuvem ou local que você está usando, mas assim que você tiver um conjunto de VMs com conectividade de rede habilitada entre eles, as próximas etapas para configurar o pacote do Service Fabric, editar as configurações de cluster e executar scripts de criação e gerenciamento de cluster serão semelhantes. Isso garante que o seu conhecimento e experiência com a operação e o gerenciamento de clusters do Service Fabric poderão ser transferidos quando você optar por novos ambientes de hospedagem.

## Benefícios do recurso Deploy Anywhere
* Nenhum bloqueio de fornecedor.
* Os aplicativos do Service Fabric, uma vez escritos, podem ser executados em vários ambientes de hospedagem com pouca ou nenhuma alteração.
* O conhecimento sobre a criação de aplicativos do Service Fabric é transferido de uma plataforma de hospedagem para outras.
* A experiência operacional da execução de clusters do Service Fabric é transferida de um ambiente de implantação para outros.
* Amplo alcance de clientes, sem limitação de restrições de ambiente de hospedagem.
* Camada extra de proteção e confiabilidade contra falhas generalizadas, movendo os serviços para outro ambiente de implantação, se um datacenter ou nuvem fornecida enfrentar um blecaute.

## Diferenças de clusters do Service Fabric hospedados no Deploy Anywhere e no Azure
A execução de clusters do Service Fabric usando o Serviço Hospedado do Azure fornece alguns benefícios em relação ao uso da opção Deploy Anywhere, Portanto, se você não tenha necessidades específicas com relação ao local onde você executa os clusters, sugerimos que você os execute como clusters hospedados do Azure. No Azure, fornecemos integração com outros recursos e serviços do Azure que facilitam as operações e o gerenciamento do cluster:

* Portal do Azure: o Portal do Azure facilita a criação e o gerenciamento de clusters.
* Gerenciador de recursos do Azure: o uso de ARM permite o fácil gerenciamento de todos os recursos usados pelo cluster como uma unidade e simplifica o acompanhamento de custos e faturamento. 
* Diagnóstico: No Azure, fornecemos a integração com o WAD (Diagnóstico do Microsoft Azure) e informações operacionais para diagnósticos.
* Dimensionamento automático: para os clusters hospedados do Azure, forneceremos uma funcionalidade interna de dimensionamento automático. Em outros ambientes que usam do recurso Deploy Anywhere, você precisará criar seu próprio recurso de dimensionamento automático ou dimensionar manualmente usando as APIs expostas pelo Service Fabric para dimensionamento de clusters.

<!---HONumber=AcomDC_1203_2015-->