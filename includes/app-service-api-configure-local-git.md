Configure a implantação local do Git para o aplicativo de API com o comando [az webapp source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git).   

O Serviço de Aplicativo oferece suporte a várias maneiras de implantar conteúdo em um aplicativo Web, como FTP, Git local, GitHub, Visual Studio Team Services e Bitbucket. Para este início rápido, implante usando o Git local. Isso significa que você implanta usando um comando Git para enviar por push de um repositório local para um repositório no Azure.  

Use o script abaixo para definir as credenciais de implantação de nível de conta que serão usadas ao enviar o código por push, incluindo seus próprios valores para o nome de usuário e a senha.   

```azurecli-interactive
az webapp deployment user set --user-name <desired user name> --password <desired password>
```
