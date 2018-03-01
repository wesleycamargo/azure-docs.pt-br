---
title: "Use uma MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário em uma VM do Linux para acessar o Azure Cosmos DB"
description: "Um tutorial que orienta você durante o processo de usar uma MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário em uma VM do Linux para acessar o Azure Cosmos DB."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2018
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2c0c3597999e80af86f079385653d94ddfcab245
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-cosmos-db"></a>Use uma MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário em uma VM do Linux para acessar o Azure Cosmos DB 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Este tutorial mostra como criar e usar a MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário para uma Máquina Virtual do Linux e, em seguida, usá-la para acessar o Azure Cosmos DB. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário
> * Atribuir a MSI atribuída pelo usuário a uma máquina virtual Linux
> * Conceder o acesso ao MSI para uma instância do Azure Cosmos DB
> * Obter um token de acesso usando a identidade da MSI atribuída pelo usuário e usá-la para acessar o Azure Cosmos DB

## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Para executar os exemplos de script CLI neste tutorial, você tem duas opções:

- Usar o [Azure Cloud Shell](~/articles/cloud-shell/overview.md), seja pelo Portal do Azure ou por meio do botão **Experimentar**, localizado no canto superior direito de cada bloco de código.
- [Instale a versão mais recente da CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 ou posterior) se preferir usar um console local da CLI.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Criar uma máquina virtual do Linux em um novo grupo de recursos

Para este tutorial, vamos criar uma nova VM do Linux. Você também pode habilitar o MSI em uma VM existente.

