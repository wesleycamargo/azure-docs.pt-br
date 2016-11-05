---
title: Usar nós de borda vazios no HDInsight | Microsoft Docs
description: Como adicionar um nó de borda vazio ao cluster HDInsight que pode ser usado como um cliente e como testar/hospedar seus aplicativos de HDInsight.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: ''

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2016
ms.author: jgao

---
# Usar nós de borda vazios no HDInsight
Saiba como adicionar um nó de borda vazio a um cluster HDInsight baseado em Linux. Um nó de borda vazio é uma máquina virtual do Linux com as mesmas ferramentas de cliente instaladas e configuradas como nos nós de cabeçalho, mas sem serviços do hadoop em execução. Você pode usar o nó de borda para acessar o cluster, testar e hospedar seus aplicativos clientes.

Você pode adicionar um nó de borda vazio a um cluster HDInsight existente ou a um novo cluster quando ele é criado. A adição de um nó de borda vazio é feita usando o modelo Azure Resource Manager. O exemplo abaixo demonstra como isso é feito usando um modelo:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "[variables('clusterApiVersion')]",
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "[parameters('edgeNodeSize')]"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
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

## Adicionar um nó de borda a um cluster existente
Nesta seção, você pode usar um modelo do Resource Manager para adicionar um nó de borda a um cluster HDInsight existente. O modelo do Resource Manager pode ser encontrado no [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode). O modelo do Resource Manager chama um script de ação de script localizado em https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. O script não realiza nenhuma ação. Isso serve para demonstrar a chamada da ação de script de um modelo do Resource Manager.

**Para adicionar um nó de borda vazio a um cluster existente**

1. Se você ainda não tem um, crie um cluster HDInsight. Confira o [tutorial do Hadoop: introdução ao uso do Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem a seguir para entrar no Azure e abra o modelo do Azure Resource Manager no portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure as seguintes propriedades:
   
   * CLUSTERNAME: insira o nome de um cluster HDInsight existente.
   * EDGENODESIZE: selecione um dos tamanhos de VM.
   * EDGENODEPREFIX: o valor padrão é **new**. Usando o valor padrão, o nome do nó de borda é **new-edgenode**. Você pode personalizar o prefixo no portal. Você também pode personalizar o nome completo no modelo.
4. Clique em **OK** para salvar as alterações.
5. Em **Grupo de recursos**, selecione um grupo de recursos.
6. Clique em **Analisar termos legais** e em **Comprar**.
7. Selecione **Fixar no painel** e então clique em **Criar**.

## Adicionar um nó de borda ao criar um cluster
Nesta seção, você pode usar um modelo do Resource Manager para criar um cluster HDInsight com um nó de borda. O modelo do Resource Manager pode ser encontrado em um [Contêiner de armazenamento de blobs do Azure](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json) público. O modelo do Resource Manager chama um script de ação de script localizado em https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. O script não realiza nenhuma ação. Isso serve para demonstrar a chamada da ação de script de um modelo do Resource Manager.

**Para adicionar um nó de borda vazio a um cluster existente**

1. Se você ainda não tem um, crie um cluster HDInsight. Confira o [tutorial do Hadoop: introdução ao uso do Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem a seguir para entrar no Azure e abra o modelo do Azure Resource Manager no portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure as seguintes propriedades:
   
   * CLUSTERNAME: insira um nome para o novo cluster a criar.
   * CLUSTERLOGINUSERNAME: insira o nome de usuário HTTP do Hadoop. O nome padrão é **admin**.
   * CLUSTERLOGINPASSWORD: digite a senha do usuário HTTP do Hadoop.
   * SSHUSERNAME: insira o nome de usuário do SSH. O nome padrão é **sshuser**.
   * SSHPASSWORD: digite a senha de usuário do SSH.
   * LOCAL: insira o local do nome do grupo de recursos, o cluster e a conta de armazenamento padrão.
   * CLUSTERTYPE: o valor padrão é **hadoop**.
   * CLUSTERWORKERNODECOUNT: o valor padrão é 2.
   * EDGENODESIZE: selecione um dos tamanhos de VM.
   * EDGENODEPREFIX: o valor padrão é **new**. Usando o valor padrão, o nome do nó de borda é **new-edgenode**. Você pode personalizar o prefixo no portal. Você também pode personalizar o nome completo no modelo.
4. Clique em **OK** para salvar as alterações.
5. Em **Grupo de recursos**, digite um novo nome de grupo de recursos.
6. Clique em **Analisar termos legais** e em **Comprar**.
7. Selecione **Fixar no painel** e então clique em **Criar**.

## Acessar um nó de borda
O ponto de extremidade ssh do nó de borda é <EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22. Por exemplo, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

O nó de borda aparece como um aplicativo no portal do Azure. O portal fornece as informações para acessar o nó de borda usando o SSH.

**Para verificar o ponto de extremidade SSH do nó de borda**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Clique em **Aplicativos** na folha do cluster. Você verá o nó de borda. O nome padrão é **new-edgenode**.
4. Clique no nó de borda. Você deverá ver o ponto de extremidade SSH.

**Para usar o Hive no nó de borda**

1. Use SSH para conectar-se ao nó de borda. Confira [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, do Unix ou do OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
2. Depois que você tiver se conectado ao nó de borda usando o SSH, use o seguinte comando para abrir o console do Hive:
   
        hive
3. Execute o seguinte comando para mostrar as tabelas de Hive no cluster:
   
        show tables;

## Excluir um nó de borda
Você pode excluir um nó de borda no portal do Azure.

**Para acessar um nó de borda**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Clique em **Aplicativos** na folha do cluster. Você verá uma lista de nós de borda.
4. Clique no nó de borda que deseja excluir e clique em **Excluir**.
5. Clique em **Sim** para confirmar.

## Próximas etapas
Neste artigo, você aprendeu como adicionar um nó de borda e como acessar o nó de borda. Para saber mais, consulte os seguintes artigos:

* [Instalar aplicativos do HDInsight](hdinsight-apps-install-applications.md): saiba como instalar um aplicativo do HDInsight em seus clusters.
* [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): saiba como implantar um aplicativo do HDInsight não publicado no HDInsight.
* [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): saiba como definir aplicativos do HDInsight.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Personalizar clusters Hadoop baseados em Linux no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.

<!---HONumber=AcomDC_0914_2016-->