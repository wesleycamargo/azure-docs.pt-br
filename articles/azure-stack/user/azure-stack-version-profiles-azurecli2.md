---
title: Conectar-se ao Azure Stack com a CLI | Microsoft Docs
description: Saiba como usar a interface de linha de comando de plataforma cruzada (CLI) para gerenciar e implantar recursos no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 40973fbdd1965eb84776fc9365718c65fa0149a7
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416982"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Use perfis de versão de API com a CLI do Azure no Azure Stack

Você pode seguir as etapas neste artigo para definir a Interface de linha de comando (CLI do Azure) para gerenciar recursos do Kit de desenvolvimento do Azure Stack de plataformas de cliente Linux, Mac e Windows.

## <a name="install-cli"></a>Instalar a CLI

Entre sua estação de trabalho de desenvolvimento e instale a CLI. O Azure Stack requer a versão 2.0 ou posterior da CLI do Azure. Você pode instalar essa versão, usando as etapas descritas a [instalar a CLI do Azure](/cli/azure/install-azure-cli) artigo. Para verificar se a instalação foi bem-sucedida, abra uma janela de prompt de comando ou terminal e execute o seguinte comando:

```azurecli
az --version
```

Você deve ver a versão da CLI do Azure e outras bibliotecas dependentes instaladas em seu computador.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar no certificado de raiz da autoridade de certificação do Azure Stack

1. Obter o certificado de raiz da autoridade de certificação do Azure Stack da [seu operador do Azure Stack](../azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate) e confiar nele. Para confiar no certificado de raiz da autoridade de certificação do Azure Stack, acrescente-o para o certificado existente do Python.