1. Clique em **+ Criar um recurso** encontrado na parte superior esquerda do portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **VM de Ubuntu Server 16.04 LTS**.
3. Insira as informações da máquina virtual. Para **Tipo de autenticação**, selecione **Chave pública SSH** ou **Senha**. As credenciais criadas permitirão que você faça logon na máquina virtual.

    ![VM de MSI Linux](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Escolha uma **Assinatura** para a máquina virtual na lista suspensa.
5. Em **Grupo de Recursos**, escolha **Criar novo** e digite o nome do grupo de recursos dessa VM. Ao concluir, clique em **OK**.
6. Selecione o tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos** ou altere o filtro Tipo de disco com suporte. Na folha **Configurações**, mantenha os padrões e clique em **OK**.

## <a name="create-a-user-assigned-msi"></a>Criar uma MSI atribuída pelo usuário

1. Se você estiver usando o console da CLI (em vez de uma sessão do Azure Cloud Shell), comece entrando no Azure. Use uma conta que esteja associada à assinatura do Azure na qual você deseja criar a nova MSI:

    ```azurecli
    az login
    ```

2. Crie uma MSI atribuída pelo usuário usando [az identity create](/cli/azure/identity#az_identity_create). O parâmetro `-g` especifica o grupo de recursos no qual o MSI é criado, enquanto o parâmetro `-n` especifica seu nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<MSI NAME>` pelos seus próprios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    A resposta contém os detalhes para o MSI atribuído ao usuário criado, semelhante ao exemplo a seguir (observe os valores `clientId` e `id` de seu MSI, pois serão usados em etapas posteriores):

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
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

## <a name="create-a-cosmos-db-account"></a>Criar uma conta do BD Cosmos 

Se você ainda não tiver uma conta de Cosmos DB, crie uma agora. Você também poderá ignorar esta etapa e usar uma conta de Cosmos DB existente, se preferir. 

1. Clique no botão **+/Criar novo serviço** encontrado no canto superior esquerdo do portal do Azure.
2. Clique em **Bancos de dados** e, em seguida, **Azure Cosmos DB** e um novo painel "Nova conta" será exibido.
3. Insira uma **ID** para a conta do Azure Cosmos DB, a qual será usada posteriormente.  
4. A **API** deve ser definida como “SQL”. A abordagem descrita neste tutorial pode ser usada com outros tipos de API disponíveis, mas as etapas neste tutorial são para a API do SQL.
5. Verifique se a **Assinatura** e o **Grupo de Recursos** correspondem ao que você especificou quando criou sua VM na etapa anterior.  Selecione um **Local** no qual o Cosmos DB está disponível.
6. Clique em **Criar**.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Criar uma coleção na conta de banco do Cosmos DB

Em seguida, adicione uma coleção de dados na conta do Cosmos DB que você possa consultar em etapas posteriores.

1. Navegue até sua conta do Cosmos DB recém-criado.
2. Na guia **Visão geral**, clique no botão **+/Adicionar coleção** e um painel "Adicionar coleção" surgirá.
3. Dê à coleção uma ID de banco de dados, ID de coleção, selecione uma capacidade de armazenamento, insira uma chave de partição, digite um valor de taxa de transferência e clique em **OK**.  Para este tutorial, é suficiente usar "Teste" como ID do banco de dados e a ID da coleção, selecionar uma capacidade de armazenamento fixa e a menor taxa de transferência (400 RU/s).

## <a name="grant-your-user-assigned-msi-access-to-the-cosmos-db-account-access-keys"></a>Conceder acesso de MSI Atribuída pelo Usuário às chaves de acesso da conta do Cosmos DB

O Cosmos DB não dá suporte nativo a autenticação do Microsoft Azure AD.  No entanto, você pode usar um MSI para recuperar uma chave de acesso do Cosmos DB do Gerenciador de Recursos e usar essa chave para acessar o Cosmos DB.  Nesta etapa, você concede acesso de MSI atribuída pelo usuário às chaves para a conta do Cosmos DB.

Primeiro, conceda o acesso de identidade do MSI para a conta do Cosmos DB no Azure Resource Manager usando a CLI do Azure. Atualize os valores de `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<COSMOS DB ACCOUNT NAME>` para seu ambiente. Substitua `<MSI PRINCIPALID>` pela propriedade `principalId` retornada pelo comando `az identity create` em [Criar uma MSI atribuída pelo usuário](#create-a-user-assigned-msi).  O Cosmos DB oferece suporte a dois níveis de granularidade ao usar chaves de acesso: acesso de leitura/gravação à conta e acesso somente leitura à conta.  Atribua a função `DocumentDB Account Contributor` se você quiser obter chaves de leitura/gravação para a conta ou atribua a função `Cosmos DB Account Reader Role` se você quiser obter chaves somente leitura para a conta:

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

A resposta inclui os detalhes da atribuição de função criada:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msi-and-use-it-to-call-azure-resource-manager"></a>Obter um token de acesso usando a MSI Atribuída pelo Usuário e usá-la para chamar o Azure Resource Manager

Para o restante do tutorial, trabalhe na VM criada anteriormente.

Para concluir essas etapas, você precisará do cliente SSH. Se você estiver usando o Windows, poderá usar o cliente SSH no [Subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Se precisar de ajuda para configurar as chaves do cliente SSH, confira [Como usar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md), ou [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal do Azure, navegue até **Máquinas Virtuais**, vá para a máquina virtual do Linux e na página **Visão geral**, clique em **Conectar** na parte superior. Copie a cadeia de caracteres para conectar-se à VM. 
2. Conecte-se à VM usando um cliente SSH.  
3. Em seguida, você recebe uma solicitação para inserir sua **Senha**, adicionada durante a criação da **VM do Linux**. Assim, você deve se conectar com êxito.  
4. Use o CURL para obter um token de acesso para o Azure Resource Manager.  

    A solicitação CURL e a resposta para o token de acesso está abaixo.  Substitua <CLIENT ID> pelo valor de clientId da MSI atribuída pelo usuário:
    
    ```bash
    curl 'http://localhost:50342/oauth2/token?resource=https://management.azure.com/&client_id=<CLIENT ID>' -H "Metadata:true"
    ```
    
    > [!NOTE]
    > Na solicitação anterior, o valor do parâmetro "resource" deve ser uma correspondência exata do que é esperado pelo Azure AD. Ao usar a ID de recurso do Azure Resource Manager, você deve incluir a barra à direita no URI.
    > Na resposta seguinte, o elemento access_token foi reduzido para fins de brevidade.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Obter chaves de acesso do Azure Resource Manager para realizar chamadas do Cosmos DB  

Agora, usaremos CURL para fazer uma chamada ao Gerenciador de Recursos usando o token de acesso recuperado na seção anterior para recuperar a chave de acesso da conta do Cosmos DB. Assim que tivermos a chave de acesso, poderemos fazer consultas no Cosmos DB. Substitua os parâmetros `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<COSMOS DB ACCOUNT NAME>` pelos seus próprios valores. Substitua o valor `<ACCESS TOKEN>` pelo token de acesso que você recuperou anteriormente.  Se você quiser recuperar chaves de leitura/gravação, use o tipo de operação de chave `listKeys`.  Se você quiser recuperar chaves somente leitura, use o tipo de operação de chave `readonlykeys`:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> O texto na URL anterior diferencia maiúsculas de minúsculas, portanto, verifique se você está usando letras maiúsculas e minúsculas para os Grupos de Recursos refletirem-nas adequadamente. Além disso, é importante saber que se trata de uma solicitação POST, não uma solicitação GET, e passe um valor para capturar um limite de comprimento com -d que pode ser NULL.  

A resposta de CURL fornece a lista de chaves.  Por exemplo, se você receber as chaves somente leitura:  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

Agora que você tem a chave de acesso para a conta do Cosmos DB, pode passá-la para um SDK do Cosmos DB e fazer chamadas para acessar a conta.  Como exemplo rápido, você pode passar a chave de acesso para a CLI do Azure.  Você pode obter o <COSMOS DB CONNECTION URL> na guia **Visão geral** da folha da conta do Cosmos DB no portal do Azure.  Substitua o <ACCESS KEY> pelo valor obtido acima:

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Esse comando de CLI retorna detalhes sobre a coleção:

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral da MSI, consulte [MSI (Identidade de Serviço Gerenciado) para recursos do Azure](msi-overview.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.