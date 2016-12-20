---
title: Cluster HPC Pack 2016 no Azure | Microsoft Docs
description: Saiba como implantar um cluster HPC Pack 2016 no Azure
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/14/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 7e7dc6b6d58da556dfa07d5d21b3e70483d36ef9
ms.openlocfilehash: 688f3f0885606949a265300af215f416e8f94155


---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>Implantar um cluster HPC Pack 2016 no Azure

Siga as etapas neste artigo para implantar um cluster [Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) em máquinas virtuais do Azure. HPC Pack é a solução de HPC gratuita da Microsoft fundamentada nas tecnologias Microsoft Azure e Windows Server e que dá suporte a uma ampla gama de cargas de trabalho de HPC.

Use um do [modelos do Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) para implantar o cluster HPC Pack 2016. Você tem várias opções de topologia de cluster com quantidades diferentes de nós principais do cluster, e com nós de computação Linux ou Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="pfx-certificate"></a>Certificado PFX

Um cluster Microsoft HPC Pack 2016 requer um certificado PFX (Troca de Informações Pessoais) para proteger a comunicação entre os nós HPC. O certificado deve atender aos seguintes requisitos:

* Ele deve ter uma chave privada capaz de fazer a troca das chaves
* O uso da chave inclui a Assinatura Digital e a Codificação de Chave
* O uso avançado de chave inclui a Autenticação de Servidor e a Autenticação de Cliente

Antes de implantar o cluster HPC, carregue o certificado em um [cofre de chaves do Azure](../key-vault/index.md) como um segredo e registre as seguintes informações para uso durante a implantação: **Nome do cofre**, **Grupo de recursos do cofre**, **URL do certificado** e **Impressão digital do certificado**.

Segue um exemplo de script do PowerShell para carregar o certificado. Para saber mais sobre como carregar um certificado em um cofre de chaves do Azure, confira [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md).

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>Topologias com suporte

Escolha uma da [modelos do Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) para implantar o cluster HPC Pack 2016. A seguir estão as arquiteturas de alto nível das três topologias de cluster com suporte. As topologias de alta disponibilidade incluem vários nós de cabeçalho do cluster.

1. Cluster de alta disponibilidade com o domínio do Active Directory

    ![Cluster de alta disponibilidade no domínio do AD](./media/virtual-machines-windows-hpcpack-2016-cluster/haad.png)



2. Cluster de alta disponibilidade sem o domínio do Active Directory

    ![Cluster de alta disponibilidade sem domínio do AD](./media/virtual-machines-windows-hpcpack-2016-cluster/hanoad.png)

3. Cluster com um único nó de cabeçalho

   ![Cluster com um único nó principal](./media/virtual-machines-windows-hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>Implantar um cluster

Para criar o cluster, escolha um modelo e clique em **Implantar no Azure**. No [portal do Azure](https://portal.azure.com), especifique parâmetros para o modelo, conforme descrito nas etapas a seguir. Cada modelo cria todos os recursos do Azure necessários para a infraestrutura do cluster HPC. Os recursos incluem uma rede virtual do Azure, um endereço IP público, um balanceador de carga (apenas para um cluster de alta disponibilidade), interfaces de rede, conjuntos de disponibilidade, contas de armazenamento e máquinas virtuais.

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>Etapa 1: selecionar a assinatura, o local e o grupo de recursos

A **assinatura** e o **local** devem ser os mesmos que você especificou quando carregou o certificado PFX (confira os pré-requisitos). Recomendamos que você crie um **grupo de recursos** para a implantação.

### <a name="step-2-specify-the-parameter-settings"></a>Etapa 2: especificar as configurações de parâmetro

Digite ou modifique os valores dos parâmetros do modelo. Clique no ícone ao lado de cada parâmetro para obter informações de Ajuda. Confira também as diretrizes sobre os [tamanhos de VM disponíveis](virtual-machines-windows-sizes.md).

Especifique os valores registrados nos pré-requisitos para os seguintes parâmetros: **Nome do cofre**, **Grupo de recursos do cofre**, **URL do certificado** e **Impressão digital do certificado**.

###<a name="step-3-review-legal-terms-and-create"></a>Etapa 3. Examinar os termos legais e criar
Clique em **Examinar termos legais** para examinar os termos. Se você concordar, clique em **Comprar** e clique em **Criar** para iniciar a implantação.

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster
1. Depois que o cluster HPC Pack for implantado, vá para o [portal do Azure](https://portal.azure.com). Clique em **Grupos de recursos** e encontre o grupo de recursos no qual o cluster foi implantado. Você pode encontrar as máquinas virtuais do nó de cabeçalho.

    ![Nós de cabeçalho de cluster no portal](./media/virtual-machines-windows-hpcpack-2016-cluster/clusterhns.png)

2. Clique em um nó de cabeçalho (em um cluster de alta disponibilidade, clique em qualquer um dos nós de cabeçalho). Em **Essentials**, você pode encontrar o endereço IP público ou o nome DNS completo do cluster.

    ![Configurações de conexão do cluster](./media/virtual-machines-windows-hpcpack-2016-cluster/clusterconnect.png)

3. Clique em **Conectar** para fazer logon em um dos nós de cabeçalho usando a Área de Trabalho Remota com seu nome de usuário de administrador especificado. Se o cluster implantado estiver em um domínio do Active Directory, o nome de usuário estará no formato <privateDomainName>\<NomedeUsuárioAdmin > (por exemplo, hpc.local\hpcadmin).

## <a name="next-steps"></a>Próximas etapas
* Envie trabalhos para seu cluster. Confira [Enviar trabalhos para HPC e cluster HCP Pack no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md) e [Gerenciar um cluster HPC Pack 2016 no Azure usando o Azure Active Directory](virtual-machines-windows-hpcpack-cluster-active-directory.md).




<!--HONumber=Nov16_HO3-->


