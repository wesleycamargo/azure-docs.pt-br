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
ms.openlocfilehash: dca6ca5f21f4a4f1701af57eb40d92094b6a4754
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Como gerenciar a expiração do conteúdo da Web na Rede de Distribuição de Conteúdo do Microsoft Azure
> [!div class="op_single_selector"]
> * [Conteúdo da Web do Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 

Os arquivos de qualquer servidor Web de origem publicamente acessível podem ser armazenados em cache na CDN (Rede de Distribuição de Conteúdo) do Azure até que a TTL (vida útil) tenha decorrida. A vida útil é determinada pelo cabeçalho `Cache-Control` na resposta HTTP do servidor de origem. Este artigo descreve como definir cabeçalhos `Cache-Control` para recurso de Aplicativos Web do Serviço de Aplicativo do Microsoft Azure, Serviços de Nuvem do Microsoft Azure, aplicativos ASP.NET e sites do IIS (Serviços de Informações da Internet), todos configurados de forma semelhante. Você pode definir o cabeçalho `Cache-Control` usando arquivos de configuração ou programaticamente. 

Também é possível controlar as configurações de cache do Portal do Azure, definindo as [regras de cache da CDN](cdn-caching-rules.md). Se você configurar uma ou mais regras de cache e definir o comportamento do cache para **Substituir** ou **Ignorar cache**, as configurações de cache fornecidas pela origem discutidas neste artigo serão ignoradas. Para obter informações sobre conceitos gerais de cache, consulte [Como funciona o cache](cdn-how-caching-works.md).

> [!TIP]
> Você pode optar por não definir nenhuma vida útil em um arquivo. Nesse caso, o Azure CDN aplica automaticamente um TTL padrão de sete dias, exceto se tiver configurado as regras de cache no Portal do Azure. Esse padrão TTL aplica-se somente para otimizações de entrega da web gerais. Para otimizações de arquivo grande, o TTL padrão é de um dia e para otimizações de streaming de mídia, o TTL padrão é um ano.
> 
> Para obter mais informações sobre como a CDN do Azure trabalha para acelerar o acesso a arquivos e outros recursos, consulte [Visão geral da Rede de Distribuição de Conteúdo do Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Configurando cabeçalhos de Cache-Control usando arquivos de configuração
Para conteúdo estático, como imagens e folhas de estilo, você pode controlar a frequência de atualização modificando os arquivos de configuração **applicationHost.config** ou **Web.config** para seu aplicativo Web. O elemento `<system.webServer>/<staticContent>/<clientCache>` em qualquer um dos arquivos define o cabeçalho `Cache-Control` para o conteúdo.

### <a name="using-applicationhostconfig-files"></a>Usando arquivos ApplicationHost.config
O arquivo **ApplicationHost.config** é o arquivo raiz do sistema de configuração do IIS. As definições de configuração em um arquivo **ApplicationHost.config** afetam todos os aplicativos no site, mas são substituídas pelas configurações dos arquivos **Web.config** que existem para um aplicativo Web.

### <a name="using-webconfig-files"></a>Usando arquivos Web.config
Com um arquivo **Web.config**, você pode personalizar a maneira como seu aplicativo Web ou um diretório específico dele se comporta. Normalmente, você tem pelo menos um arquivo **Web.config** na pasta raiz do seu aplicativo Web. Para cada arquivo **Web.config** em uma pasta específica, as definições de configuração afetam tudo nessa pasta e em todas as suas subpastas, a menos que sejam substituídas no nível da subpasta por outro arquivo **Web.config**. Por exemplo, você pode definir um elemento `<clientCache>` em um arquivo **Web.config** na pasta raiz do seu aplicativo Web para armazenar em cache todo o conteúdo estático nele por três dias. Você também pode adicionar um arquivo **Web.config** com conteúdo mais variável (por exemplo, `\frequent`) e definir seu elemento `<clientCache>` para armazenar em cache o conteúdo da subpasta por seis horas. O resultado final é que o conteúdo em todo o site será armazenado em cache por três dias, exceto pelo o conteúdo no diretório `\frequent`, que será armazenado em cache por apenas seis horas.  

O exemplo de XML a seguir mostra como configurar o elemento `<clientCache>` em um arquivo de configuração para especificar a idade máxima de três dias:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Para usar o atributo **cacheControlMaxAge**, você deve definir o valor do atributo **cacheControlMode** como `UseMaxAge`. Essa configuração fez o cabeçalho HTTP e a diretiva, `Cache-Control: max-age=<nnn>`, serem adicionados à resposta. O formato do valor do intervalo de tempo para o atributo **cacheControlMaxAge** é `<days>.<hours>:<min>:<sec>`. Seu valor é convertido em segundos e é usado como o valor da diretiva `Cache-Control` `max-age`. Para obter mais informações sobre o elemento `<clientCache>`, consulte [Cache de cliente <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Configurando cabeçalhos Cache-Control programaticamente
Para aplicativos ASP.NET, você controla o comportamento de cache de CDN programaticamente configurando a propriedade **HttpResponse.Cache** da API do .NET. Para obter informações sobre a propriedade **HttpResponse.Cache**, consulte [Propriedade HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [Classe HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Para armazenar em cache o conteúdo do aplicativo programaticamente no ASP.NET, siga essas etapas:
   1. Verifique se o conteúdo está marcado como armazenável em cache, configurando `HttpCacheability` para `Public`. 
   2. Defina um validador de cache chamando um dos seguintes métodos `HttpCachePolicy`:
      - Chame `SetLastModified` para definir um valor de carimbo de data/hora para o cabeçalho `Last-Modified`.
      - Chame `SetETag` para definir um valor para o cabeçalho `ETag`.
   3. Opcionalmente, especifique um tempo de expiração de cache, chamando `SetExpires` para definir um valor para o cabeçalho `Expires`. Caso contrário, as heurísticas de cache padrão descritas anteriormente neste documento se aplicam.

Por exemplo, para armazenar em cache o conteúdo por uma hora, adicione o seguinte código C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testando o cabeçalho de Controle de Cache
Você pode facilmente verificar as configurações TTL do seu conteúdo da Web. Com as [ferramentas para desenvolvedores](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) do seu navegador, teste se o conteúdo da Web inclui cabeçalho de resposta `Cache-Control`. Você também pode usar uma ferramenta como a **wget**, [Postman](https://www.getpostman.com/) ou [Fiddler](http://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Próximas etapas
* [Leia os detalhes sobre o elemento **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Leia a documentação sobre a propriedade **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Leia a documentação da **Classe HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)  
* [Saiba mais sobre conceitos de cache](cdn-how-caching-works.md)
