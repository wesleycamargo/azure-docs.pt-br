---
title: "Utilizar uma MSI atribuída pelo usuário em uma VM do Linux para acessar o Armazenamento do Microsoft Azure"
description: "Um tutorial que orienta você durante o processo de usar uma MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário em uma VM do Linux para acessar o Armazenamento do Microsoft Azure."
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 91fe06825d1db586b715617241b0ca39115414c0
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Use uma MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário em uma VM do Linux para acessar o Armazenamento do Microsoft Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Este tutorial mostra como criar e usar a MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário para uma Máquina Virtual do Linux e, em seguida, usá-la para acessar o Armazenamento do Microsoft Azure. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário
> * Atribuir a MSI atribuída pelo usuário a uma máquina virtual Linux
> * Conceder o acesso ao MSI para uma instância do Armazenamento do Microsoft Azure
> * Obter um token de acesso usando a identidade da MSI atribuída pelo usuário e usá-la para acessar o Armazenamento do Microsoft Azure

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Para executar os exemplos de script CLI neste tutorial, você tem duas opções:

- Usar o [Azure Cloud Shell](~/articles/cloud-shell/overview.md), seja pelo Portal do Azure ou por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
- [Instale a versão mais recente da CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 ou posterior) se preferir usar um console local da CLI.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Criar uma máquina virtual do Linux em um novo grupo de recursos

Primeiro, crie uma nova VM do Linux. Você também poderá habilitar a MSI em uma VM existente, se preferir.

