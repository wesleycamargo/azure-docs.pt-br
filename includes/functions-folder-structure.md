
O código para todas as funções em um aplicativo de função específica está localizado em uma pasta raiz que contém um arquivo de configuração de host e uma ou mais subpastas. Cada subpasta contém o código para uma função distinta, como no exemplo a seguir:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

O arquivo host.json contém algumas configurações específicas do tempo de execução e encontra-se na pasta raiz do aplicativo de função. Para obter informações sobre as configurações que estão disponíveis, consulte a [referência do host.json](../articles/azure-functions/functions-host-json.md).

Cada função tem uma pasta que contém um ou mais arquivos de código, a configuração function.json e outras dependências.

