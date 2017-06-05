Use o comando [az appservice web create](/cli/azure/appservice/web#create) para criar o Aplicativo Web. No seguinte comando, substitua um nome de aplicativo exclusivo onde você vê o espaço reservado `<app_name>`. O `<app_name>` é usado no site do DNS padrão para o aplicativo Web. Se `<app_name>` for não exclusivo, você obterá a mensagem de erro amigável "O site com o nome <app_name> fornecido já existe".

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

Quando o aplicativo Web tiver sido criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```

Navegue até o site (`http://<app_name>.azurewebsites.net`) para ver seu aplicativo Web recém-criado.

![app-service-web-service-created](../articles/app-service-web/media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)


## <a name="configure-local-git-deployment"></a>Configurar a implantação do git local

O Serviço de Aplicativo oferece suporte a várias maneiras de implantar conteúdo em um aplicativo Web, como FTP, Git local, GitHub, Visual Studio Team Services e Bitbucket. 

Para este início rápido, implante usando o Git local. Isso significa que você implanta usando um comando Git para enviar por push de um repositório local para um repositório no Azure. 

Use o comando [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) para configurar o acesso do Git local para o aplicativo Web.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

A saída tem o seguinte formato:

```
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

Salve o URI mostrado. Você o usará na próxima etapa. O `<username>` é o [usuário de implantação](#configure-a-deployment-user) que você criou na etapa anterior.

## <a name="push-to-azure-from-git"></a>Enviar por push do Git para o Azure

Adicione um Azure remoto ao seu repositório Git local.

```bash
git remote add azure <URI from previous step>
```

Envie por push para o Azure remoto para implantar seu aplicativo. Você recebe uma solicitação pela senha criada anteriormente ao criar o usuário de implantação. Verifique se você inseriu a senha que você criou em [Configurar um usuário de implantação](#configure-a-deployment-user), não a senha usada para fazer logon no Portal do Azure.

```azurecli
git push azure master
```

O comando anterior exibe informações semelhantes ao seguinte exemplo:

```bash
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (13/13), 2.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cc39b1e4cb'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-app"></a>Navegar até o aplicativo


Navegue até o aplicativo implantado:

```
http://<app_name>.azurewebsites.net
```

