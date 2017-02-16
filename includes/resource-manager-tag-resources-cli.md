Para adicionar uma marca a um grupo de recursos, use **azure group set**. Se o grupo de recursos não tiver todas as marcas existentes, passe a marca.

```azurecli
azure group set -n tag-demo-group -t Dept=Finance
```

Marcações são atualizadas como um todo. Se desejar adicionar uma marca a um grupo de recursos com marcas existentes, passe todas as marcas. 

```azurecli
azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
```

Marcas não são herdadas pelos recursos em um grupo de recursos. Para adicionar uma marca a um recurso, use **azure resource set**. Passe o número de versão de API para o tipo de recurso ao qual você está adicionando a marca. Se precisa recuperar a versão de API, use o comando a seguir com o provedor de recursos para o tipo que você está configurando:

```azurecli
azure provider show -n Microsoft.Storage --json
```

Nos resultados, procure o tipo de recurso desejado.

```azurecli
"resourceTypes": [
{
  "resourceType": "storageAccounts",
  ...
  "apiVersions": [
    "2016-01-01",
    "2015-06-15",
    "2015-05-01-preview"
  ]
}
...
```

Agora, forneça essa versão de API, nome do grupo de recursos, nome do recurso, tipo de recurso e valor da marca como parâmetros.

```azurecli
azure resource set -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -t Dept=Finance -o 2016-01-01
```

Marcações existem diretamente em recursos e grupos de recursos. Para ver as marcações existentes, obtenha um grupo de recursos e seus recursos com **azure group show**.

```azurecli
azure group show -n tag-demo-group --json
```

Ele retorna metadados sobre o grupo de recursos, incluindo todas as marcas aplicadas a esse grupo.

```azurecli
{
  "id": "/subscriptions/4705409c-9372-42f0-914c-64a504530837/resourceGroups/tag-demo-group",
  "name": "tag-demo-group",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "location": "southcentralus",
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Project": "Upgrade"
  },
  ...
}
```

Você pode exibir as marcações de um recurso específico usando **azure resource show**.

```azurecli
azure resource show -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -o 2016-01-01 --json
```

Para recuperar todos os recursos com um valor de marca, use:

```azurecli
azure resource list -t Dept=Finance --json
```

Para recuperar todos os grupos de recursos com um valor de marca, use:

```azurecli
azure group list -t Dept=Finance
```

Você pode exibir as marcas existentes em sua assinatura com o seguinte comando:

```azurecli
azure tag list
```


<!--HONumber=Jan17_HO2-->


