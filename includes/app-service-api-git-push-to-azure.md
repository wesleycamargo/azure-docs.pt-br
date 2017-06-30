Use a CLI do Azure para obter a URL de implantação remota de seu Aplicativo de API e configurar sua implantação local do Git para enviar por push para o remoto.

```bash
giturl=$(az webapp deployment source config-local-git -n $app_name \ -g myResourceGroup --query [url] -o tsv)

git remote add azure $giturl
```

Envie por push para o Azure remoto para implantar seu aplicativo. Você recebe uma solicitação pela senha criada anteriormente ao criar o usuário de implantação. Verifique se você inseriu a senha que você criou anteriormente no início rápido, não a senha usada para fazer logon no portal do Azure.

```bash
git push azure master
```
