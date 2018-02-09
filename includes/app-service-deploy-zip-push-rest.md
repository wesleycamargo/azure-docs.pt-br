## <a name="rest"></a>Implantar usando as APIs REST 
 
Você pode usar as [APIs REST do serviço de implantação](https://github.com/projectkudu/kudu/wiki/REST-API) para implantar o arquivo .zip ao seu aplicativo no Azure. Basta envie uma solicitação POST para https://<app_name>.scm.azurewebsites.net/api/zipdeploy. A solicitação POST deve conter o arquivo .zip no corpo da mensagem. As credenciais de implantação para seu aplicativo são fornecidas na solicitação usando a autenticação BÁSICA HTTP. Para obter mais informações, consulte a [referência de implantação por push do .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

O exemplo a seguir usa a ferramenta de rotação para enviar uma solicitação que contém o arquivo .zip. Você pode executar a rotação do terminal em um computador Mac ou Linux ou usando o Bash no Windows. Substitua o `<zip_file_path>` espaço reservado com o caminho para o local do arquivo .zip do seu projeto. Substitua também `<app_name>` com o nome exclusivo do seu aplicativo.

Substitua o `<deployment_user>` espaço reservado com o nome de usuário de suas credenciais de implantação. Quando solicitado pelo cURL, digite a senha. Para saber como definir as credenciais de implantação para seu aplicativo, consulte [Definir e redefinir credenciais de usuário](../articles/app-service/app-service-deployment-credentials.md#userscope).   

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Essa solicitação dispara a implantação de envio do arquivo .zip carregado. Você pode examinar as implantações atuais e anteriores, usando o ponto de extremidade https://<app_name>.scm.azurewebsites.net/api/deployments, conforme mostrado no exemplo de cURL a seguir. Novamente, substitua `<app_name>` com o nome do seu aplicativo e `<deployment_user>` com o nome de usuário de suas credenciais de implantação.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```