## <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

Adicione um Azure remoto ao seu repositório Git local.

```bash
git remote add azure <URI from previous step>
```

Envie por push para o Azure remoto para implantar seu aplicativo. Você recebe uma solicitação pela senha criada anteriormente ao criar o usuário de implantação. Verifique se você inseriu a senha que você criou em [Configurar um usuário de implantação](#configure-a-deployment-user), não a senha usada para fazer logon no Portal do Azure.

```bash
git push azure master
```

O comando anterior exibe informações semelhantes ao seguinte exemplo:
