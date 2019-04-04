---
title: Conectar-se ao Azure Stack com a CLI | Microsoft Docs
description: Saiba como usar a interface de linha de comando de plataforma cruzada (CLI) para gerenciar e implantar recursos no Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 21167366ff3af2bb360c33eaae9d591020bf11a5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487576"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Use perfis de versão de API com a CLI do Azure no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode seguir as etapas neste artigo para definir a Interface de linha de comando (CLI do Azure) para gerenciar recursos do Kit de desenvolvimento na pilha do Azure (ASDK) em plataformas de cliente Linux, Mac e Windows.

## <a name="prepare-for-azure-cli"></a>Preparar para a CLI do Azure

Será necessário o certificado da AC raiz para o Azure Stack usar a CLI do Azure no computador de desenvolvimento. Você pode usar o certificado para gerenciar os recursos por meio da CLI.

 - **O certificado de raiz da autoridade de certificação do Azure Stack** é necessária se você estiver usando a CLI em uma estação de trabalho fora o ASDK.  

 - **O ponto de extremidade de aliases de máquina virtual** fornece um alias, como "UbuntuLTS" ou "Win2012Datacenter", que faz referência a um editor de imagem, oferta, SKU e versão como um único parâmetro durante a implantação de VMs.  

As seções a seguir descrevem como obter esses valores.

### <a name="export-the-azure-stack-ca-root-certificate"></a>Exportar o certificado de raiz da autoridade de certificação do Azure Stack

Se você estiver usando um sistema integrado, você não precisa exportar o certificado de raiz da autoridade de certificação. Você precisará exportar o certificado de raiz da autoridade de certificação em um ASDK.

Para exportar o certificado de raiz ASDK no formato PEM:

1. [Criar um VM do Windows no Azure Stack](azure-stack-quick-windows-portal.md).

2. Entrar para a máquina, abra um prompt elevado do PowerShell e, em seguida, execute o seguinte script:

    ```powershell  
      $label = "AzureStackSelfSignedRootCert"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }

    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
    ```

3. Copie o certificado em seu computador local.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configurar o ponto de extremidade de aliases de máquina virtual

Você pode definir um ponto de extremidade publicamente acessível que hospeda um arquivo de alias de máquina virtual. O arquivo de alias de máquina virtual é um arquivo JSON que fornece um nome comum para uma imagem. Quando você implanta uma VM como um parâmetro de CLI do Azure, você usará o nome.

1. Se você publicar uma imagem personalizada, anote as informações de publicador, oferta, SKU e versão que você especificou durante a publicação. Se for uma imagem do marketplace, você pode exibir as informações usando o ```Get-AzureVMImage``` cmdlet.  

2. Baixe o [arquivo de exemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) do GitHub.

3. Crie uma conta de armazenamento no Azure Stack. Quando isso for feito, crie um contêiner de blob. Definir a política de acesso para "public".  

4. Carregar o arquivo JSON para o novo contêiner. Quando isso for concluído, você pode exibir a URL do blob. Selecione o nome do blob e, em seguida, selecionando a URL das propriedades do blob.

### <a name="install-or-upgrade-cli"></a>Instalar ou atualizar a CLI

Entre sua estação de trabalho de desenvolvimento e instale a CLI. O Azure Stack requer a versão 2.0 ou posterior da CLI do Azure. A versão mais recente dos perfis de API requer uma versão atual da CLI.  Você pode instalar a CLI, usando as etapas descritas a [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) artigo. Para verificar se a instalação foi bem-sucedida, abra uma janela de prompt de comando ou terminal e execute o seguinte comando:

```shell
az --version
```

Você deve ver a versão da CLI do Azure e outras bibliotecas dependentes instaladas em seu computador.

### <a name="install-python-on-windows"></a>instalar o Python no Windows

