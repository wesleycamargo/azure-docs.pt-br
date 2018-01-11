## <a name="deploy-uploaded-zip-file"></a>Implantar o arquivo ZIP carregado

No Cloud Shell, implante o arquivo ZIP carregado no seu aplicativo Web usando o comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip). Verifique se você substituiu o *\<app_name>* pelo nome do seu aplicativo Web.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/myAppFiles.zip
```

Esse comando implanta os arquivos e diretórios do arquivo ZIP para sua pasta de aplicativo padrão do Serviço de Aplicativo (`\home\site\wwwroot`) e reinicia o aplicativo. Se qualquer processo de compilação personalizada adicional for configurado, ele também será executado.
