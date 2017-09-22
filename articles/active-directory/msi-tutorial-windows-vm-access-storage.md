---
title: Usar um MSI de VM do Windows para acessar o armazenamento do Azure
description: "Um tutorial que orienta o processo de usar uma Identidade de Serviço Gerenciada (MSI) de VM do Windows para acessar o armazenamento do Azure."
services: active-directory
documentationcenter: 
author: elkuzmen
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 86d88e3d44f442171f69d0baea5e7d689b963277
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---

# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-storage"></a>Usar o MSI (Identidade de Serviço Gerenciada) da VM do Windows para acessar o armazenamento do Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como habilitar o MSI (Identidade de Serviço Gerenciada) para uma Máquina Virtual do Linux e, em seguida, usar essa identidade para acessar as Chaves de Armazenamento. Use as Chaves de Armazenamento normalmente ao realizar operações de armazenamento, por exemplo, ao usar o SDK de Armazenamento. Para este tutorial, vamos carregar e baixar blobs usando a CLI do Azure. Você saberá como:


> [!div class="checklist"]
> * Habilitar o MSI em uma Máquina Virtual do Windows 
> * Criar uma nova conta de armazenamento
> * Conceder acesso ao armazenamento à sua VM 
> * Obter um token de acesso para sua conta de armazenamento usando a identidade da VM 


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Criar uma máquina virtual do Windows em um novo grupo de recursos

Para este tutorial, vamos criar uma nova VM do Windows. Você também pode habilitar o MSI em uma VM existente.

