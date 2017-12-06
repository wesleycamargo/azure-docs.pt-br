---
title: Conecte-se a pilha do Azure com CLI | Microsoft Docs
description: Saiba como usar a interface de linha de comando de plataforma cruzada (CLI) para gerenciar e implantar recursos na pilha do Azure
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: sngun
ms.openlocfilehash: 9a0ad3d8c2cdd3cd1d46e789c2b65677ac5a10b1
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="install-and-configure-cli-for-use-with-azure-stack"></a>Instalar e configurar o CLI para uso com a pilha do Azure

Neste artigo, vamos orientá-lo pelo processo de usando a interface de linha de comando do Azure (CLI) para gerenciar recursos do Kit de desenvolvimento de pilha do Azure do Linux e plataformas de cliente Mac. 

## <a name="install-cli"></a>Instalar a CLI

Em seguida, entre sua estação de trabalho de desenvolvimento e instale CLI. A pilha do Azure requer a versão 2.0 do CLI do Azure. Você pode instalar que usando as etapas descritas no [instalar o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) artigo. Para verificar se a instalação foi bem-sucedida, abra uma janela de prompt de comando ou um terminal e execute o seguinte comando:

```azurecli
az --version
```

Você deve ver a versão da CLI do Azure e outras bibliotecas dependentes que estão instaladas no seu computador.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar no certificado de raiz da autoridade de certificação de pilha do Azure

Obtenha o certificado de raiz da autoridade de certificação de pilha do Azure do operador pilha do Azure e confiar nele. Para confiar no certificado de raiz da autoridade de certificação de pilha do Azure, anexá-la para o certificado existente do Python. Se você estiver executando a CLI de um computador Linux que é criado dentro do ambiente de pilha do Azure, execute o seguinte comando bash:

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

Se você estiver executando a CLI de um computador fora do ambiente do Azure Sack, primeiro você deve configurar o [conectividade de VPN a pilha do Azure](azure-stack-connect-azure-stack.md). Agora, copie o certificado PEM que você exportou anteriormente em sua estação de trabalho de desenvolvimento e execute os seguintes comandos, dependendo SO da estação de trabalho seu desenvolvimento.

### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting needed information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path root.pem -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>Obter o ponto de extremidade de aliases de máquina virtual

Antes dos usuários podem criar máquinas virtuais usando a CLI, eles devem entrar em contato com o operador de pilha do Azure e obter o ponto de extremidade de aliases de máquina virtual URI. Por exemplo, o Azure usa o seguinte URI: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json. O administrador de nuvem deve configurar um ponto de extremidade semelhante para a pilha do Azure com as imagens que estão disponíveis no mercado de pilha do Azure. Os usuários precisam passar o URI do ponto de extremidade para o `endpoint-vm-image-alias-doc` parâmetro para o `az cloud register` comando conforme mostrado na próxima seção. 
   

## <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

Use as seguintes etapas para se conectar ao Azure pilha:

1. Registrar o seu ambiente de pilha do Azure executando o `az cloud register` comando.
   
   a. Para registrar o *nuvem administrativa* ambiente, use:

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

   b. Para registrar o *usuário* ambiente, use:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

2. Defina o ambiente de active usando os comandos a seguir.

   a. Para o *nuvem administrativa* ambiente, use:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   b. Para o *usuário* ambiente, use:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

3. Atualize sua configuração de ambiente para usar o perfil de versão de API específico da pilha do Azure. Para atualizar a configuração, execute o seguinte comando:

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. Entre seu ambiente de pilha do Azure usando o `az login` comando. Você pode entrar no ambiente de pilha do Azure como um usuário ou como um [entidade de serviço](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects). 

   * Entrar como um *usuário*: você pode especificar o nome de usuário e senha diretamente dentro de `az login` comando ou autenticar usando um navegador. Você precisa fazer o último se sua conta tiver habilitada a autenticação multifator.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Se sua conta de usuário tiver habilitada a autenticação multifator, você pode usar o `az login command` sem fornecer o `-u` parâmetro. Executar o comando fornece uma URL e um código que você deve usar para autenticar.
   
   * Entrar como um *entidade de serviço*: antes de você entrar, [criar uma entidade de serviço por meio do portal do Azure](azure-stack-create-service-principals.md) ou CLI e atribuí-la uma função. Agora, entre usando o seguinte comando:

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>Testar a conectividade

Agora que temos tudo o que a instalação, vamos usar CLI para criar recursos na pilha do Azure. Por exemplo, você pode criar um grupo de recursos para um aplicativo e adicionar uma máquina virtual. Use o comando a seguir para criar um grupo de recursos denominado "MyResourceGroup":

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Se o grupo de recursos é criado com êxito, o comando anterior gera as seguintes propriedades do recurso recém-criado:

![Criar um grupo de recursos](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Problemas conhecidos
Há alguns problemas conhecidos que você deve estar ciente ao usar CLI na pilha do Azure:

* O modo interativo de CLI ou seja o `az interactive` comando ainda não é suportado na pilha do Azure.
* Para obter a lista de imagens de máquinas virtuais disponíveis na pilha do Azure, use o `az vm images list --all` comando em vez do `az vm image list` comando. Especificando o `--all` opção garante que a resposta retorna apenas as imagens que estão disponíveis em seu ambiente de pilha do Azure. 
* Aliases de imagem de máquina virtual que estão disponíveis no Azure podem não ser aplicáveis a pilha do Azure. Ao usar imagens da máquina virtual, você deve usar o parâmetro inteiro do URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) em vez do alias de imagem. Este URN deve corresponder às especificações de imagem derivado de `az vm images list` comando.
* Por padrão, a CLI 2.0 usa "Standard_DS1_v2" como o tamanho de imagem de máquina virtual padrão. No entanto, esse tamanho ainda não disponível na pilha do Azure, portanto, é necessário especificar o `--size` parâmetro explicitamente ao criar uma máquina virtual. Você pode obter a lista de tamanhos de máquinas virtuais que estão disponíveis na pilha do Azure usando o `az vm list-sizes --location <locationName>` comando.


## <a name="next-steps"></a>Próximas etapas

[Implantar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)

[Gerenciar permissões de usuário](azure-stack-manage-permissions.md)

