<properties
   pageTitle="Configurar o cluster autônomo | Microsoft Azure"
   description="Este artigo descreve como configurar seu cluster autônomo ou particular do Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="dkshir"/>


# Definições de configuração para o cluster autônomo no Windows

Este artigo descreve como configurar um cluster autônomo do Service Fabric usando o arquivo _**ClusterConfig.JSON**_. Esse arquivo é baixado em seu computador de trabalho quando você [baixa o pacote do Service Fabric autônomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage). O arquivo ClusterConfig.JSON permite que você especifique informações como os nós do Service Fabric e seus endereços IP, tipos diferentes de nós no cluster, as configurações de segurança, bem como a topologia de rede em termos de domínios de falha/atualização, para o cluster do Service Fabric.

Vamos examinar abaixo as várias seções desse arquivo.

## Configurações gerais do cluster
Isso abrange as configurações específicas mais amplas do cluster, conforme mostra o trecho de JSON abaixo.

    "name": "SampleCluster",
    "clusterManifestVersion": "1.0.0",
    "apiVersion": "2015-01-01-alpha",

Você pode atribuir qualquer nome amigável ao cluster do Service Fabric, atribuindo a ele a variável **name**. Você pode alterar o **clusterManifestVersion** de acordo com a sua configuração; será necessário atualizá-lo antes de atualizar sua configuração do Service Fabric. Você pode deixar a **apiVersion** com o valor padrão.


<a id="clusternodes"></a>
## Nós no cluster
Você pode configurar os nós no cluster de seu Service Fabric usando a seção **nós**, como mostra o trecho de código a seguir.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Um cluster do Service Fabric exige no mínimo 3 nós. Você pode adicionar mais nós a esta seção de acordo com a sua configuração. A tabela a seguir explica as definições de configuração para cada nó.

