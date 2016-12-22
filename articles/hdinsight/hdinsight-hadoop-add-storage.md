---
title: Adicionar outras contas de armazenamento do Azure ao HDInsight | Microsoft Docs
description: Saiba como adicionar outras contas de armazenamento do Azure a um cluster existente do HDInsight.
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 697580da9d7c836655ec64745cfb211beabb84f2
ms.openlocfilehash: 941d5a8dba421dd5f59bb61af35ac654e42d5910


---

# <a name="add-additional-azure-storage-accounts-to-hdinsight"></a>Adicionar outras contas de armazenamento do Azure ao HDInsight

Saiba como usar ações de script para adicionar outras contas de armazenamento do Azure a um cluster existente do HDInsight.

> [!IMPORTANT]
> As informações neste documento mostram como adicionar mais armazenamento a um cluster após sua criação. Para saber mais sobre como adicionar outras contas de armazenamento durante a criação do cluster, veja a seção __Usar armazenamento adicional__ do documento [Criar clusters HDInsight com base no Linux](hdinsight-hadoop-provision-linux-clusters.md#use-additional-storage).

## <a name="how-it-works"></a>Como ele funciona

Este script usa os seguintes parâmetros:

* __Nome da conta de armazenamento do Azure__: o nome da conta de armazenamento a ser adicionada ao cluster do HDInsight. Após a execução do script, o HDInsight poderá ler e gravar dados armazenados nessa conta de armazenamento.

* __Chave da conta de armazenamento do Azure__: uma chave que concede acesso à conta de armazenamento.

* __-p__ (opcional): se for especificado, a chave não será criptografada e armazenada no arquivo core-site.xml como texto sem formatação.

Durante o processamento, o script executa as ações a seguir:

* Se a conta de armazenamento já existir na configuração de core-site.xml do cluster, o script será encerrado e nenhuma ação será executada.

* Verifica se a conta de armazenamento existe e se pode ser acessada usando a chave.

* Criptografa a chave usando a credencial do cluster. Isso impede que os usuários do HDInsight possam extrair e usar facilmente a chave da conta de armazenamento do Ambari.

* Adiciona a conta de armazenamento ao arquivo core-site.xml.

* Para e reinicia os serviços Oozie, YARN, MapReduce2 e HDFS, para que eles obtenham as novas informações da conta de armazenamento.

> [!WARNING]
> Se a conta de armazenamento estiver em uma região diferente do cluster do HDInsight, você poderá enfrentar um desempenho ruim. O acesso a dados em uma região diferente envia o tráfego de rede para fora do data center regional do Azure e para a internet pública, o que pode introduzir latência. Além disso, o envio de dados para fora de um data center regional pode custar mais, pois uma cobrança de saída será aplicada quando os dados deixarem um data center.

## <a name="the-script"></a>O script

__Local do script__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Requisitos__:

* O script deve ser aplicado sobre os __Nós de cabeça__.

## <a name="to-use-the-script"></a>Para usar o script

Consulte Aplicar uma ação de script em uma seção de cluster em execução do documento [Personalizar clusters HDInsight baseados em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) para obter informações sobre como usar ações de script por meio do Portal do Azure, do Azure PowerShell e da CLI do Azure.

Ao usar as informações fornecidas no documento de personalização, substitua qualquer exemplo de URI de ação de script pelo URI desse script (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh). Substitua quaisquer parâmetros de exemplo pelo nome da conta de armazenamento do Azure e pela chave da conta de armazenamento a ser adicionada ao cluster.

> [!NOTE]
> Não é necessário marcar esse script como __Persistido__, pois ele atualiza diretamente a configuração do Ambari para o cluster.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Contas de armazenamento não exibidas no Portal do Azure ou nas ferramentas

Ao visualizar o cluster do HDInsight no Portal do Azure, a seleção da entrada __Contas de Armazenamento__ em __Propriedades__ não exibe as contas de armazenamento adicionadas por meio dessa ação de script. O Azure PowerShell e a CLI do Azure também não exibirão a conta de armazenamento adicional.

Isso acontece porque o script apenas modifica a configuração de core-site.xml para o cluster. No momento, essas informações não são usadas ao recuperar as informações de cluster usando as APIs de gerenciamento do Azure.

Para exibir informações da conta de armazenamento adicionadas ao cluster usando esse script, use a API REST do Ambari. O comando a seguir demonstra como usar [cURL (http://curl.haxx.se/)](http://curl.haxx.se/) e [jq (https://stedolan.github.io/jq/)](https://stedolan.github.io/jq/) para recuperar e analisar dados JSON do Ambari:

> [!div class="tabbedCodeSnippets" data-resources="OutlookServices.Calendar"]
> ```PowerShell
> curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["""fs.azure.account.key.STORAGEACCOUNT.blob.core.windows.net"""] | select(. != null)'
> ```
> ```Bash
> curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.STORAGEACCOUNT.blob.core.windows.net"] | select(. != null)'
> ```

Ao usar esse comando, substitua __NOMEDOCLUSTER__ pelo nome do cluster do HDInsight. Substitua __SENHA__ pela senha de logon HTTP do cluster. Substitua __STORAGEACCOUNT__ pelo nome da conta de armazenamento adicionada usando a ação de script. As informações que retornam desse comando são semelhantes ao texto a seguir:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Esse texto é um exemplo de uma chave criptografada, que é usada para acessar a conta de armazenamento.

### <a name="unable-to-access-storage-after-changing-key"></a>Não é possível acessar o armazenamento após a alteração da chave

Se você alterar a chave de uma conta de armazenamento, o HDInsight não poderá mais acessar a conta de armazenamento.

Isso acontece porque a chave armazenada no core-site.xml para o cluster é a chave antiga.

A nova execução da ação de script __não__ atualizará a chave, pois o script verifica se já existe uma entrada para a conta de armazenamento. Se existir, ele não fará alterações.

Para contornar esse problema, remova a entrada existente da conta de armazenamento. Faça isso usando as etapas abaixo:

1. Em um navegador da Web, abra a interface de usuário na Web do Ambari para seu cluster do HDInsight. O URI é https://CLUSTERNAME.azurehdinsight.net. Substitua __CLUSTERNAME__ pelo nome do cluster.

    Quando receber a solicitação, insira o usuário e a senha de logon HTTP para seu cluster.

2. Na lista de serviços à esquerda da página, selecione __HDFS__. Em seguida, selecione a guia __Configurações__ no centro da página.

3. No campo __Filtrar...__, insira um valor de __fs.azure.account__. Isso retornará entradas para quaisquer outras contas de armazenamento que foram adicionadas ao cluster. Há dois tipos de entradas; __keyprovider__ e __key__. Ambas conterão o nome da conta de armazenamento como parte do nome da chave. 

    Veja a seguir exemplos de entradas para uma conta de armazenamento chamada __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Depois de identificar as chaves da conta de armazenamento que você precisa remover, use o ícone vermelho '-' à direita da entrada para excluí-las. Depois, use o botão __Salvar__ para salvar as alterações.

5. Depois que as alterações forem salvas, use a ação de script para adicionar a conta de armazenamento e o novo valor de chaves ao cluster.

### <a name="poor-performance"></a>Desempenho ruim

Se a conta de armazenamento estiver em uma região diferente do cluster do HDInsight, você poderá enfrentar um desempenho ruim. O acesso a dados em uma região diferente envia o tráfego de rede para fora do data center regional do Azure e para a internet pública, o que pode introduzir latência.

### <a name="additional-charges"></a>Custos adicionais

Se a conta de armazenamento estiver em uma região diferente do cluster do HDInsight, você poderá observar encargos adicionais em sua cobrança do Azure. Um encargo de saída é aplicado quando os dados saem de um data center regional, mesmo se o tráfego for destinado a outro data center do Azure em uma região diferente.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a adicionar outras contas de armazenamento a um cluster existente do HDInsight. Para saber mais sobre as ações de script, confira [Personalizar clusters HDInsight com base em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md)


<!--HONumber=Nov16_HO5-->


