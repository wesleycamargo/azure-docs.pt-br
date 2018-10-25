---
title: Usar o Data Lake Store com Hadoop no HDInsight do Azure
description: Aprenda a consultar dados do Azure Data Lake Store e a armazenar os resultados da sua análise.
services: hdinsight,storage
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: d205a46c672523e029816b573742d991de79b2ae
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956721"
---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>Usar o Data Lake Store com clusters Azure HDInsight

Para analisar dados no cluster HDInsight, você pode armazenar os dados no [Armazenamento do Azure](../storage/common/storage-introduction.md), no [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) ou em ambos. As duas opções de armazenamento permitem que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário.

Neste artigo, você aprenderá como o Data Lake Store funciona com clusters do HDInsight. Para saber como o Armazenamento do Azure funciona com clusters HDInsight, consulte [Usar o Armazenamento do Azure com clusters do Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Para saber mais sobre a criação de um cluster HDInsight, veja [Criar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]
> O Data Lake Store é sempre acessado por meio de um canal seguro, portanto não há nenhum nome do esquema de sistema de arquivos `adls`. Use sempre `adl`.
> 


## <a name="availability-for-hdinsight-clusters"></a>Disponibilidade para clusters do HDInsight

O Hadoop dá suporte a uma noção do sistema de arquivos padrão. O sistema de arquivos padrão implica esquema e autoridade padrões. Ele também pode ser usado para resolver caminhos relativos. Durante o processo de criação do cluster HDInsight, você pode especificar um contêiner de blobs no Armazenamento do Azure como o sistema de arquivos padrão ou, com o HDInsight 3.5 e versões posteriores, você pode selecionar o Armazenamento do Azure ou o Azure Data Lake Store como o sistema de arquivos padrão. 

Os clusters HDInsight podem usar o Data Lake Store de duas maneiras:

* Como armazenamento padrão
* Como armazenamento adicional, com o Azure Storage Blob como o armazenamento padrão.

A partir de agora, somente alguns tipos/versões do cluster HDInsight dão suporte ao uso do Data Lake Store como armazenamento padrão e contas de armazenamento adicionais:

| Tipo de cluster HDInsight | Data Lake Store como armazenamento padrão | Data Lake Store como armazenamento adicional| Observações |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight versão 3.6 | sim | sim | |
| HDInsight versão 3.5 | sim | SIM | Com exceção do HBase|
| HDInsight versão 3.4 | Não | SIM | |
| HDInsight versão 3.3 | Não  | Não  | |
| HDInsight versão 3.2 | Não  | Sim | |
| Storm | | |Você pode usar o Data Lake Store para gravar os dados de uma topologia do Storm. Também é possível usar o Data Lake Store para dados de referência que, em seguida, podem ser lidos por uma topologia do Storm.|

O uso do Data Lake Store como uma conta de armazenamento adicional não afeta o desempenho nem a capacidade de ler ou gravar do cluster para o armazenamento do Azure.


## <a name="use-data-lake-store-as-default-storage"></a>Usar o Data Lake Store como armazenamento padrão

Quando HDInsight é implantado com o Data Lake Store como o armazenamento padrão, os arquivos relacionados a cluster são armazenados no Data Lake Store no seguinte local:

    adl://mydatalakestore/<cluster_root_path>/

em que `<cluster_root_path>` é o nome de uma pasta que você cria no Data Lake Store. Ao especificar um caminho raiz para cada cluster, você pode usar a mesma conta do Data Lake Store para mais de um cluster. Portanto, você terá uma configuração em que:

* O Cluster1 pode usar o caminho `adl://mydatalakestore/cluster1storage`
* O Cluster2 pode usar o caminho `adl://mydatalakestore/cluster2storage`

Observe que os dois clusters usam a mesma conta **mydatalakestore** do Data Lake Store. Cada cluster tem acesso ao seu próprio sistema de arquivos raiz no Data Lake Store. A experiência de implantação do Portal do Azure em particular, solicita que você use um nome de pasta como **/clusters/\<clustername>** para o caminho raiz.

Para poder usar um Data Lake Store como armazenamento padrão, você deve conceder o acesso à entidade do serviço para os seguintes caminhos:

- A raiz da conta do Data Lake Store.  Por exemplo: adl://mydatalakestore/.
- A pasta para todas as pastas de cluster.  Por exemplo: adl://mydatalakestore/clusters.
- A pasta para o cluster.  Por exemplo: adl://mydatalakestore/clusters/cluster1storage.

Para saber mais sobre como criar a entidade de segurança e conceder acesso, consulte [Configurar acesso ao Data Lake Store](#configure-data-lake-store-access).


## <a name="use-data-lake-store-as-additional-storage"></a>Usar o Data Lake Store como armazenamento adicional

Você também pode usar o Data Lake Store como armazenamento adicional para o cluster. Nesses casos, o armazenamento padrão do cluster pode ser um Azure Storage Blob ou uma conta do Data Lake Store. Se você estiver executando trabalhos de HDInsight com os dados armazenados no Data Lake Store como armazenamento adicional, use o caminho totalmente qualificado para os arquivos. Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Observe que agora não há **cluster_root_path** na URL. Isso ocorre porque o Data Lake Store não é um armazenamento padrão nesse caso, portanto, tudo o que você precisa fazer é fornecer o caminho para os arquivos.

Para poder usar um Data Lake Store como armazenamento adicional, você só precisa conceder acesso à entidade principal para os caminhos onde os arquivos são armazenados.  Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Para saber mais sobre como criar a entidade de segurança e conceder acesso, consulte [Configurar acesso ao Data Lake Store](#configure-data-lake-store-access).


## <a name="use-more-than-one-data-lake-store-accounts"></a>Usar mais de uma conta do Data Lake Store

Adicionar uma conta do Data Lake Store como adicional e adicionar mais de uma conta do Data Lake Store é feito dando ao cluster do HDInsight permissão sobre dados em uma ou mais contas do Data Lake Store. Consulte [Configurar acesso ao Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Configurar acesso ao Data Lake Store

Para configurar o acesso ao Data Lake Store do seu cluster HDInsight, você deve ter uma entidade de serviço do Azure AD (AzureActive directory). Somente um administrador do Azure AD pode criar uma entidade de serviço. A entidade de serviço deve ser criada com um certificado. Para obter mais informações, consulte [Início rápido: configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md), e [Criar entidade de serviço com certificado autoassinado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]
> Se você pretende usar o Azure Data Lake Store como armazenamento adicional para o cluster HDInsight, é altamente recomendável que faça isso ao criar o cluster, conforme descrito neste artigo. Adicionar o Azure Data Lake Store como mais armazenamento para um cluster HDInsight não é um cenário suportado.
>

## <a name="access-files-from-the-cluster"></a>Acessar arquivos do cluster

Há várias maneiras para acessar os arquivos no Data Lake Store em um cluster HDInsight.

* **Usando o nome totalmente qualificado**. Com essa abordagem, é necessário fornecer o caminho completo para o arquivo que você deseja acessar.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Usando o formato de caminho encurtado**. Com essa abordagem, você substitui o caminho até a raiz do cluster com adl:///. Portanto, no exemplo acima, é possível substituir `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` por `adl:///`.

        adl:///<file path>

* **Usando o caminho relativo**. Com essa abordagem, você só fornece o caminho relativo para o arquivo que deseja acessar. Por exemplo, se o caminho completo para o arquivo é:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Você pode acessar o mesmo arquivo sample.log usando esse caminho relativo em vez disso.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>Criar um cluster HDInsight com acesso ao Data Lake Store

Use os links a seguir para obter instruções detalhadas sobre como criar clusters HDInsight com acesso ao Data Lake Store.

* [Usando o Portal](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Usando o PowerShell (com o Data Lake Store como o armazenamento padrão)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Usando o PowerShell (com o Data Lake Store como o armazenamento adicional)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Usando modelos do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-store-access"></a>Atualizar o certificado do HDInsight para acesso do Data Lake Store

O código de exemplo do PowerShell a seguir lê um arquivo de certificado local e atualiza seu cluster do HDInsight com o novo certificado para acessar o Azure Data Lake Store. Forneça seu próprio nome do cluster HDInsight, nome do grupo de recursos, ID da assinatura, ID do aplicativo, caminho local para o certificado. Digite a senha quando solicitado.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subscriptionId

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o Azure Data Lake Store, compatível com HDFS, com o HDInsight. Isso permite que você crie soluções de aquisição de dados para arquivamento de longo prazo escalonáveis e use o HDInsight para desbloquear as informações nos dados armazenados estruturados e não estruturados.

Para obter mais informações, consulte:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Criar um cluster HDInsight para usar o Data Lake Store usando o Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Usar Assinaturas de Acesso Compartilhado do Armazenamento do Azure para restringir o acesso a dados com o HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
