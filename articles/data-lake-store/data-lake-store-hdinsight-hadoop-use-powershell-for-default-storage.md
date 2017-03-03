---
title: "PowerShell: Cluster HDInsight do Azure Data Lake Store como o armazenamento padrão | Microsoft Docs"
description: Usar o Azure PowerShell para criar e usar clusters HDInsight com o Azure Data Lake
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/14/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 307070c755cff059b4b82494d28e06cf490a6f7a
ms.openlocfilehash: 6c5badbbea7385cac1407e4af148d5b647af04ac
ms.lasthandoff: 02/16/2017


---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-default-storage"></a>Use o Azure PowerShell para criar um cluster HDInsight com o Data Lake Store (como o armazenamento padrão)
> [!div class="op_single_selector"]
> * [Usando o Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usando o PowerShell (para o armazenamento padrão)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usando o PowerShell (para o armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usando o Gerenciador de Recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar o Azure PowerShell para configurar um cluster HDInsight com o Azure Data Lake Store **como o armazenamento padrão**. Para obter instruções sobre como criar um cluster HDInsight com o Azure Data Lake Store como armazenamento adicional, veja [Criar um cluster HDInsight com o Data Lake Store como armazenamento adicional](data-lake-store-hdinsight-hadoop-use-powershell.md).

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Usando o Data Lake Store para armazenamento de cluster HDInsight

Aqui estão algumas considerações importantes para usar o HDInsight com o Data Lake Store:

* A opção para criar clusters HDInsight com acesso ao Data Lake Store como armazenamento padrão está disponível para o HDInsight versão 3.5.

* Para clusters HBase (Windows e Linux), o Data Lake Store **não é compatível** como uma opção de armazenamento, tanto para armazenamento padrão quanto para armazenamento adicional.


A configuração do HDInsight para trabalhar com o Repositório Data Lake usando o PowerShell envolve as seguintes etapas:

* Criar um Repositório Azure Data Lake
* Configurar a autenticação para acesso baseado em dados ao Repositório Data Lake
* Criar o cluster HDInsight com a autenticação para o Repositório Data Lake
* Executar um trabalho de teste no cluster

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azureps-cmdlets-docs).
* **SDK do Windows**. Você pode instalá-lo clicando [aqui](https://dev.windows.com/en-us/downloads). Use isso para criar um certificado de segurança.
* **Entidade de serviço do Azure Active Directory**. As etapas neste tutorial fornecem instruções sobre como criar uma entidade de serviço no Azure AD. No entanto, você deve ser administrador do Azure AD para poder criar uma entidade de serviço. Se você for administrador do Azure AD, poderá ignorar esse pré-requisito e continuar com o tutorial.

    **Se você não for um administrador do Azure AD**, não poderá executar as etapas necessárias para criar uma entidade de serviço. Nesse caso, o administrador do Azure AD deverá primeiro criar uma entidade de serviço antes de criar um cluster HDInsight com Data Lake Store. Além disso, a entidade de serviço deve ser criada usando um certificado, conforme descrito em [Criar uma entidade de serviço com certificado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="create-an-azure-data-lake-store"></a>Criar um Repositório Azure Data Lake
Execute estas etapas para criar um Repositório Data Lake.

1. Na área de trabalho, abra uma nova janela do Azure PowerShell e insira o trecho a seguir. Quando solicitado a fazer logon, lembre-se de fazer logon como o proprietário ou um dos administradores da assinatura:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Se você receber um erro semelhante a `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` ao registrar o provedor de recursos do Repositório Data Late, é possível que sua assinatura não esteja na lista branca do Repositório Azure Data Lake. Verifique se você habilitou sua assinatura do Azure para a visualização pública do Data Lake Store seguindo estas [instruções](data-lake-store-get-started-portal.md).
   >
   >
2. Uma conta do Repositório Azure Data Lake está associada a um Grupo de Recursos do Azure. Comece criando um Grupo de Recursos do Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Criar um Grupo de Recursos do Azure](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Criar um Grupo de Recursos do Azure")
3. Crie uma conta do Repositório Azure Data Lake. O nome especificado para a conta deve conter apenas letras minúsculas e números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Criar uma conta do Azure Data Lake](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Criar uma conta do Azure Data Lake")
4. Verifique se a conta foi criada com êxito.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    A saída para isso deve ser **True**.

5. Usar o Data Lake Store como armazenamento padrão exige que você especifique um caminho raiz para o qual os arquivos específicos do cluster sejam copiados durante a criação do cluster. Use os cmdlets abaixo para criar um caminho raiz, que no trecho a seguir é **/clusters/hdiadlcluster**.

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurar a autenticação para acesso baseado em dados ao Repositório Data Lake
Cada assinatura do Azure está associada com um Azure Active Directory. Os usuários e serviços que acessam os recursos da assinatura usando o Portal Clássico do Azure ou a API do Azure Resource Manager primeiro precisam realizar a autenticação com o Azure Active Directory. O acesso é concedido às assinaturas e serviços do Azure atribuindo a função apropriada para eles em um recurso do Azure.  Para serviços, uma entidade de serviço identifica o serviço no Active Directory do Azure (AAD). Esta seção ilustra como conceder a um serviço de aplicativo, como o HDInsight, acesso a um recurso do Azure (a conta do Repositório Azure Data Lake criada anteriormente), criando uma entidade de serviço para o aplicativo e atribuindo funções a ela por meio do Azure PowerShell.

Para configurar a autenticação do Active Directory para o Azure Data Lake, você deve executar as seguintes tarefas.

* Crie um certificado autoassinado
* Criar um aplicativo no Active Directory do Azure e uma entidade de serviço

### <a name="create-a-self-signed-certificate"></a>Crie um certificado autoassinado
Verifique se o [SDK do Windows](https://dev.windows.com/en-us/downloads) está instalado antes de continuar com as etapas nesta seção. Você também deve ter criado um diretório, como **C:\mycertdir**, no qual o certificado será criado.

1. Na janela do PowerShell, navegue até o local no qual você instalou o SDK do Windows (normalmente, `C:\Program Files (x86)\Windows Kits\10\bin\x86` e use o utilitário [MakeCert][makecert] para criar um certificado autoassinado e uma chave privada. Use os seguintes comandos.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        
        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Você receberá uma solicitação para inserir a senha da chave privada. Após a execução bem-sucedida do comando, você verá um **CertFile.cer** e **mykey.pvk** no diretório de certificado especificado.
2. Use o utilitário [Pvk2Pfx][pvk2pfx] para converter os arquivos .pvk e .cer criados pelo MakeCert em um arquivo .pfx. Execute o comando a seguir.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando receber a solicitação, digite a senha da chave privada especificada anteriormente. O valor especificado para o parâmetro **-po** é a senha que está associada ao arquivo .pfx. Após a conclusão bem-sucedida do comando, você também verá um arquivo CertFile.pfx no diretório de certificado especificado.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Criar um Active Directory do Azure e uma entidade de serviço
Nesta seção, você executará as etapas para criar uma entidade de serviço para um aplicativo do Active Directory do Azure, atribuir uma função à entidade de serviço e autenticar como a entidade de serviço ao fornecer um certificado. Execute os seguintes comandos para criar um aplicativo no Active Directory do Azure.

1. Cole os seguintes cmdlets na janela do console do PowerShell. Verifique se o valor especificado para a propriedade **-DisplayName** é exclusivo. Além disso, os valores para **-HomePage** e **-IdentiferUris** são valores de espaço reservado e não são verificados.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Crie uma entidade de serviço usando a ID do aplicativo.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Conceda acesso à raiz do Data Lake Store e a todas as pastas no caminho raiz especificado anteriormente à entidade de serviço. Use os cmdlets abaixo.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-default-storage"></a>Criar um cluster Linux HDInsight com o Data Lake Store como armazenamento padrão

Nesta seção, criamos um cluster Linux Hadoop HDInsight com o Data Lake Store como armazenamento padrão. Para esta versão, o cluster HDInsight e o Data Lake Store devem estar no mesmo local.

1. Comece recuperando a ID do locatário da assinatura. Você precisará disso posteriormente.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
        
2. Crie o cluster HDInsight. Use os seguintes cmdlets.

        # Set these variables
        
        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                          # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.5" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    Após a conclusão do cmdlet, você verá uma saída listando os detalhes do cluster.

        
## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Executar trabalhos de teste no cluster HDInsight para usar o Repositório Data Lake
Após a configuração de um cluster HDInsight, você poderá executar trabalhos de teste no cluster a fim de testar se o cluster HDInsight pode acessar o Repositório Data Lake. Para fazer isso, executaremos um trabalho do Hive de exemplo que cria uma tabela usando os dados de exemplo que já estão disponíveis no Data Lake Store em **<cluster root>/example/data/sample.log**.

Nesta seção, você acessará o cluster Linux HDInsight criado por você por SSH e executará uma consulta Hive de exemplo.

* Se você estiver usando um cliente Windows para acessar o cluster por SSH, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se você estiver usando um cliente Linux para acessar o cluster por SSH, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Uma vez conectado, inicie a CLI do Hive usando o seguinte comando:

        hive
2. Usando a CLI, insira as instruções a seguir para criar uma nova tabela chamada **vehicles** usando os dados de exemplo no Repositório Data Lake:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Você deve ver a saída da consulta no console do SSH. 

    > [!NOTE]
       > O caminho para os dados de exemplo no comando CREATE TABLE acima é `adl:///example/data/`, em que `adl:///` é a raiz do cluster. Adotando o exemplo da raiz do cluster especificada neste tutorial, isso será `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Portanto, você pode usar a alternativa mais curta ou fornecer o caminho completo para a raiz do cluster. 
       >
       >



## <a name="access-data-lake-store-using-hdfs-commands"></a>Repositório Data Lake usando comandos do HDFS
Após a configuração do cluster HDInsight para usar o Repositório Data Lake, você poderá usar os comandos do shell HDFS para acessar o armazenamento.

Nesta seção, você acessará o cluster Linux HDInsight criado por você por SSH e executará os comandos HDFS. 

* Se você estiver usando um cliente Windows para acessar o cluster por SSH, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se você estiver usando um cliente Linux para acessar o cluster por SSH, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Uma vez conectado, use o comando do sistema de arquivos HDFS a seguir para listar os arquivos no Repositório Data Lake.

    hdfs dfs -ls adl:///

Você também pode usar o comando `hdfs dfs -put` para carregar alguns arquivos no Repositório Data Lake e usar `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.

## <a name="see-also"></a>Consulte também
* [Portal: criar um cluster HDInsight para usar o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