|**Configuração de nó**|**Descrição**|
|-----------------------|--------------------------|
|nodeName|Você pode atribuir qualquer nome amigável ao nó.|
|iPAddress|Descubra o endereço IP do seu nó abrindo uma janela de comando e digitando `ipconfig`. Anote o endereço IPV4 e atribua a ele a variável **iPAddress**.|
|nodeTypeRef|Cada nó pode ser receber um tipo de nó diferente. Os [tipos de nó](#nodetypes) são definidos na seção abaixo.|
|faultDomain|Os domínios de falha permitem que os administradores de cluster definam os nós físicos que podem falhar ao mesmo tempo devido às dependências físicas compartilhadas.|
|upgradeDomain|Os domínios de atualização descrevem conjuntos de nós que são desligados para atualizações do Service Fabric quase ao mesmo tempo. Você pode escolher quais nós atribuir a quais Domínios de atualização, pois eles não são limitados por quaisquer requisitos físicos.| 


## Configurações de diagnóstico
Você pode configurar parâmetros para habilitar o diagnóstico e solucionar problemas de falhas de nó e do cluster, usando a seção **diagnosticsFileShare** conforme mostra o trecho a seguir.

    "diagnosticsFileShare": {
        "etlReadIntervalInMinutes": "5",
        "uploadIntervalInMinutes": "10",
        "dataDeletionAgeInDays": "7",
        "etwStoreConnectionString": "file:c:\\ProgramData\\SF\\FileshareETW",
        "crashDumpConnectionString": "file:c:\\ProgramData\\SF\\FileshareCrashDump",
        "perfCtrConnectionString": "file:c:\\ProgramData\\SF\\FilesharePerfCtr"
    },

Essas variáveis ajudam na coleta de logs de rastreamento ETW, despejos de memória e contadores de desempenho. Leia [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) e [Rastreamento ETW](https://msdn.microsoft.com/library/ms751538.aspx) para saber mais sobre os logs de rastreamento de ETW. [Despejos de memória](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) para o nó do Service Fabric, bem como o cluster, podem ser direcionados para a pasta **crashDumpConnectionString**. Os [contadores de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) do cluster podem ser direcionados para a pasta **perfCtrConnectionString** em seu computador.


## **Propriedades** do cluster

A seção **propriedades** no ClusterConfig.JSON é usada para configurar o cluster da seguinte maneira.

### **segurança** 
A seção **segurança** é necessária para um cluster de autônomo seguro do Service Fabric. O trecho a seguir mostra uma parte desta seção.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
		. . .
	}

**Metadados** contêm uma descrição do seu cluster seguro e podem ser definidos de acordo com a sua configuração. **ClusterCredentialType** e **ServerCredentialType** determinam o tipo de segurança que o cluster e os nós implementarão. Eles podem ser definidos como *X509* para segurança baseada em certificado, ou *Windows* para uma segurança baseada no Azure Active Directory. O restante da seção **segurança** terá base no tipo de segurança. leia [Segurança baseada em certificados em um cluster autônomo](service-fabric-windows-cluster-x509-security.md) ou [Segurança do Windows em um cluster autônomo](service-fabric-windows-cluster-windows-security.md) para obter informações sobre como preencher o resto da seção **segurança**.

### **reliabilityLevel**
**reliabilityLevel** define o número de cópias de serviços do sistema que podem ser executadas em nós primários do cluster. Isso aumenta a confiabilidade desses serviços e, portanto, do cluster. Você pode definir essa variável como *Bronze*, *Prata*, *Ouro* ou *Platina* para 3, 5, 7 ou 9 cópias desses serviços, respectivamente. Veja um exemplo abaixo.

	"reliabilityLevel": "Bronze",
	
Observe que como um nó principal executa uma única cópia dos serviços do sistema, você precisaria de no mínimo três nós primários para os níveis de confiabilidade *Bronze*, 5 para *Prata*, 7 para *Ouro* e 9 para *Platina*.


<a id="nodetypes"></a>
### **nodeTypes**
A seção **nodeTypes** descreve o tipo de nó que o cluster possui. Pelo menos um tipo de nó deve ser especificado para um cluster, conforme mostra o trecho a seguir.

	"nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpoint": "19001",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
			"startPort": "20001",
            "endPort": "20031"
        },
        "ephemeralPorts": {
            "startPort": "20032",
            "endPort": "20062"
        },
        "isPrimary": true
    }]

O **nome** é o nome amigável para este tipo de nó específico. Para criar um nó desse tipo de nó, será necessário atribuir o nome amigável para este tipo de nó à variável **nodeTypeRef** desse nó, conforme mencionado na seção [Nós no cluster](#clusternodes) acima. Para cada tipo de nó, você pode definir vários pontos de extremidade para se conectar a esse cluster. Você pode escolher qualquer número de porta para esses pontos de extremidade de conexão, desde que eles não entrem em conflito com qualquer outro ponto de extremidade neste cluster. Em um cluster com vários tipos de nó, haverá um tipo de nó primário, com **isPrimary** definido como *true*. O restante dos nós terá **isPrimary** definido como *false*. Leia [Considerações de planejamento de capacidade para cluster do Service Fabric](service-fabric-cluster-capacity.md) para saber mais sobre os valores de **nodeTypes** e **reliabilityLevel**, de acordo com a capacidade de seu cluster, bem como para saber a diferença entre os tipos de nó primários e não primários.


### **fabricSettings**
Esta seção permite que você defina os diretórios raiz para os dados e logs do Service Fabric. Você pode personalizá-los somente durante a criação inicial do cluster. Veja abaixo um exemplo de trecho de código desta seção.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Observe que se você personalizar somente a raiz dos dados, a raiz do log será colocada um nível abaixo da raiz dos dados.


## Próximas etapas

Uma vez que o arquivo ClusterConfig.JSON estiver totalmente configurado de acordo com a configuração do cluster autônomo, você poderá implantar o cluster seguindo o artigo [Criar um cluster do Azure Service Fabric no local ou na nuvem](service-fabric-cluster-creation-for-windows-server.md) e ir para [Visualizando o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!---HONumber=AcomDC_0629_2016-->