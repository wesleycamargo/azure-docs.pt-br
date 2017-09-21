---
title: Usar um MSI de VM do Linux para acessar o armazenamento do Azure
description: "Um tutorial que orienta o processo de usar uma Identidade de Serviço Gerenciada (MSI) de VM do Linux para acessar o armazenamento do Azure."
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
ms.openlocfilehash: 9fec51528d1203a7107558408ced42fa2b2b884a
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---


# <a name="use-managed-service-identity-msi-with-a-linux-vm-to-access-storage-credentials"></a>Usar o MSI (Identidade de Serviço Gerenciada) com uma VM do Linux para acessar as credenciais de armazenamento

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como habilitar o MSI (Identidade de Serviço Gerenciada) para uma Máquina Virtual do Linux e, em seguida, usar essa identidade para acessar as Chaves de Armazenamento. Use as Chaves de Armazenamento normalmente ao realizar operações de armazenamento, por exemplo, ao usar o SDK de Armazenamento. Para este tutorial, vamos carregar e baixar blobs usando a CLI do Azure. Você saberá como:


> [!div class="checklist"]
> * Habilitar MSI em uma Máquina Virtual do Linux 
> * Criar uma nova conta de armazenamento
> * Conceder acesso ao armazenamento à sua VM
> * Obter um token de acesso para sua conta de armazenamento usando a identidade da VM para acessar Chaves de Armazenamento 


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Criar uma Máquina Virtual do Linux em um novo Grupo de Recursos

Para este tutorial, vamos criar uma nova VM do Linux. Você também pode habilitar o MSI em uma VM existente.

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**.
3. Insira as informações da máquina virtual. Para **Tipo de autenticação**, selecione **Chave pública SSH** ou **Senha**. As credenciais criadas permitirão que você faça logon na máquina virtual.

    ![Texto Alt da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Escolha uma **Assinatura** para a máquina virtual na lista suspensa.
5. Para selecionar um novo **Grupo de recursos** no qual você deseja criar a máquina virtual, escolha **Criar novo**. Ao concluir, clique em **OK**.
6. Selecione o tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos** ou altere o filtro Tipo de disco com suporte. Na folha de configurações, mantenha os padrões e clique em **OK**.

## <a name="enable-msi-on-your-vm"></a>Habilitar o MSI na sua VM

Um MSI de máquina virtual permite obter tokens de acesso do Azure AD sem a necessidade de colocar as credenciais no seu código. Nos bastidores, habilitar o MSI faz duas coisas: instala a extensão de VM do MSI em sua VM, e isso permite MSI para a VM.  

1. Selecione a **Máquina virtual** na qual você deseja habilitar o MSI.
2. Na barra de navegação à esquerda, clique em **Configuração**.
3. Você verá **Identidade de Serviço Gerenciado**. Para registrar e habilitar o MSI, selecione **Sim**; se você deseja desabilitá-la, escolha Não.
4. Lembre-se de clicar em **Salvar** para salvar a configuração.

    ![Texto Alt da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se você deseja verificar quais extensões estão nesta **VM do Linux**, clique em **Extensões**. Se o MSI estiver habilitado, **ManagedIdentityExtensionforLinux** aparecerá na lista.

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
7. Por fim, em **Selecionar** escolha sua Máquina Virtual do Linux na lista suspensa e clique em **Salvar**. 
    ![Texto Alt da imagem](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Obter um token de acesso usando a identidade da máquina virtual e usá-la para chamar o Azure Resource Manager

Você precisará usar o terminal Bash, escolher e baixar sua distribuição Linux [aqui](https://msdn.microsoft.com/commandline/wsl/install_guide).


1. No portal, navegue até a VM do Linux e em **Visão geral**, clique em **Conectar**. Você receberá uma solicitação para usar o Bash, anote seu SSH e IP de VM no alerta. 
2. Abra e conecte-se ao Bash.  
3. No terminal, insira seu **SSH** e a **VM** com a qual você deseja se conectar, por exemplo, "ssh admin@12.61.219.35".  
4. Em seguida, você receberá uma solicitação para inserir sua **Senha**, adicionada durante a criação da **VM do Linux**. Assim, você deve se conectar com êxito.  
5. Em seguida, faça uma solicitação usando CURL para obter o token de autorização para a VM do Linux à qual você está conectado. O ponto de extremidade **Azure Resource Manager** é https://management.azure.com.  

    A solicitação CURL para o token de acesso está abaixo:
    
    ```bash
    curl --data "authority= https://login.microsoftonline.com/<TENANT ID>&&resource=https://management.azure.com/"  -H Metadata:true http://localhost:50432/oauth2/token   
    ```
    
    > [!NOTE]
    > Verifique se a URL do recurso para o qual você está tentando solicitar acesso contém a formatação correta com uma barra no final, como 'https:<RESOURCE>/'
    
    ```powershell
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEP"}
     ```
    
## <a name="the-curl-request-to-get-storage-keys-from-azure-resource-manager"></a>A solicitação CURL para obter as Chaves de Armazenamento do Azure Resource Manager  

> [!NOTE]
> O texto na URL diferencia maiúsculas de minúsculas, portanto, verifique se você está usando letras maiúsculas e minúsculas para os Grupos de Recursos refletirem adequadamente. Além disso, é importante saber que se trata de uma solicitação POST, não uma solicitação GET, e passe um valor para capturar um limite de comprimento com -d que pode ser NULL.  

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –request POST -d"" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

A resposta de CURL fornece a lista de chaves:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNtXcluBiFSV2Fs+JTj6c+VK5wWB4RWUVYw2Y5BkRQu21cRpAztt3S19bAowhGPqJrAfMr3gn89Ui9VPbtA=="},{"keyName":"key2","permissions":"Full","value":"U+uI0B1Iygw1bDK6Ic1VWKMMY77ac0VV3kqYaCW7FcUFiuw4UkLfEKbnF2sZpIfFR9zUWniLBhlt+eUn/TeSJg=="}]} 
```


Crie um arquivo para ser carregado; esse será um arquivo de blob de exemplo que você pode carregar com as Chaves de Armazenamento em sua conta de armazenamento dentro do contêiner criado. 

Em uma VM do Linux, você pode fazer isso com o seguinte comando. 

```bash
echo "This is a test file." > test.txt
```
 Em seguida, carregue o arquivo usando a CLI do Azure e autentique com a Chave de Armazenamento.
 

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name 
                        [--account-name] 
                        [--account-key] 
```

Resposta: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

além disso, baixe o arquivo usando a CLI do Azure e autentique com a Chave de Armazenamento. 

Solicitação: 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         [--account-name]
                         [--account-key]  
```

Resposta: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-12T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="related-content"></a>Conteúdo relacionado

Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](../active-directory/msi-overview.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.


