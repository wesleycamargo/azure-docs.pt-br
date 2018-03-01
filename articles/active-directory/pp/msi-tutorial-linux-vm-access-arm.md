---
title: "Use a MSI atribuída pelo usuário de VM do Linux para acessar o Azure Resource Manager"
description: "Um tutorial que orienta você durante o processo de uso de uma MSI (Identidade do Serviço Gerenciado) atribuída pelo usuário em uma VM do Linux para acessar o Azure Resource Manager."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7013cb48738d4dccb328728fe1e47b3f43bff4f6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-resource-manager"></a>Usar uma MSI (Identidade do Serviço Gerenciado) atribuída pelo usuário em uma VM do Linux para acessar o Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Este tutorial explica como habilitar a MSI (Identidade do Serviço Gerenciado), atribuí-la a uma VM (Máquina Virtual) do Linux e, em seguida, usar essa identidade para acessar a API do Azure Resource Manager. 

Identidade do Serviço Gerenciado são gerenciadas automaticamente pelo Azure. Elas permitem que a autenticação para serviços compatíveis com a autenticação do Azure AD, sem a necessidade de inserir as credenciais em seu código.

Você aprenderá como:

> [!div class="checklist"]
> * Criar uma MSI atribuída pelo usuário
> * Atribuir o MSI para uma VM do Linux 
> * Conceder o acesso por MSI a um Grupo de Recursos no Azure Resource Manager 
> * Obter um token de acesso usando a MSI e usá-la para chamar o Azure Resource Manager 

## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Para executar os exemplos de script CLI neste tutorial, você tem duas opções:

- Usar o [Azure Cloud Shell](~/articles/cloud-shell/overview.md), seja pelo Portal do Azure ou por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
- [Instale a versão mais recente da CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 ou posterior) se preferir usar um console local da CLI.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Criar uma Máquina Virtual do Linux em um novo Grupo de Recursos

Para este tutorial, você primeiro cria uma nova VM do Linux. Você também pode usar uma VM existente.

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**.
3. Insira as informações da máquina virtual. Para **Tipo de autenticação**, selecione **Chave pública SSH** ou **Senha**. As credenciais criadas permitirão que você faça logon na máquina virtual.

    ![Criar VM do Linux](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

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
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
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

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Conceder o acesso à MSI atribuída pelo usuário a um grupo de recursos no Azure Resource Manager 

A MSI fornece a seu código um token de acesso para autenticação junto a recursos compatíveis com a autenticação do Azure AD. Neste tutorial, seu código acessa a API do Azure Resource Manager. 

No entanto, antes de ele poder fazer isso, você precisa conceder acesso à identidade da MSI a um recurso no Azure Resource Manager. Nesse caso, o grupo de recursos no qual a VM está contida. Atualize os valores de `<SUBSCRIPTION ID>` e `<RESOURCE GROUP>` conforme apropriado para seu ambiente. Além disso, substitua `<MSI PRINCIPALID>` pela propriedade `principalId` retornada pelo comando `az identity create` em [Criar uma MSI atribuída pelo usuário](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

A resposta contém detalhes para a atribuição de função criada, semelhante ao exemplo a seguir:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Obter um token de acesso utilizando a identidade da VM e usá-la para chamar o Resource Manager 

Para o restante do tutorial, trabalharemos na VM criada anteriormente.

Para concluir essas etapas, você precisará do cliente SSH. Se você estiver usando o Windows, poderá usar o cliente SSH no [Subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Se precisar de ajuda para configurar as chaves do cliente SSH, confira [Como usar chaves SSH com o Windows no Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), ou [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal do Azure, navegue até **Máquinas Virtuais**, vá para a máquina virtual do Linux e na página **Visão geral**, clique em **Conectar** na parte superior. Copie a cadeia de caracteres para conectar-se à VM.
2. **Conecte-se** à VM com um cliente SSH de sua escolha.  
3. Na janela de terminal, usando a CURL, faça uma solicitação para o ponto de extremidade do MSI local para obter um token de acesso para o Azure Resource Manager.  

   A solicitação CURL para adquirir um token de acesso é mostrada no exemplo a seguir. Substitua `<CLIENT ID>` pela propriedade `clientId` retornada pelo comando `az identity create` em [Criar uma MSI atribuída pelo usuário](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<CLIENT ID>"   
   ```
    
    > [!NOTE]
    > O valor do parâmetro `resource` deve ser uma correspondência exata para o que é esperado pelo Azure AD. Ao usar a ID de recurso do Resource Manager, você deve incluir a barra à direita no URI. 
    
    A resposta inclui o token de acesso que você precisa para acessar o Azure Resource Manager. 
    
    Exemplo de resposta:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

4. Agora use o token de acesso para acessar o Azure Resource Manager e leia as propriedades do grupo de recursos ao qual você concedeu anteriormente o acesso à MSI atribuída pelo usuário. Substitua `<SUBSCRIPTION ID>` e `<RESOURCE GROUP>` pelos valores que você especificou anteriormente e `<ACCESS TOKEN>` pelo token retornado na etapa anterior.

    > [!NOTE]
    > A URL diferencia maiúsculas de minúsculas, portanto, verifique se você usou letras maiúsculas e minúsculas exatamente da mesma forma que você usou anteriormente ao nomear o grupo de recursos, incluindo a letra maiúscula "G" em `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    A resposta contém as informações de grupo de recursos específicas, semelhantes ao exemplo a seguir: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](msi-overview.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.
