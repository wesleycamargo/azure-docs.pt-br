---
title: Como usar redis-cli com o Cache Redis do Azure | Microsoft Docs
description: Saiba como usar redis-cli com o Cache Redis do Azure.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: wesmc
ms.openlocfilehash: facc3e2079c8f5e19266f5379762d71754a6ed84
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182276"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-redis-cache"></a>Como usar a ferramenta de linha de comando Redis com o Cache Redis do Azure

*redis-cli.exe* é uma ferramenta de linha de comando popular para interagir com um Cache Redis como um cliente. Essa ferramenta também está disponível para uso com o Cache Redis do Azure.

A ferramenta está disponível para as plataformas Windows baixando as [Ferramentas de linha de comando Redis para Windows](https://github.com/MSOpenTech/redis/releases/). 

Se você deseja executar a ferramenta de linha de comando em outra plataforma, faça o download do Cache Redis de [http://redis.io/download](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Coletar informações de acesso do cache

Você pode coletar as informações necessárias para acessar o cache usando três métodos:

1. Usando a CLI do Azure [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Usando o Azure PowerShell [Get-AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-4.4.1)
3. Usando o Portal do Azure.

Nesta seção, você vai recuperar as chaves do Portal do Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Habilitar o acesso para redis-cli.exe

Com o Cache Redis do Azure, apenas a porta SSL (6380) é habilitada por padrão. A ferramenta de linha de comando `redis-cli.exe` não oferece suporte a SSL. Você tem duas opções de configuração para usá-lo:

1. [Habilitar a porta não SSL (6379)](cache-configure.md#access-ports) - **Essa configuração não é recomendada** porque nessa configuração, as chaves de acesso são enviadas por meio de TCP em texto não criptografado. Essa alteração pode comprometer o acesso ao seu cache. O único cenário em que você pode considerar essa configuração é quando você está acessando apenas um cache de teste.

2. Baixar e instalar o [stunnel](https://www.stunnel.org/downloads.html).

    Execute **stunnel GUI Start** para iniciar o servidor.

    Clique com o botão direito no ícone da barra de tarefas para o servidor stunnel e clique em **Mostrar janela de log**.

    No menu da janela Log stunnel, clique em **Configuração** > **Editar configuração** para abrir o arquivo de configuração atual.

    Adicione a seguinte entrada para *redis-cli.exe* sob a seção **Definições de serviço**. Insira o nome real do cache em vez de `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Salve e feche o arquivo de configuração. 
  
    No menu da janela Log stunnel, clique em **Configuração** > **Recarregar configuração**.


## <a name="connect-using-the-redis-command-line-tool"></a>Conecte-se usando a ferramentas de linha de comando do Redis.

Ao usar stunnel, execute *redis-cli.exe* e passe apenas sua *porta* e *chave de acesso* (primário ou secundário) para conectar-se ao cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel com redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Se você estiver usando um cache de teste com a porta não SSL **não segura**, execute `redis-cli.exe` e passe o *nome de host*, *porta*, e *chave de acesso* (primário ou secundário) para se conectar ao cache de teste.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel com redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como usar o [Console Redis](cache-configure.md#redis-console) para emitir comandos.

