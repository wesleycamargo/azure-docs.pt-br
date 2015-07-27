<properties 
 pageTitle="Como gerenciar a expiração do conteúdo do serviço de nuvem na CDN do Azure (Rede de Distribuição de Conteúdo" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="zhangmanling" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.workload="media" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="04/25/2015" 
 ms.author="mazha"/>

#Como gerenciar a expiração do conteúdo do serviço de nuvem na CDN do Azure (Rede de Distribuição de Conteúdo

Os objetos que mais se beneficiam do cache da CDN do Azure são aqueles que são acessados frequentemente durante seu período de TTL (vida útil). Um objeto permanece no cache pelo período de TTL e é atualizado pelo serviço de nuvem depois que esse tempo termina. Em seguida, o processo se repete.

Se você não fornecer valores de cache, a TTL de um objeto será de sete dias.

Para conteúdo estático, como imagens e folhas de estilo, você pode controlar a frequência de atualização incluindo um web.config na pasta CDN que tem o conteúdo e modificando as configurações de **clientCache** para controlar o cabeçalho Cache-Control para seu conteúdo. As configurações de web.config afetarão tudo na pasta e em todas as subpastas, a menos que sejam substituídas em outra subpasta mais abaixo. Por exemplo, você pode definir um tempo de vida padrão na raiz para que todo o conteúdo estático seja armazenado em cache por três dias, mas ter uma subpasta que tenha conteúdo mais variável com uma configuração de cache de seis horas.

O seguinte XML mostra e exemplo de configuração de **clientCache** para especificar um tempo decorrido máximo de três dias:

	<configuration> 
	  <system.webServer> 
	        <staticContent> 
	            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" /> 
	        </staticContent> 
	  </system.webServer> 
	</configuration>

Especificar **UseMaxAge** adiciona um cabeçalho Cache-Control: max-age=<nnn> à resposta com base no valor especificado no atributo **CacheControlMaxAge**. O formato do período de tempo para o atributo **cacheControlMaxAge** é <days>.<hours>:<min>:<sec>. Para obter mais informações sobre o nó **clientCache**, consulte [Cache de cliente <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).

Para conteúdo retornado de aplicativos, como páginas .aspx, você pode definir o comportamento do cache CDN programaticamente definindo a propriedade **HttpResponse.Cache**. Para obter mais informações sobre a propriedade **HttpResponse.Cache**, consulte [Propriedade HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [Classe HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).

Se você quiser armazenar em cache programaticamente o conteúdo do aplicativo, verifique se o conteúdo está marcado como armazenável em cache, definindo HttpCacheability como *Public*. Além disso, verifique se um validador de cache está definido. O validador de cache pode ser um carimbo de data/hora de Última Modificação definido chamando SetLastModified ou um valor de etag definido chamando SetETag. Opcionalmente, você também pode especificar um tempo de expiração de cache chamando SetExpires ou pode contar com a heurística de cache padrão descrita anteriormente neste documento.

Por exemplo, para armazenar o conteúdo em cache por uma hora, adicione o seguinte:

            // Set the caching parameters.
            Response.Cache.SetExpires(DateTime.Now.AddHours(1));
            Response.Cache.SetCacheability(HttpCacheability.Public);
            Response.Cache.SetLastModified(DateTime.Now);

##Consulte também

[Como gerenciar a expiração do conteúdo de blob na CDN (Rede de Distribuição de Conteúdo) do Azure](./cdn-manage-expiration-of-blob-content.md)

<!---HONumber=July15_HO3-->