1. Instale [Python 3 em seu sistema](https://www.python.org/downloads/).

2. Atualize o PIP. PIP é um Gerenciador de pacotes para o Python. Abra um prompt de comando ou um prompt do PowerShell com privilégios elevados e digite o seguinte comando:

    ```powershell  
    python -m pip install --upgrade pip
    ```

3. Instalar o **certifi** módulo. [Certifi](https://pypi.org/project/certifi/) um módulo e uma coleção de certificados raiz para validar a confiabilidade de certificados SSL ao verificar a identidade dos hosts TLS. Abra um prompt de comando ou um prompt do PowerShell com privilégios elevados e digite o seguinte comando:

    ```powershell
    pip install certifi
    ```

### <a name="install-python-on-linux"></a>Instalar o Python no Linux

1. A imagem do Ubuntu 16.04 vem com o Python 2.7 e 3.5 do Python instalado por padrão. Você pode verificar sua versão do Python 3, executando o seguinte comando:

    ```bash  
    python3 --version
    ```

2. Atualize o PIP. PIP é um Gerenciador de pacotes para o Python. Abra um prompt de comando ou um prompt do PowerShell com privilégios elevados e digite o seguinte comando:

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. Instalar o **certifi** módulo. [Certifi](https://pypi.org/project/certifi/) é uma coleção de certificados raiz para validar a confiabilidade de certificados SSL ao verificar a identidade dos hosts TLS. Abra um prompt de comando ou um prompt do PowerShell com privilégios elevados e digite o seguinte comando:

    ```bash
    pip3 install certifi
    ```

### <a name="install-python-on-macos"></a>Instalar o Python no macOS

1. Instale [Python 3 em seu sistema](https://www.python.org/downloads/). Para versões do Python 3.7, Python.org fornece duas opções de binários do instalador para download. A variante de padrão é 64-bit-only e funciona no macOS 10.9 (Mavericks) e sistemas posteriores. Verifique sua versão do python abrindo o terminal e digitando o seguinte comando:

    ```bash  
    python3 --version
    ```

2. Atualize o PIP. PIP é um Gerenciador de pacotes para o Python. Abra um prompt de comando ou um prompt do PowerShell com privilégios elevados e digite o seguinte comando:

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. Instalar o **certifi** módulo. [Certifi](https://pypi.org/project/certifi/) um módulo e uma coleção de certificados raiz para validar a confiabilidade de certificados SSL ao verificar a identidade dos hosts TLS. Abra um prompt de comando ou um prompt do PowerShell com privilégios elevados e digite o seguinte comando:

    ```bash
    pip3 install certifi
    ```

## <a name="windows-azure-ad"></a>Windows (Azure AD Azure)

Esta seção explica como configurar a CLI se você estiver usando o Azure AD como o serviço de gerenciamento de identidade e está usando a CLI em um computador Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar no certificado de raiz da autoridade de certificação do Azure Stack

Se você estiver usando o ASDK, você precisará confiar no certificado de raiz da autoridade de certificação no computador remoto. Você não precisará fazer isso com os sistemas integrados.

Para confiar no certificado de raiz da autoridade de certificação do Azure Stack, acrescente-o para o certificado existente do Python.

1. Localize o certificado em seu computador. O local pode variar, dependendo de onde você instalou o Python. Abra um prompt de comando ou um prompt do PowerShell com privilégios elevados e digite o seguinte comando:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Anote o local do certificado. Por exemplo, `~/lib/python3.5/site-packages/certifi/cacert.pem`. O caminho específico dependem do seu sistema operacional e a versão do Python que você instalou.

2. Confie no certificado de raiz de autoridade de certificação do Azure Stack anexando-o para o certificado existente do Python.

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

### <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

1. Registre-se o seu ambiente de pilha do Azure executando o `az cloud register` comando.

    Em alguns cenários, a conectividade de internet de saída direta é roteada por meio de um proxy ou firewall, que impõe a interceptação de SSL. Nesses casos, o `az cloud register` comando pode falhar com um erro como "Não é possível obter pontos de extremidade da nuvem". Para contornar esse erro, você pode definir as seguintes variáveis de ambiente:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Registre o seu ambiente. Use os seguintes parâmetros ao executar `az cloud register`.

    | Valor | Exemplo | DESCRIÇÃO |
    | --- | --- | --- |
    | Nome do ambiente | AzureStackUser | Use `AzureStackUser` para o ambiente do usuário. Se você for operador, especifique `AzureStackAdmin`. |
    | Ponto de extremidade do Gerenciador de recursos | https://management.local.azurestack.external | O **ResourceManagerUrl** no Azure Stack desenvolvimento ASDK (Kit) é: `https://management.local.azurestack.external/` O **ResourceManagerUrl** em sistemas integrados é: `https://management.<region>.<fqdn>/` Para recuperar os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Se você tiver uma pergunta sobre o ponto de extremidade do sistema integrado, entre em contato com seu operador de nuvem. |
    | Ponto de extremidade de armazenamento | local.azurestack.external | `local.azurestack.external` é para o ASDK. Para um sistema integrado, você desejará usar um ponto de extremidade para o seu sistema.  |
    | Sufixo Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` é para o ASDK. Para um sistema integrado, você desejará usar um ponto de extremidade para o seu sistema.  |
    | Imagem de VM alias doc ponto de extremidade; | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI do documento que contém os aliases de imagem de máquina virtual. Para obter mais informações, consulte [# # # configurar o ponto de extremidade de aliases de máquina virtual](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Defina o ambiente ativo, usando os comandos a seguir.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Atualize a configuração do seu ambiente para usar o perfil de versão de API específico do Azure Stack. Para atualizar a configuração, execute o seguinte comando:

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Se você estiver executando uma versão do Azure Stack antes da compilação 1808, você deve usar o perfil da versão de API **2017-03-09-profile** em vez do perfil da versão de API **2018-03-01-hybrid**. Você precisará estar usando uma versão recente da CLI do Azure.
 
1. Entre seu ambiente do Azure Stack usando o `az login` comando. Você pode entrar no ambiente do Azure Stack como um usuário ou como um [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md). 

   - Entrar como um *usuário*: 

     Você pode especificar o nome de usuário e senha diretamente dentro de `az login` de comando ou autenticar usando um navegador. Se sua conta tiver a autenticação multifator habilitada, você deve fazer o último:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Se sua conta de usuário tiver a autenticação multifator habilitada, você pode usar o `az login` comando sem fornecer o `-u` parâmetro. Executar esse comando fornece uma URL e um código que você deve usar para autenticar.

   - Entrar como um *entidade de serviço*: 
    
     Antes de entrar no, [criar uma entidade de serviço por meio do portal do Azure](azure-stack-create-service-principals.md) ou a CLI e atribuí-lo uma função. Agora, entre usando o comando a seguir:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Testar a conectividade

Com tudo configurado, usar a CLI para criar recursos no Azure Stack. Por exemplo, você pode criar um grupo de recursos para um aplicativo e adicionar uma máquina virtual. Use o comando a seguir para criar um grupo de recursos chamado "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Se o grupo de recursos é criado com êxito, o comando anterior gera as seguintes propriedades do recurso recém-criado:

![Criar um grupo de recursos](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

Esta seção explica como configurar a CLI se você estiver usando o Active Directory Federated Services (AD FS) como seu serviço de gerenciamento de identidade e está usando a CLI em um computador Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar no certificado de raiz da autoridade de certificação do Azure Stack

Se você estiver usando o ASDK, você precisará confiar no certificado de raiz da autoridade de certificação no computador remoto. Você não precisará fazer isso com os sistemas integrados.

1. Localize o certificado em seu computador. O local pode variar, dependendo de onde você instalou o Python. Abra um prompt de comando ou um prompt do PowerShell com privilégios elevados e digite o seguinte comando:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Anote o local do certificado. Por exemplo, `~/lib/python3.5/site-packages/certifi/cacert.pem`. O caminho específico dependem do seu sistema operacional e a versão do Python que você instalou.

2. Confie no certificado de raiz de autoridade de certificação do Azure Stack anexando-o para o certificado existente do Python.

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

### <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

1. Registre-se o seu ambiente de pilha do Azure executando o `az cloud register` comando.

    Em alguns cenários, a conectividade de internet de saída direta é roteada por meio de um proxy ou firewall, que impõe a interceptação de SSL. Nesses casos, o `az cloud register` comando pode falhar com um erro como "Não é possível obter pontos de extremidade da nuvem". Para contornar esse erro, você pode definir as seguintes variáveis de ambiente:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Registre o seu ambiente. Use os seguintes parâmetros ao executar `az cloud register`.

    | Valor | Exemplo | DESCRIÇÃO |
    | --- | --- | --- |
    | Nome do ambiente | AzureStackUser | Use `AzureStackUser` para o ambiente do usuário. Se você for operador, especifique `AzureStackAdmin`. |
    | Ponto de extremidade do Gerenciador de recursos | https://management.local.azurestack.external | O **ResourceManagerUrl** no Azure Stack desenvolvimento ASDK (Kit) é: `https://management.local.azurestack.external/` O **ResourceManagerUrl** em sistemas integrados é: `https://management.<region>.<fqdn>/` Para recuperar os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Se você tiver uma pergunta sobre o ponto de extremidade do sistema integrado, entre em contato com seu operador de nuvem. |
    | Ponto de extremidade de armazenamento | local.azurestack.external | `local.azurestack.external` é para o ASDK. Para um sistema integrado, você desejará usar um ponto de extremidade para o seu sistema.  |
    | Sufixo Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` é para o ASDK. Para um sistema integrado, você desejará usar um ponto de extremidade para o seu sistema.  |
    | Imagem de VM alias doc ponto de extremidade; | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI do documento que contém os aliases de imagem de máquina virtual. Para obter mais informações, consulte [# # # configurar o ponto de extremidade de aliases de máquina virtual](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Defina o ambiente ativo, usando os comandos a seguir.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Atualize a configuração do seu ambiente para usar o perfil de versão de API específico do Azure Stack. Para atualizar a configuração, execute o seguinte comando:

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Se você estiver executando uma versão do Azure Stack antes da compilação 1808, você deve usar o perfil da versão de API **2017-03-09-profile** em vez do perfil da versão de API **2018-03-01-hybrid**. Você precisará estar usando uma versão recente da CLI do Azure.

1. Entre seu ambiente do Azure Stack usando o `az login` comando. Você pode entrar no ambiente do Azure Stack como um usuário ou como um [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md). 

   - Entrar como um *usuário*:

     Você pode especificar o nome de usuário e senha diretamente dentro de `az login` de comando ou autenticar usando um navegador. Se sua conta tiver a autenticação multifator habilitada, você deve fazer o último:

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/" --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2018-03-01-hybrid"
     ```

     > [!NOTE]
     > Se sua conta de usuário tiver a autenticação multifator habilitada, você pode usar o `az login` comando sem fornecer o `-u` parâmetro. Executar esse comando fornece uma URL e um código que você deve usar para autenticar.

   - Entrar como um *entidade de serviço*: 
    
     Prepare o arquivo. PEM a ser usado para logon de entidade de serviço.

     O computador cliente em que a entidade foi criada, exportar o certificado de entidade de serviço como um pfx com a chave privada localizado em `cert:\CurrentUser\My`; o nome tem o mesmo nome que a entidade do certificado.

     Converta o pfx em pem (use o utilitário OpenSSL).

     Entrar para a CLI:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>Testar a conectividade

Com tudo configurado, usar a CLI para criar recursos no Azure Stack. Por exemplo, você pode criar um grupo de recursos para um aplicativo e adicionar uma máquina virtual. Use o comando a seguir para criar um grupo de recursos chamado "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Se o grupo de recursos é criado com êxito, o comando anterior gera as seguintes propriedades do recurso recém-criado:

![Criar um grupo de recursos](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

Esta seção explica como configurar a CLI se você estiver usando o Azure AD como o serviço de gerenciamento de identidade e está usando a CLI em um computador Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar no certificado de raiz da autoridade de certificação do Azure Stack

Se você estiver usando o ASDK, você precisará confiar no certificado de raiz da autoridade de certificação no computador remoto. Você não precisará fazer isso com os sistemas integrados.

Confie no certificado de raiz de autoridade de certificação do Azure Stack anexando-o para o certificado existente do Python.

1. Localize o certificado em seu computador. O local pode variar, dependendo de onde você instalou o Python. Você precisará ter o pip e o certifi [módulo instalado](#install-python-on-linux). Você pode usar o seguinte comando do Python a partir do prompt do bash:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Anote o local do certificado. Por exemplo, `~/lib/python3.5/site-packages/certifi/cacert.pem`. O caminho específico depende do seu sistema operacional e a versão do Python que você instalou.

2. Execute o seguinte comando do bash com o caminho para o seu certificado.

   - Para um computador Linux remoto:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Para uma máquina Linux dentro do ambiente do Azure Stack:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

Use as seguintes etapas para se conectar ao Azure Stack:

1. Registre-se o seu ambiente de pilha do Azure executando o `az cloud register` comando. Em alguns cenários, a conectividade de internet de saída direta é roteada por meio de um proxy ou firewall, que impõe a interceptação de SSL. Nesses casos, o `az cloud register` comando pode falhar com um erro como "Não é possível obter pontos de extremidade da nuvem". Para contornar esse erro, você pode definir as seguintes variáveis de ambiente:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Registre o seu ambiente. Use os seguintes parâmetros ao executar `az cloud register`.

    | Valor | Exemplo | DESCRIÇÃO |
    | --- | --- | --- |
    | Nome do ambiente | AzureStackUser | Use `AzureStackUser` para o ambiente do usuário. Se você for operador, especifique `AzureStackAdmin`. |
    | Ponto de extremidade do Gerenciador de recursos | https://management.local.azurestack.external | O **ResourceManagerUrl** no Azure Stack desenvolvimento ASDK (Kit) é: `https://management.local.azurestack.external/` O **ResourceManagerUrl** em sistemas integrados é: `https://management.<region>.<fqdn>/` Para recuperar os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Se você tiver uma pergunta sobre o ponto de extremidade do sistema integrado, entre em contato com seu operador de nuvem. |
    | Ponto de extremidade de armazenamento | local.azurestack.external | `local.azurestack.external` é para o ASDK. Para um sistema integrado, você desejará usar um ponto de extremidade para o seu sistema.  |
    | Sufixo Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` é para o ASDK. Para um sistema integrado, você desejará usar um ponto de extremidade para o seu sistema.  |
    | Imagem de VM alias doc ponto de extremidade; | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI do documento que contém os aliases de imagem de máquina virtual. Para obter mais informações, consulte [# # # configurar o ponto de extremidade de aliases de máquina virtual](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Defina o ambiente do Active Directory. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Atualize a configuração do seu ambiente para usar o perfil de versão de API específico do Azure Stack. Para atualizar a configuração, execute o seguinte comando:

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Se você estiver executando uma versão do Azure Stack antes da compilação 1808, você deve usar o perfil da versão de API **2017-03-09-profile** em vez do perfil da versão de API **2018-03-01-hybrid**. Você precisará estar usando uma versão recente da CLI do Azure.

5. Entre seu ambiente do Azure Stack usando o `az login` comando. Você pode entrar no ambiente do Azure Stack como um usuário ou como um [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md). 

   * Entrar como um *usuário*:

     Você pode especificar o nome de usuário e senha diretamente dentro de `az login` de comando ou autenticar usando um navegador. Se sua conta tiver a autenticação multifator habilitada, você deve fazer o último:

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Se sua conta de usuário tiver a autenticação multifator habilitada, você pode usar o `az login` comando sem fornecer o `-u` parâmetro. Executar esse comando fornece uma URL e um código que você deve usar para autenticar.
   
   * Entrar como um *entidade de serviço*
    
     Antes de entrar no, [criar uma entidade de serviço por meio do portal do Azure](azure-stack-create-service-principals.md) ou a CLI e atribuí-lo uma função. Agora, entre usando o comando a seguir:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Testar a conectividade

Com tudo configurado, usar a CLI para criar recursos no Azure Stack. Por exemplo, você pode criar um grupo de recursos para um aplicativo e adicionar uma máquina virtual. Use o comando a seguir para criar um grupo de recursos chamado "MyResourceGroup":

```azurecli
    az group create -n MyResourceGroup -l local
```

Se o grupo de recursos é criado com êxito, o comando anterior gera as seguintes propriedades do recurso recém-criado:

![Criar um grupo de recursos](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

Esta seção explica como configurar a CLI se você estiver usando o Active Directory Federated Services (AD FS) como seu serviço de gerenciamento e está usando a CLI em um computador Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar no certificado de raiz da autoridade de certificação do Azure Stack

Se você estiver usando o ASDK, você precisará confiar no certificado de raiz da autoridade de certificação no computador remoto. Você não precisará fazer isso com os sistemas integrados.

Confie no certificado de raiz de autoridade de certificação do Azure Stack anexando-o para o certificado existente do Python.

1. Localize o certificado em seu computador. O local pode variar, dependendo de onde você instalou o Python. Você precisará ter o pip e o certifi [módulo instalado](#install-python-on-linux). Você pode usar o seguinte comando do Python a partir do prompt do bash:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Anote o local do certificado. Por exemplo, `~/lib/python3.5/site-packages/certifi/cacert.pem`. O caminho específico depende do seu sistema operacional e a versão do Python que você instalou.

2. Execute o seguinte comando do bash com o caminho para o seu certificado.

   - Para um computador Linux remoto:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Para uma máquina Linux dentro do ambiente do Azure Stack:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

Use as seguintes etapas para se conectar ao Azure Stack:

1. Registre-se o seu ambiente de pilha do Azure executando o `az cloud register` comando. Em alguns cenários, a conectividade de internet de saída direta é roteada por meio de um proxy ou firewall, que impõe a interceptação de SSL. Nesses casos, o `az cloud register` comando pode falhar com um erro como "Não é possível obter pontos de extremidade da nuvem". Para contornar esse erro, você pode definir as seguintes variáveis de ambiente:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Registre o seu ambiente. Use os seguintes parâmetros ao executar `az cloud register`.

    | Valor | Exemplo | DESCRIÇÃO |
    | --- | --- | --- |
    | Nome do ambiente | AzureStackUser | Use `AzureStackUser` para o ambiente do usuário. Se você for operador, especifique `AzureStackAdmin`. |
    | Ponto de extremidade do Gerenciador de recursos | https://management.local.azurestack.external | O **ResourceManagerUrl** no Azure Stack desenvolvimento ASDK (Kit) é: `https://management.local.azurestack.external/` O **ResourceManagerUrl** em sistemas integrados é: `https://management.<region>.<fqdn>/` Para recuperar os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Se você tiver uma pergunta sobre o ponto de extremidade do sistema integrado, entre em contato com seu operador de nuvem. |
    | Ponto de extremidade de armazenamento | local.azurestack.external | `local.azurestack.external` é para o ASDK. Para um sistema integrado, você desejará usar um ponto de extremidade para o seu sistema.  |
    | Sufixo Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` é para o ASDK. Para um sistema integrado, você desejará usar um ponto de extremidade para o seu sistema.  |
    | Imagem de VM alias doc ponto de extremidade; | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI do documento que contém os aliases de imagem de máquina virtual. Para obter mais informações, consulte [# # # configurar o ponto de extremidade de aliases de máquina virtual](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Defina o ambiente do Active Directory. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Atualize a configuração do seu ambiente para usar o perfil de versão de API específico do Azure Stack. Para atualizar a configuração, execute o seguinte comando:

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Se você estiver executando uma versão do Azure Stack antes da compilação 1808, você deve usar o perfil da versão de API **2017-03-09-profile** em vez do perfil da versão de API **2018-03-01-hybrid**. Você precisará estar usando uma versão recente da CLI do Azure.

5. Entre seu ambiente do Azure Stack usando o `az login` comando. Você pode entrar no ambiente do Azure Stack como um usuário ou como um [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md). 

6. Entrar: 

   *  Como uma **usuário** usando um navegador da web com um código de dispositivo:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >Executando o comando fornece uma URL e um código que você deve usar para autenticar.

   * Como uma entidade de serviço:
        
     Prepare o arquivo. PEM a ser usado para logon de entidade de serviço.

      * O computador cliente em que a entidade foi criada, exportar o certificado de entidade de serviço como um pfx com a chave privada localizado em `cert:\CurrentUser\My`; o nome tem o mesmo nome que a entidade do certificado.
  
      * Converta o pfx em pem (use o utilitário OpenSSL).

     Entrar para a CLI:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>Testar a conectividade

Com tudo configurado, usar a CLI para criar recursos no Azure Stack. Por exemplo, você pode criar um grupo de recursos para um aplicativo e adicionar uma máquina virtual. Use o comando a seguir para criar um grupo de recursos chamado "MyResourceGroup":

```azurecli
  az group create -n MyResourceGroup -l local
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
