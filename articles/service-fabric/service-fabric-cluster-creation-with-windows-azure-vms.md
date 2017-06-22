---
title: "Criar um cluster autônomo com VMs do Azure executando o Windows | Microsoft Docs"
description: "Saiba como criar e gerenciar um cluster do Azure Service Fabric nas máquinas virtuais do Azure executando o Windows Server."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 7eeb40d2-fb22-4a77-80ca-f1b46b22edbc
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/21/2017
ms.author: ryanwi;chackdan
redirect_url: /azure/service-fabric/service-fabric-cluster-creation-via-arm
ms.translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: ba1f6e5662700c309fcf198a4e114fd9b2b47c5f
ms.contentlocale: pt-br
ms.lasthandoff: 03/29/2017


---
# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Criar um cluster do Service Fabric autônomo com três nós e com máquinas virtuais do Azure executando o Windows Server
Este artigo descreve como criar um cluster nas máquinas virtuais (VMs) do Azure baseadas no Windows usando o instalador autônomo do Service Fabric para o Windows Server. O cenário é um caso especial de [Criar e gerenciar um cluster em execução no Windows Server](service-fabric-cluster-creation-for-windows-server.md) em que as VMs são [VMs do Azure executando o Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), no entanto, você não está criando [um cluster do Service Fabric baseado em nuvem do Azure](service-fabric-cluster-creation-via-portal.md). A diferença em seguir este padrão é que o cluster do Service Fabric autônomo criado pelas seguintes etapas é totalmente gerenciado por você, enquanto que os clusters do Service Fabric baseados em nuvem do Azure são gerenciados e atualizados pelo provedor de recursos do Service Fabric.

## <a name="steps-to-create-the-standalone-cluster"></a>Etapas para criar o cluster autônomo
1. Entre no Portal do Azure e crie uma nova VM do Windows Server 2012 R2 Datacenter ou do Windows Server 2016 Datacenter em um grupo de recursos. Leia o artigo [Criar uma VM do Windows no Portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter mais detalhes.
2. Adicione mais algumas VMs ao mesmo grupo de recursos. Verifique se cada uma das VMs tem o mesmo nome de usuário e senha do administrador quando criadas. Uma vez criadas, você deve ver as três VMs na mesma rede virtual.
3. Conecte cada uma das VMs e desative o Firewall do Windows usando o [painel Gerenciador de Servidores, Servidor Local](https://technet.microsoft.com/library/jj134147.aspx). Isso faz com que o tráfego de rede possa comunicar-se entre os computadores. Ainda conectado a cada máquina, obtenha o endereço IP abrindo um prompt de comando e digitando `ipconfig`. Como alternativa, você pode ver o endereço IP de cada computador no portal selecionando o recurso de rede virtual do grupo de recursos e verificando os adaptadores de rede criados para cada um desses computadores.
4. Conecte uma das VMs e teste se você pode executar o ping nas duas outras VMs com êxito.
5. Conecte uma das VMs, [baixe o pacote do Service Fabric autônomo para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) em uma nova pasta no computador e extrair o pacote.
6. Abra o arquivo *ClusterConfig.Unsecure.MultiMachine.json* no Bloco de Notas e edite cada nó com os três endereços IP das máquinas. Mude o nome do cluster na parte superior e salve o arquivo.  Um exemplo parcial do manifesto do cluster é mostrado abaixo.
   
    ```
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "01-2017",
        "nodes": [
        {
            "nodeName": "standalonenode0",
            "iPAddress": "10.1.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "standalonenode1",
            "iPAddress": "10.1.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "standalonenode2",
            "iPAddress": "10.1.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
        ],
    ```
7. Se você pretende que isso seja um cluster seguro, decida a medida de segurança que você gostaria de usar e execute as etapas no link associado: [Certificado X509](service-fabric-windows-cluster-x509-security.md) ou [Segurança do Windows](service-fabric-windows-cluster-windows-security.md). Se estiver configurando o cluster usando a Segurança do Windows, será necessário configurar um controlador de domínio para gerenciar o Active Directory. Observe que não há suporte para o uso de um computador controlador de domínio como um nó do Service Fabric.
8. Abra uma [janela do ISE do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Navegue até a pasta onde você extraiu o pacote do instalador autônomo baixado e salvou o arquivo de configuração do cluster. Execute o comando PowerShell a seguir para implantar o cluster:
   
    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
    ```

O script configurará remotamente o cluster do Service Fabric e deverá informar o andamento à medida que implantação ocorre.

9. Após um minuto, você poderá verificar se o cluster está operando conectando-se ao Service Fabric Explorer usando um dos endereços IP da máquina, por exemplo, usando `http://10.1.0.5:19080/Explorer/index.html`. 

## <a name="next-steps"></a>Próximas etapas
* [Criar clusters autônomos do Service Fabric no Windows Server ou Linux](service-fabric-deploy-anywhere.md)
* [Adicionar ou remover nós de um cluster do Service Fabric autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Definições de configuração para o cluster autônomo no Windows](service-fabric-cluster-manifest.md)
* [Proteger um cluster autônomo no Windows usando a segurança](service-fabric-windows-cluster-windows-security.md)
* [Proteger um cluster autônomo no Windows usando os certificados X509](service-fabric-windows-cluster-x509-security.md)