1.  Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.
2.  Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. 
3.  Insira as informações da máquina virtual. O **Nome de usuário** e **Senha** criados aqui são as credenciais usadas para fazer logon na máquina virtual.
4.  Escolha uma **Assinatura** para a máquina virtual na lista suspensa.
5.  Para selecionar um novo **Grupo de recursos** no qual você deseja criar a máquina virtual, escolha **Criar novo**. Ao concluir, clique em **OK**.
6.  Selecione o tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos os** ou altere o filtro **Tipo de disco com suporte**. Na folha de configurações, mantenha os padrões e clique em **OK**.

    ![Texto Alt da imagem](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Habilitar o MSI na sua VM

Um MSI de máquina virtual permite obter tokens de acesso do Azure AD sem a necessidade de colocar as credenciais no seu código. Nos bastidores, habilitar o MSI faz duas coisas: instala a extensão de VM do MSI em sua VM, e isso habilita o MSI para a máquina virtual.  

1. Selecione a **Máquina virtual** na qual você deseja habilitar o MSI.
2. Na barra de navegação à esquerda, clique em **Configuração**.
3. Você verá **Identidade de Serviço Gerenciado**. Para registrar e habilitar o MSI, selecione **Sim**; se você deseja desabilitá-la, escolha Não.
4. Lembre-se de clicar em **Salvar** para salvar a configuração.

    ![Texto Alt da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se você deseja verificar quais extensões estão nesta **VM do Windows**, clique em **Extensões**. Se o MSI estiver habilitado, **ManagedIdentityExtensionforWindows** aparecerá na lista.

    ![Texto Alt da imagem](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento 

Use as chaves de armazenamento normalmente durante operações de armazenamento. Neste exemplo, nos concentraremos em carregar e baixar blobs usando a CLI do Azure. 

1. Navegue até o barra lateral e selecione **Armazenamento**.  
2. Crie uma nova **Conta de Armazenamento**.  
3. Em **Modelo de implantação**, insira **Resource Manager** e em **Tipo de conta** insira **Uso Geral**.  
4. Verifique se **Assinatura** e **Grupo de Recursos** são os que você usou quando criou sua **Máquina Virtual do Linux** na etapa anterior.

    ![Texto Alt da imagem](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>Conceder o acesso para uso das Chaves de Armazenamento à identidade de sua VM 

Usando o MSI seu código pode obter tokens de acesso para autenticar para recursos que oferecem suporte à autenticação do Azure AD.   

1. Navegue até a guia para **Armazenamento**.  
2. Selecione a **Conta de Armazenamento** específica criada anteriormente.   
3. Acesse **Controle de acesso (IAM)** no painel esquerdo.  
4. Em seguida, **Adicione** uma nova atribuição de função à sua VM, escolha **Função** como **Função de Serviço de Operador da Chave da Conta de Armazenamento**.  
5. No menu suspenso seguinte, **Atribuir acesso a** ao recurso **Máquina Virtual**.  
6. Em seguida, certifique-se de que a assinatura correta está listada na lista suspensa **Assinatura**. E para **Grupo de Recursos**, selecione **Todos os grupos de recursos**.  
7. Por fim, em **Selecionar** escolha sua Máquina Virtual do Windows na lista suspensa e clique em **Salvar**. 

    ![Texto Alt da imagem](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Obter um token de acesso usando a identidade da máquina virtual e usá-la para chamar o Azure Resource Manager 

Você precisará usar o **PowerShell** nesta parte.  Se ele não estiver instalado, baixe-o [aqui](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1. No portal, navegue até **Máquinas virtuais** e vá para a máquina virtual do Windows e em **Visão geral**, clique em **Conectar**. 
2. Insira o seu **Nome de usuário** e **Senha** que você adicionou quando criou a VM do Windows. 
3. Agora que você criou uma **Conexão de Área de Trabalho Remota** com a máquina virtual, abra o **PowerShell** na sessão remota. 
4. Usando Invoke-WebRequest do Powershell, faça uma solicitação ao ponto de extremidade do MSI local para obter um token de acesso para o Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost/50342/oauth2/token -Method GET -Body @resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > O valor do parâmetro "resource" deve ser uma correspondência exata para o que é esperado pelo Azure AD. Ao usar a ID de recurso do Azure Resource Manager, você deve incluir a barra à direita no URI.
    
    Em seguida, extraia a resposta completa, que é armazenada como uma cadeia de caracteres JSON (JavaScript Object Notation) formatada no objeto $response. 
    
    ```powershell
    $content = $repsonse.Content | ConvertFrom-Json
    ```
    Em seguida, extraia o token de acesso da resposta.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Por fim, chame o Azure Resource Manager usando o token de acesso. Neste exemplo, também estamos usando Invoke-WebRequest do PowerShell para fazer a chamada para o Azure Resource Manager e incluir o token de acesso no cabeçalho de autorização.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE]
    > A URL diferencia maiúsculas de minúsculas, portanto, verifique se você está usando letras maiúsculas e minúsculas exatamente da mesma forma que você usou anteriormente ao nomear o Grupo de Recursos e a letra maiúscula "G" em "resourceGroup".
    
## <a name="get-the-storage-keys-from-azure-resource-manager"></a>Obter as Chaves de Armazenamento do Azure Resource Manager 

```powershell
PS C:\> $keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/97f51385-2edc-4b69-bed8-7778dd4cb761/resourceGroups/SKwan_Test/providers/Microsoft.Storage/storageAccounts/skwanteststorage/listKeys/?api-version=2016-12-01 -Method POST$ -Headers @{Authorization="Bearer $ARMToken"}
```

```powershell
PS C:\> $keysContent = $keysResponse.Content | ConvertFrom-Json
```

```powershell
PS C:\> $key = $keysContent.keys[0].value
```

**Criar um arquivo a ser carregados usando a CLI do Azure**

```bash
echo "This is a test text file." > test.txt
```

**Carregar o arquivo usando a CLI do Azure e autenticando com a Chave de Armazenamento**

> [!NOTE]
> Primeiro, lembre-se de instalar os cmdlets de armazenamento do Azure "Install-Module Azure.Storage". 

Solicitação do PowerShell:


```powershell
PS C:\> $ctx = New-AzureStorageContext -StorageAccountName skwanteststorage -StorageAccountKey $key
PS C:\> Set-AzureStorageBlobContent -File test.txt -Container testcontainer -Blob testblob -Context $ctx
```

Resposta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

**Baixar o arquivo usando a CLI do Azure e autenticando com a Chave de Armazenamento**

Solicitação do PowerShell:

```powershell
PS C:\> Get-AzureStorageBlobContent -Blob <blob name> -Container <container name> -Destination <file> -Context $ctx
```

Resposta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```





