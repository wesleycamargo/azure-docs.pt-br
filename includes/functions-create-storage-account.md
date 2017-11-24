## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

O Functions usa uma conta de propósito geral do Armazenamento do Azure para manter o estado e outras informações sobre suas funções. Crie uma conta de propósito geral de armazenamento no grupo de recursos que você criou ao utilizar o comando [az storage account create](/cli/azure/storage/account#create).

No comando a seguir, substitua um nome da conta de armazenamento globalmente exclusivo quando você vir o espaço reservado `<storage_name>`. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

Depois que a conta de armazenamento for criada, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```