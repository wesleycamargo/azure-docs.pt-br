---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 8ebf5ddfa118e0aeadeab0c00a981871a4b5708e
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429916"
---
Os aplicativos .NET podem usar o cliente de cache **StackExchange.Redis** , que pode ser configurado no Visual Studio usando um pacote NuGet que simplifica a configuração dos aplicativos do cliente de cache. 

> [!NOTE]
> Para obter mais informações, consulte a página do GitHub [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) e a [documentação do cliente de cache StackExchange.Azure](http://github.com/StackExchange/StackExchange.Redis#documentation).
>
>

Para configurar um aplicativo de cliente no Visual Studio utilizando o pacote NuGet StackExchange.Redis, clique com o botão direito no projeto em **Gerenciador de Soluções** e escolha **Gerenciar pacotes NuGet**. 

![Gerenciar pacotes NuGet](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Digite **StackExchange.Redis** ou **StackExchange.Redis.StrongName** na caixa de texto de pesquisa, selecione a versão desejada nos resultados e clique em **Instalar**.

> [!NOTE]
> Se preferir utilizar uma versão de nome forte da biblioteca do cliente de **StackExchange.Redis**, escolha **StackExchange.Redis.StrongName**; caso contrário escolha **StackExchange.Redis**.
>
>

![Pacote NuGet StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Os downloads de pacote NuGet acrescentam as referências de assembly necessárias para o seu aplicativo de cliente para acessar o Cache do Azure para Redis com o cliente de Cache StackExchange.Azure para Redis.

> [!NOTE]
> Se você já tiver configurado seu projeto para usar o StackExchange.Redis, poderá verificar atualizações ao pacote por meio do **Gerenciador de Pacotes NuGet**. Para verificar e instalar as versões atualizadas do pacote NuGet StackExchange.Redis, clique em **Atualizações** na janela **Gerenciador de Pacotes NuGet**. Se uma atualização ao pacote NuGet StackExchange.Redis estiver disponível, você poderá atualizar seu projeto para usar a versão atualizada.
>
>

Você também pode instalar o pacote do NuGet StackExchange.Redis clicando no **Gerenciador de Pacotes NuGet**, **Console do Gerenciador de Pacotes** no menu **Ferramentas** e executando o seguinte comando na janela **Console do Gerenciador de Pacotes**.

```
Install-Package StackExchange.Redis
```
