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
ms.date: 12/12/2016
ms.author: ryanwi;chackdan
translationtype: Human Translation
ms.sourcegitcommit: ea42f4b8cb026cc52749b3dc3b3e0dca7c82d14f
ms.openlocfilehash: ec0849b9e49b0ce5341908434248e51f1195ba37


---
# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Criar um cluster do Service Fabric autônomo com três nós e com máquinas virtuais do Azure executando o Windows Server
Este artigo descreve como criar um cluster nas máquinas virtuais (VMs) do Azure baseadas no Windows usando o instalador autônomo do Service Fabric para o Windows Server. Trata-se de um caso especial de [Criar e gerenciar um cluster em execução no Windows Server](service-fabric-cluster-creation-for-windows-server.md) em que as VMs são [VMs do Azure executando o Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), no entanto, você não está criando [um cluster do Service Fabric baseado em nuvem do Azure](service-fabric-cluster-creation-via-portal.md). A diferença é que o cluster do Service Fabric autônomo criado pelas seguintes etapas é totalmente gerenciado por você, enquanto que os clusters do Service Fabric baseados em nuvem do Azure são gerenciados e atualizados pelo provedor de recursos do Service Fabric.

## <a name="steps-to-create-the-standalone-cluster"></a>Etapas para criar o cluster autônomo
1. Entre no portal do Azure e crie uma nova VM do Windows Server 2012 R2 Datacenter em um grupo de recursos. Leia o artigo [Criar uma VM do Windows no Portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter mais detalhes.
2. Adicione mais algumas VMs do Windows Server 2012 R2 Datacenter ao mesmo grupo de recursos. Verifique se cada uma das VMs tem o mesmo nome de usuário e senha do administrador quando criadas. Uma vez criadas, você deve ver as três VMs na mesma rede virtual.
3. Conecte cada uma das VMs e desative o Firewall do Windows usando o [painel Gerenciador de Servidores, Servidor Local](https://technet.microsoft.com/library/jj134147.aspx). Isso faz com que o tráfego de rede possa comunicar-se entre os computadores. Ainda conectado a cada máquina, obtenha o endereço IP abrindo um prompt de comando e digitando `ipconfig`. Como alternativa, você pode ver o endereço IP de cada computador no portal selecionando o recurso de rede virtual do grupo de recursos e verificando os adaptadores de rede criados para cada um desses computadores.
4. Conecte uma das VMs e teste se você pode executar o ping nas duas outras VMs com êxito.
5. Conecte uma das VMs, [baixe o pacote do Service Fabric autônomo para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) em uma nova pasta no computador e extrair o pacote.
6. Abra o arquivo *ClusterConfig.Unsecure.MultiMachine.json* no Bloco de Notas e edite cada nó com os três endereços IP das máquinas. Mude o nome do cluster na parte superior e salve o arquivo.  Um exemplo parcial do manifesto do cluster é mostrado abaixo.
   
    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```
7. Abra uma [janela do ISE do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Navegue até a pasta onde você extraiu o pacote do instalador autônomo baixado e salvou o arquivo de manifesto do cluster. Execute o comando do PowerShell a seguir.
   
    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
    ```
8. Você deve ver o script do PowerShell ser executado, conectar cada computador e criar um cluster. Após um minuto, você poderá verificar se o cluster está operando conectando o Service Fabric Explorer em um dos endereços IP da máquina, por exemplo, usando `http://10.7.0.5:19080/Explorer/index.html`. Por ser um cluster autônomo que usa VMs do Azure, para torná-lo seguro, será preciso [implantar certificados para as VMs do Azure](service-fabric-windows-cluster-x509-security.md) ou configurar um dos computadores como um [controlador do AD (Active Directory) do Windows Server para a autenticação do Windows](service-fabric-windows-cluster-windows-security.md), exatamente como você faria localmente.

## <a name="next-steps"></a>Próximas etapas
* [Criar clusters autônomos do Service Fabric no Windows Server ou Linux](service-fabric-deploy-anywhere.md)
* [Adicionar ou remover nós de um cluster do Service Fabric autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Definições de configuração para o cluster autônomo no Windows](service-fabric-cluster-manifest.md)
* [Proteger um cluster autônomo no Windows usando a segurança](service-fabric-windows-cluster-windows-security.md)
* [Proteger um cluster autônomo no Windows usando os certificados X509](service-fabric-windows-cluster-x509-security.md)




<!--HONumber=Dec16_HO2-->


