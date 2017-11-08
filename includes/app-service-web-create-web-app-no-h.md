No Cloud Shell, crie um [aplicativo Web](../articles/app-service/app-service-web-overview.md) no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [az webapp list-runtimes](/cli/azure/webapp#create). 

O aplicativo Web fornece um espaço de hospedagem para seu código e fornece uma URL para exibir o aplicativo implantado.

No comando a seguir, substitua *\<nome_do_aplicativo >* por um nome exclusivo (os caracteres válidos são `a-z`, `0-9` e `-`). Se `<app_name>` não for exclusivo, você obterá a mensagem de erro "O site com o nome <nome_do_aplicativo> fornecido já existe." A URL padrão do aplicativo Web é `https://<app_name>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

Quando o aplicativo Web tiver sido criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Você criou um aplicativo Web vazio com a implantação do git habilitada.

> [!NOTE]
> A URL do Git remoto é mostrada na propriedade `deploymentLocalGitUrl` com o formato `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Salve essa URL pois você precisará dela mais tarde.
>

Navegue até o site para ver seu Aplicativo Web recém-criado.

```bash
http://<app_name>.azurewebsites.net
```
