---
title: "Como gerenciar a expiração do conteúdo da Web na Rede de Distribuição de Conteúdo do Microsoft Azure | Microsoft Docs"
description: "Saiba como gerenciar a expiração de conteúdo de Serviços de Nuvem/Aplicativos Web do Azure, ASP.NET ou IIS na CDN do Azure."
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: d58a245923242b3963b188ca869e8290d999c0a2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Como gerenciar a expiração do conteúdo da Web na Rede de Distribuição de Conteúdo do Microsoft Azure
 na CDN do Azure
> [!div class="op_single_selector"]
> * [Serviços de Nuvem/Aplicativos Web do Azure, ASP.NET ou IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 

Arquivos de qualquer servidor Web de origem publicamente acessível podem ser armazenados em cache na CDN (Rede de Distribuição de Conteúdo) até que a TTL (vida útil) tenha decorrida. A vida útil é determinada pelo [`Cache-Control` cabeçalho](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) na resposta HTTP do servidor de origem. Este artigo descreve como definir cabeçalhos `Cache-Control` para recurso de Aplicativos Web do Serviço de Aplicativo do Microsoft Azure, Serviços de Nuvem do Microsoft Azure, aplicativos ASP.NET e sites dos Serviços de Informações da Internet, todos configurados de forma semelhante.

> [!TIP]
> Você pode optar por não definir nenhuma vida útil em um arquivo. Nesse caso, o Azure CDN aplica automaticamente um TTL padrão de sete dias.
> 
> Para obter mais informações sobre como a CDN do Azure trabalha para acelerar o acesso a arquivos e outros recursos, consulte [Visão geral da Rede de Distribuição de Conteúdo do Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-in-configuration-files"></a>Configurando cabeçalhos de Cache-Control em arquivos de configuração
Para conteúdo estático, como imagens e folhas de estilo, você pode controlar a frequência de atualização modificando os arquivos **applicationHost.config** ou **Web.config** para seu aplicativo Web. O elemento **system.webServer\staticContent\clientCache** no arquivo de configuração define o cabeçalho `Cache-Control` para o seu conteúdo. Para os arquivos **web.config**, as configurações afetam tudo na pasta e em todas as suas subpastas, a menos que sejam substituídas no nível da subpasta. Por exemplo, você pode definir uma configuração de TTL padrão na pasta raiz para armazenar em cache todo o conteúdo estático por três dias e, definir uma subpasta com conteúdo mais variável para armazenar em cache seu conteúdo por apenas seis horas. Embora as configurações de arquivo **applicationHost.config** afetem todos os aplicativos no site, elas são substituídas pelas configurações de quaisquer arquivos **web.config** existentes nos aplicativos.

O seguinte exemplo XML mostra como configurar o **clientCache** para especificar um temo máximo de três dias:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Especificar **UseMaxAge** faz com que um cabeçalho `Cache-Control: max-age=<nnn>` seja adicionado à resposta com base no valor especificado no atributo **CacheControlMaxAge**. O formato do intervalo de tempo para o atributo **cacheControlMaxAge** é `<days>.<hours>:<min>:<sec>`. Para obter mais informações sobre o nó **clientCache**, consulte [Cache de Cliente <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Configurando cabeçalhos Cache-Control no código
Para aplicativos ASP.NET, você pode definir o comportamento de cache de CDN programaticamente, definindo a propriedade **HttpResponse.Cache**. Para obter mais informações sobre a propriedade **HttpResponse.Cache**, consulte [HttpResponse.Cache Property](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [HttpCachePolicy Class](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Para armazenar em cache o conteúdo do aplicativo programaticamente no ASP.NET, siga essas etapas:
   1. Verifique se o conteúdo está marcado como armazenável em cache, configurando `HttpCacheability` para *Público*. 
   2. Defina um validador de cache, chamando um dos seguintes métodos:
      - Chame `SetLastModified` para definir o carimbo de data/hora LastModified.
      - Chame `SetETag` para definir um valor `ETag`.
   3. Opcionalmente, especifique um tempo de expiração de cache, chamando `SetExpires`. Caso contrário, as heurísticas de cache padrão descritas anteriormente neste documento se aplicam.

Por exemplo, para armazenar em cache o conteúdo por uma hora, adicione o seguinte código C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Próximas etapas
* [Leia os detalhes sobre o elemento **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Leia a documentação sobre a propriedade **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Leia a documentação sobre a **classe HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

