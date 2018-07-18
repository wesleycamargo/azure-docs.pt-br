---
title: Usar uma MSI de VM do Linux para acessar o Azure Cosmos DB
description: Um tutorial que orienta você durante o processo de usar uma MSI (Identidade de Serviço Gerenciado) atribuída pelo sistema em uma VM do Linux para acessar o Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: skwan
ms.openlocfilehash: b84426ad28dbc2264bd9b28fe0697a88390bb58d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304123"
---
# <a name="tutorial-use-a-linux-vm-msi-to-access-azure-cosmos-db"></a>Tutorial: Usar uma MSI de VM do Linux para acessar o Azure Cosmos DB 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


Este tutorial mostra como criar e usar uma MSI de VM do Linux. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma VM do Linux com MSI habilitada
> * Criar uma conta do BD Cosmos
> * Criar uma coleção na conta de banco do Cosmos DB
> * Conceder o acesso ao MSI para uma instância do Azure Cosmos DB
> * Recuperar o `principalID` da MSI de VM do Linux
> * Obter um token de acesso e usá-lo para chamar o Azure Resource Manager
> * Obter chaves de acesso do Azure Resource Manager para realizar chamadas do Cosmos DB

## <a name="prerequisites"></a>pré-requisitos

Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com) antes de continuar.

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Para executar os exemplos de script CLI neste tutorial, você tem duas opções:

- Usar o [Azure Cloud Shell](~/articles/cloud-shell/overview.md), seja pelo Portal do Azure ou por meio do botão **Experimentar**, localizado no canto superior direito de cada bloco de código.
- [Instale a versão mais recente da CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 ou posterior) se preferir usar um console local da CLI.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Criar uma máquina virtual do Linux em um novo grupo de recursos

Para este tutorial, vamos criar uma nova VM do Linux habilitada para MSI.

Para criar uma VM habilitado MSI:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az_login). Use uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar a VM:

   ```azurecli-interactive
   az login
   ```

2. Criar um [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para contenção e implantação de VM e seus recursos relacionados usando [az group create](/cli/azure/group/#az_group_create). Ignore esta etapa, se você já tiver o grupo de recursos que deseja usar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Crie uma VM usando [az vm create](/cli/azure/vm/#az_vm_create). O exemplo a seguir cria uma VM denominada *myVM* com um MSI, como solicitado pelo parâmetro `--assign-identity`. Os parâmetros `--admin-username` e `--admin-password` especificam o nome de usuário e a senha do usuário administrativo para a entrada na máquina virtual. Atualize esses valores como adequado ao seu ambiente: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="create-a-cosmos-db-account"></a>Criar uma conta do BD Cosmos 

Se você ainda não tiver uma conta do Cosmos DB, crie uma. Você poderá ignorar esta etapa e usar uma conta de Cosmos DB existente. 

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

## <a name="retrieve-the-principalid-of-the-linux-vms-msi"></a>Recuperar o `principalID` da MSI de VM do Linux

Para obter acesso às chaves de acesso da conta do Cosmos DB a partir do Gerenciador de Recursos na seção a seguir, você precisa recuperar o `principalID` do MSI de VM do Linux.  Certifique-se de substituir o `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` (grupo de recursos no qual sua VM reside) e os valores de parâmetro `<VM NAME>` pelos seus próprios valores.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
A resposta inclui os detalhes da MSI atribuída pelo sistema (atente para o principalID, pois ele é usado na próxima seção):

```bash  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }

```
## <a name="grant-your-linux-vm-msi-access-to-the-cosmos-db-account-access-keys"></a>Conceder acesso de MSI de VM do Linux às chaves de acesso da conta do Cosmos DB

O Cosmos DB não dá suporte nativo a autenticação do Microsoft Azure AD. No entanto, você pode usar um MSI para recuperar uma chave de acesso do Cosmos DB do Gerenciador de Recursos e usar essa chave para acessar o Cosmos DB. Nesta etapa, você concede o seu acesso de MSI às chaves para a conta do Cosmos DB.

Para conceder o acesso de identidade do MSI para a conta do Cosmos DB no Azure Resource Manager usando o Azure CLI, atualize os valores de `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, e `<COSMOS DB ACCOUNT NAME>` para o seu ambiente. Substituir `<MSI PRINCIPALID>` pela `principalId` propriedade retornada pelo comando `az resource show` em [Recuperar principalID do MSI da VM do Linux](#retrieve-the-principalID-of-the-linux-VM's-MSI).  O Cosmos DB oferece suporte a dois níveis de granularidade ao usar chaves de acesso: acesso de leitura/gravação à conta e acesso somente leitura à conta.  Atribua a função `DocumentDB Account Contributor` se você quiser obter chaves de leitura/gravação para a conta ou atribua a função `Cosmos DB Account Reader Role` se você quiser obter chaves somente leitura para a conta:

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

## <a name="get-an-access-token-using-the-linux-vms-msi-and-use-it-to-call-azure-resource-manager"></a>Obter um token de acesso usando a MSI da VM do Linux e usá-lo para chamar o Azure Resource Manager

Para o restante do tutorial, trabalhe na VM criada anteriormente.

Para concluir essas etapas, você precisará do cliente SSH. Se você estiver usando o Windows, poderá usar o cliente SSH no [Subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Se precisar de ajuda para configurar as chaves do cliente SSH, confira [Como usar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md), ou [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal do Azure, navegue até **Máquinas Virtuais**, vá para a máquina virtual do Linux e na página **Visão geral**, clique em **Conectar** na parte superior. Copie a cadeia de caracteres para conectar-se à VM. 
2. Conecte-se à VM usando um cliente SSH.  
3. Em seguida, você recebe uma solicitação para inserir sua **Senha**, adicionada durante a criação da **VM do Linux**. Assim, você deve se conectar com êxito.  
4. Use o CURL para obter um token de acesso para o Azure Resource Manager: 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
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

Agora, usaremos o CURL para fazer uma chamada ao Gerenciador de Recursos usando o token de acesso recuperado na seção anterior para recuperar a chave de acesso da conta do Cosmos DB. Assim que tivermos a chave de acesso, poderemos fazer consultas no Cosmos DB. Substitua os parâmetros `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<COSMOS DB ACCOUNT NAME>` pelos seus próprios valores. Substitua o valor `<ACCESS TOKEN>` pelo token de acesso que você recuperou anteriormente.  Se você quiser recuperar chaves de leitura/gravação, use o tipo de operação de chave `listKeys`.  Se você quiser recuperar chaves somente leitura, use o tipo de operação de chave `readonlykeys`:

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

- Para obter uma visão geral da MSI, consulte [MSI (Identidade de Serviço Gerenciado) para recursos do Azure](overview.md).