1. Localize o certificado em seu computador. O local pode variar, dependendo de onde você instalou o Python. Você precisará ter [pip](https://pip.pypa.io) e o [certifi](https://pypi.org/project/certifi/) módulo instalado. Você pode usar o seguinte comando do Python a partir do prompt do bash:

    ```bash  
    python -c "import certifi; print(certifi.where())"
    ```

    Anote o local do certificado. Por exemplo, `~/lib/python3.5/site-packages/certifi/cacert.pem`. O caminho específico depende do seu sistema operacional e a versão do Python que você instalou.

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>Definir o caminho para um computador de desenvolvimento dentro da nuvem

Se você estiver executando a CLI de um computador Linux que é criado dentro do ambiente do Azure Stack, execute o seguinte comando do bash com o caminho para o seu certificado.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>Definir o caminho para um computador de desenvolvimento fora da nuvem

Se você estiver executando a CLI de um computador fora do ambiente do Azure Stack:  

1. Configure [conectividade VPN para o Azure Stack](azure-stack-connect-azure-stack.md).
1. Copie o certificado PEM que você obteve do operador do Azure Stack e anote o local do arquivo (PATH_TO_PEM_FILE).
1. Execute os comandos nas seções a seguir, dependendo do sistema operacional em sua estação de trabalho de desenvolvimento.

#### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="windows"></a> Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting required information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>Obter o ponto de extremidade de aliases de máquina virtual

Antes de criar máquinas virtuais usando a CLI, entre em contato com o operador do Azure Stack e deve obter o ponto de extremidade de aliases de máquina virtual URI. Por exemplo, o Azure usa o seguinte URI: `https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json`. O administrador de nuvem deve definir um ponto de extremidade semelhante para o Azure Stack com as imagens que estão disponíveis no marketplace do Azure Stack. Você deve passar o URI do ponto de extremidade do `endpoint-vm-image-alias-doc` parâmetro para o `az cloud register` de comando, conforme mostrado na próxima seção. 
  
## <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

Use as seguintes etapas para se conectar ao Azure Stack:

1. Registre-se o seu ambiente de pilha do Azure executando o `az cloud register` comando. Em alguns cenários, a conectividade de internet de saída direta é roteada por meio de um proxy ou firewall, que impõe a interceptação de SSL. Nesses casos, o `az cloud register` comando pode falhar com um erro como "Não é possível obter pontos de extremidade da nuvem". Para contornar esse erro, você pode definir as seguintes variáveis de ambiente:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```
   
     a. Para registrar o *nuvem administrativa* ambiente, use:

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```
    b. Para registrar o *usuário* ambiente, use:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```
    c. Para registrar o *usuário* em um ambiente de multilocação, use:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --endpoint-active-directory-resource-id=<URI of the ActiveDirectoryServiceEndpointResourceID> \
        --profile 2018-03-01-hybrid
      ```
    d. Para registrar o usuário em um ambiente do AD FS, use:

      ```azurecli
      az cloud register \
        -n AzureStack  \
        --endpoint-resource-manager "https://management.local.azurestack.external" \
        --suffix-storage-endpoint "local.azurestack.external" \
        --suffix-keyvault-dns ".vault.local.azurestack.external"\
        --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" \
        --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/"\
        --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/"\
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --profile "2018-03-01-hybrid"
      ```
1. Defina o ambiente ativo, usando os comandos a seguir.
   
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

1. Atualize a configuração do seu ambiente para usar o perfil de versão de API específico do Azure Stack. Para atualizar a configuração, execute o seguinte comando:

    ```azurecli
    az cloud update \
      --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Se você estiver executando uma versão do Azure Stack antes da compilação 1808, você deve usar o perfil da versão de API **2017-03-09-profile** em vez do perfil da versão de API **2018-03-01-hybrid**.

1. Entre seu ambiente do Azure Stack usando o `az login` comando. Você pode entrar no ambiente do Azure Stack como um usuário ou como um [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md). 

    * Ambientes do AD do Azure
      * Entrar como um *usuário*: Você pode especificar o nome de usuário e senha diretamente dentro de `az login` de comando ou autenticar usando um navegador. Se sua conta tiver a autenticação multifator habilitada, você deve fazer o último:

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Se sua conta de usuário tiver a autenticação multifator habilitada, você pode usar o `az login` comando sem fornecer o `-u` parâmetro. Executar esse comando fornece uma URL e um código que você deve usar para autenticar.
   
      * Entrar como um *entidade de serviço*: Antes de entrar no, [criar uma entidade de serviço por meio do portal do Azure](azure-stack-create-service-principals.md) ou a CLI e atribuí-lo uma função. Agora, entre usando o comando a seguir:

      ```azurecli  
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```
    * Ambientes do AD FS

        * Entrar como um usuário usando um navegador da web com um código de dispositivo:  
           ```azurecli  
           az login --use-device-code
           ```

           > [!NOTE]  
           >Executando o comando fornece uma URL e um código que você deve usar para autenticar.

        * Entrar como uma entidade de serviço:
        
          1. Prepare o arquivo. PEM a ser usado para logon de entidade de serviço.

            * O computador cliente em que a entidade foi criada, exportar o certificado de entidade de serviço como um pfx com a chave privada localizado em `cert:\CurrentUser\My`; o nome tem o mesmo nome que a entidade do certificado.
        
            * Converta o pfx em pem (use o utilitário OpenSSL).

          2.  Entrar para a CLI:
            ```azurecli  
            az login --service-principal \
              -u <Client ID from the Service Principal details> \
              -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
              --tenant <Tenant ID> \
              --debug 
            ```

## <a name="test-the-connectivity"></a>Testar a conectividade

Com tudo configurado, usar a CLI para criar recursos no Azure Stack. Por exemplo, você pode criar um grupo de recursos para um aplicativo e adicionar uma máquina virtual. Use o comando a seguir para criar um grupo de recursos chamado "MyResourceGroup":

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Se o grupo de recursos é criado com êxito, o comando anterior gera as seguintes propriedades do recurso recém-criado:

![Criar um grupo de recursos](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Problemas conhecidos

Há problemas conhecidos ao usar a CLI do Azure Stack:

 - O modo interativo da CLI; Por exemplo, o `az interactive` de comando, ainda não há suporte no Azure Stack.
 - Para obter a lista de imagens de máquina virtual disponíveis no Azure Stack, use o `az vm image list --all` comando em vez do `az vm image list` comando. Especificando o `--all` opção garante que a resposta retorna apenas as imagens que estão disponíveis em seu ambiente do Azure Stack.
 - Aliases de imagem de máquina virtual que estão disponíveis no Azure podem não ser aplicáveis para o Azure Stack. Ao usar imagens de máquina virtual, você deve usar o parâmetro inteiro do URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) em vez do alias da imagem. Este URN deve corresponder às especificações de imagem conforme derivado do `az vm images list` comando.

## <a name="next-steps"></a>Próximas etapas

- [Implantar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)
- [Habilitar a CLI do Azure para usuários do Azure Stack (operador)](../azure-stack-cli-admin.md)
- [Gerenciar permissões de usuário](azure-stack-manage-permissions.md) 
