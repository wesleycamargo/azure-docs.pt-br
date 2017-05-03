Você pode verificar se a conexão foi bem-sucedida usando o comando da CLI a seguir. Configure os valores para coincidir com os seus. No exemplo, -n se refere ao nome da conexão que você criou e deseja testar.

```azurecli
az network vpn-connection show -n VNet1toSite2 -g TestRG1
```

Quando a conexão ainda está sendo estabelecida, seu status de conexão mostra 'Conectando'. Quando a conexão é estabelecida, o status da conexão é exibido como ‘Conectada’ e é possível ver os bytes de entrada e saída.