1. Clique no botão **+/Criar novo serviço** encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**.
3. Insira as informações da máquina virtual. Para **Tipo de autenticação**, selecione **Chave pública SSH** ou **Senha**. As credenciais criadas permitirão que você faça logon na máquina virtual.

    ![Texto Alt da imagem](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Escolha uma **Assinatura** para a máquina virtual na lista suspensa.
5. Para selecionar um novo **Grupo de Recursos** no qual você deseja criar a máquina virtual, escolha **Criar Novo**. Ao concluir, clique em **OK**.
6. Selecione o tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos** ou altere o filtro Tipo de disco com suporte. Na folha de configurações, mantenha os padrões e clique em **OK**.

## <a name="create-a-user-assigned-msi"></a>Criar uma MSI atribuída pelo usuário

1. Se você estiver usando o console da CLI (em vez de uma sessão do Azure Cloud Shell), comece entrando no Azure. Use uma conta que esteja associada à assinatura do Azure na qual você deseja criar a nova MSI:

    ```azurecli
    az login
    ```

2. Crie uma MSI atribuída pelo usuário usando [az identity create](/cli/azure/identity#az_identity_create). O parâmetro `-g` especifica o grupo de recursos no qual o MSI é criado, enquanto o parâmetro `-n` especifica seu nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<MSI NAME>` pelos seus próprios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    A resposta contém detalhes para a MSI criada atribuída pelo usuário, semelhante ao exemplo a seguir. Tome nota do valor `id` para a MSI, pois ele será usado na próxima etapa:

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Atribuir a MSI atribuída pelo usuário à VM do Linux

Ao contrário de uma MSI atribuída pelo sistema, uma MSI atribuída pelo usuário pode ser usada pelos clientes em vários recursos do Azure. Para este tutorial, você a atribui a uma única VM. Você também pode atribuí-la a mais de uma VM.

Atribuir a MSI atribuída pelo usuário à VM do Linux usando [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. Use a propriedade `id` retornada na etapa anterior para o valor do parâmetro `--identities`:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento 

Se você ainda não tiver uma conta de armazenamento, crie uma agora. Você também poderá ignorar esta etapa e usar uma conta de armazenamento existente, se preferir. 

1. Clique no botão **+/Criar novo serviço** encontrado no canto superior esquerdo do portal do Azure.
2. Clique em **Armazenamento**, em **Conta de Armazenamento** e um novo painel “Criar conta de armazenamento” será exibido.
3. Insira um **Nome** para a conta de armazenamento, que você usará mais tarde.  
4. **Modelo de implantação** e **Tipo de conta** devem ser definidos como "Resource manager" e "Finalidade geral", respectivamente. 
5. Verifique se a **Assinatura** e o **Grupo de Recursos** correspondem ao que você especificou quando criou sua VM na etapa anterior.
6. Clique em **Criar**.

    ![Criar nova conta de armazenamento](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Criar um contêiner de blobs na conta de armazenamento

Como arquivos exigem armazenamento de blobs, é preciso criar um contêiner de blobs para armazenar o arquivo. Em seguida, você carrega e baixa um arquivo para o contêiner de blobs na nova conta de armazenamento.

1. Navegue de volta para sua conta de armazenamento criado recentemente.
2. Clique no link **Contêineres** à esquerda, em "Serviço Blob".
3. Clique em **+ Contêiner** na parte superior da página e um painel "Novo contêiner" deslizará para fora.
4. Dê um nome ao contêiner, selecione um nível de acesso, então clique em **OK**. O nome especificado também será usado posteriormente no tutorial. 

    ![Criar um contêiner de armazenamento](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Carregue um arquivo para o contêiner recém-criado clicando no nome do contêiner, em **Carregar**, selecione um arquivo e clique em **Carregar**.

    ![Carregar arquivo de texto](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Conceda o acesso à MSI atribuída pelo usuário para um contêiner do Armazenamento do Microsoft Azure

Usando uma MSI, seu código pode obter tokens de acesso para autenticar para recursos compatíveis com a autenticação do Microsoft Azure AD. Neste tutorial, você usará o Armazenamento do Microsoft Azure.

Primeiro, você concederá o acesso de identidade da MSI a um contêiner do Armazenamento do Microsoft Azure. Nesse caso, você pode usar o contêiner criado anteriormente. Atualize os valores de `<MSI PRINCIPALID>`, `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>` e `<CONTAINER NAME>` conforme apropriado para seu ambiente. Substitua `<CLIENT ID>` pela propriedade `clientId` retornada pelo comando `az identity create` em [Criar uma MSI atribuída pelo usuário](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role ‘Reader’ --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

A resposta inclui os detalhes da atribuição de função criada:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.storage/storageAccounts/<STORAGE ACCOUNT NAME>/blogServices/default/<CONTAINER NAME>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Obter um token de acesso usando a identidade da MSI atribuída pelo usuário e usá-la para chamar o Armazenamento do Microsoft Azure

Para o restante do tutorial, será necessário trabalhar da VM criada anteriormente.

Para concluir essas etapas, você precisará do cliente SSH. Se você estiver usando o Windows, poderá usar o cliente SSH no [Subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Se precisar de ajuda para configurar as chaves do cliente SSH, confira [Como usar chaves SSH com o Windows no Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), ou [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal do Azure, navegue até **Máquinas Virtuais**, vá para a máquina virtual do Linux e na página **Visão geral**, clique em **Conectar** na parte superior. Copie a cadeia de caracteres para conectar-se à VM.
2. **Conecte-se** à VM com um cliente SSH de sua escolha. 
3. Na janela de terminal, usando CURL, faça uma solicitação para o ponto de extremidade da MSI local para obter um token de acesso para o Armazenamento do Microsoft Azure.

   A solicitação CURL para adquirir um token de acesso é mostrada no exemplo a seguir. Substitua `<CLIENT ID>` pela propriedade `clientId` retornada pelo comando `az identity create` em [Criar uma MSI atribuída pelo usuário](#create-a-user-assigned-msi):

   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fstorage.azure.com/&client_id=<CLIENT ID>"
   ```

   > [!NOTE]
   > Na solicitação anterior, o valor do parâmetro `resource` deve ser uma correspondência exata do que é esperado pelo Microsoft Azure AD. Ao usar a ID de recurso do Armazenamento do Microsoft Azure, você precisa incluir a barra à direita no URI.
   > Na resposta seguinte, o elemento access_token foi reduzido para fins de brevidade.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. Agora, use o token de acesso para acessar o Armazenamento do Microsoft Azure, por exemplo, para ler o conteúdo do arquivo de exemplo que você carregou anteriormente para o contêiner. Substitua os valores `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` e `<FILE NAME>` pelos valores que você especificou anteriormente e `<ACCESS TOKEN>` pelo token retornado na etapa anterior.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME>?api-version=2017-11-09 -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   A resposta tem o conteúdo do arquivo:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](msi-overview.md).
- Para saber como executar este mesmo tutorial usando uma credencial SAS de armazenamento, consulte [Usar uma Identidade de Serviço Gerenciado da VM do Linux para acessar o Armazenamento do Azure por meio de uma credencial SAS](msi-tutorial-linux-vm-access-storage-sas.md)
- Para obter mais informações sobre o recurso SAS da conta de Armazenamento do Azure, consulte:
  - [Como usar SAS (assinaturas de acesso compartilhado)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Constructing a Service SAS (Criação de uma SAS de serviço)](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.





