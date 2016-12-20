---
title: "Usar nós de borda vazios no HDInsight | Microsoft Docs"
description: "Como adicionar um nó de borda vazio ao cluster HDInsight que pode ser usado como um cliente e como testar/hospedar seus aplicativos de HDInsight."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 3b7bbd63a0bd87ee45a387bc0612541f0d82208b
ms.openlocfilehash: 73f7235686435300d18fe518c08adbd6c3904f39


---
# <a name="use-empty-edge-nodes-in-hdinsight"></a>Usar nós de borda vazios no HDInsight
Saiba como adicionar um nó de borda vazio a um cluster HDInsight baseado em Linux. Um nó de borda vazio é uma máquina virtual do Linux com as mesmas ferramentas de cliente instaladas e configuradas como nos nós de cabeçalho, mas sem serviços do hadoop em execução. Você pode usar o nó de borda para acessar o cluster, testar e hospedar seus aplicativos clientes. 

Você pode adicionar um nó de borda vazio a um cluster HDInsight existente ou a um novo cluster quando ele é criado. A adição de um nó de borda vazio é feita usando o modelo Azure Resource Manager.  O exemplo abaixo demonstra como isso é feito usando um modelo:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Conforme mostrado no exemplo, você pode, opcionalmente, chamar uma [ação do script](hdinsight-hadoop-customize-cluster-linux.md) para executar configurações adicionais, como a instalação do [Apache Hue](hdinsight-hadoop-hue-linux.md) no nó de borda.

Depois de criar um nó de borda, você pode conectar-se a ele usando SSH e executar as ferramentas de cliente para acessar o cluster Hadoop no HDInsight.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Adicionar um nó de borda a um cluster existente
Nesta seção, você pode usar um modelo do Resource Manager para adicionar um nó de borda a um cluster HDInsight existente.  O modelo do Resource Manager pode ser encontrado em [Modelos de Início Rápido do Azure](https://azure.microsoft.com/en-us/resources/templates/101-hdinsight-linux-with-edge-node/). O modelo do Resource Manager chama um script de ação localizado em https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. O script não realiza nenhuma ação.  Isso serve para demonstrar a chamada da ação de script de um modelo do Resource Manager.

**Para adicionar um nó de borda vazio a um cluster existente**

1. Se você ainda não tem um, crie um cluster HDInsight.  Confira o [tutorial do Hadoop: introdução ao uso do Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem a seguir para entrar no Azure e abra o modelo do Azure Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure as seguintes propriedades:
   
   * **Assinatura**: selecione uma assinatura do Azure usada para criar esse cluster.
   * **Grupo de Recursos**: selecione o grupo de recursos usado para o cluster HDInsight existente.
   * **Localização**: Selecione a localização do cluster HDInsight existente.
   * **Nome do Cluster**: insira o nome de um cluster HDInsight existente.
   * **Tamanho do Nó de Borda**: Selecione um dos tamanhos de VM.
   * **Prefixo do Nó de Borda**: O valor padrão é **novo**.  Usando o valor padrão, o nome do nó de borda é **new-edgenode**.  Você pode personalizar o prefixo no portal. Você também pode personalizar o nome completo no modelo.
4. Selecione **Concordo com os termos e condições declarados acima** e clique em **Comprar** para criar um nó de borda.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Adicionar um nó de borda ao criar um cluster
Nesta seção, você pode usar um modelo do Resource Manager para criar um cluster HDInsight com um nó de borda.  O modelo do Resource Manager pode ser encontrado em [Galeria de Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). O modelo do Resource Manager chama um script de ação localizado em https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. O script não realiza nenhuma ação.  Isso serve para demonstrar a chamada da ação de script de um modelo do Resource Manager.

**Para adicionar um nó de borda vazio a um cluster existente**

1. Se você ainda não tem um, crie um cluster HDInsight.  Confira o [tutorial do Hadoop: introdução ao uso do Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem a seguir para entrar no Azure e abra o modelo do Azure Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure as seguintes propriedades:
   
   * **Assinatura**: selecione uma assinatura do Azure usada para criar esse cluster.
   * **Grupo de Recursos**: Criar um novo grupo de recursos usado para o cluster.
   * **Local**: selecione um local para o grupo de recursos.
   * **Nome do Cluster**: insira um nome para o novo cluster a ser criado.
   * **Nome de Usuário de Logon do Cluster **: insira o nome de usuário HTTP do Hadoop.  O nome padrão é **admin**.
   * **Senha de Logon do Cluster**: digite a senha do usuário HTTP do Hadoop.
   * **Nome de Usuário Ssh**: digite o nome de usuário SSH. O nome padrão é **sshuser**.
   * **Senha do Ssh**: digite a senha de usuário do SSH.
   * **Instalar ação do script**: manter o valor padrão para percorrer este tutorial.
     
     Algumas propriedades foram embutidas no código do modelo: tipo de Cluster, contagem de nós de trabalho do Cluster, tamanho do nó de borda e nome do nó de borda.
4. Selecione **Concordo com os termos e condições declarados acima** e clique em **Comprar** para criar um cluster com nó de borda.

## <a name="access-an-edge-node"></a>Acessar um nó de borda
O ponto de extremidade ssh do nó de borda é <EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22.  Por exemplo, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

O nó de borda aparece como um aplicativo no portal do Azure.  O portal fornece as informações para acessar o nó de borda usando o SSH.

**Para verificar o ponto de extremidade SSH do nó de borda**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Clique em **Aplicativos** na folha do cluster. Você verá o nó de borda.  O nome padrão é **new-edgenode**.
4. Clique no nó de borda. Você deverá ver o ponto de extremidade SSH.

**Para usar o Hive no nó de borda**

1. Use SSH para conectar-se ao nó de borda.  Confira [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, do Unix ou do OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
2. Depois que você tiver se conectado ao nó de borda usando o SSH, use o seguinte comando para abrir o console do Hive:
   
        hive
3. Execute o seguinte comando para mostrar as tabelas de Hive no cluster:
   
        show tables;

## <a name="delete-an-edge-node"></a>Excluir um nó de borda
Você pode excluir um nó de borda no portal do Azure.

**Para acessar um nó de borda**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Clique em **Aplicativos** na folha do cluster. Você verá uma lista de nós de borda.  
4. Clique no nó de borda que deseja excluir e clique em **Excluir**.
5. Clique em **Sim** para confirmar.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como adicionar um nó de borda e como acessar o nó de borda. Para saber mais, consulte os seguintes artigos:

* [Instalar aplicativos do HDInsight](hdinsight-apps-install-applications.md): saiba como instalar um aplicativo do HDInsight em seus clusters.
* [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): saiba como implantar um aplicativo do HDInsight não publicado no HDInsight.
* [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): saiba como definir aplicativos do HDInsight.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Personalizar clusters Hadoop baseados em Linux no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.




<!--HONumber=Nov16_HO3-->


