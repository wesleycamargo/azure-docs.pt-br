<properties 
	pageTitle="CDN - melhorar o desempenho ao compactar arquivos" 
	description="Você pode aumentar a velocidade de transferência de arquivos e melhorar o desempenho do carregamento de páginas ao compactar seus arquivos." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="juliako"/>

#Melhorar o desempenho ao compactar arquivos

Este tópico descreve como aumentar a velocidade de transferência de arquivos e melhorar o desempenho do carregamento de páginas ao compactar os arquivos.

Há duas maneiras de a CDN poder oferecer suporte à compactação:

- Você pode habilitar a compactação no servidor de origem e, nesse caso, a CDN oferecerá suporte à compactação por padrão e entregará arquivos compactados aos clientes. 
- Você pode habilitar a compactação diretamente nos servidores de borda da CDN e, nesse caso, a CDN compactará os arquivos e os servirá para os usuários finais.

##Definições

- Cabeçalho da solicitação **Accept-Encoding** - esse cabeçalho indica os métodos de compactação com suporte de um agente de usuário. Ele deve ser incluído no cabeçalho da solicitação. Para obter mais informações, consulte [Definições de campo de cabeçalho](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- **Tipo de conteúdo** - uma lista de tipos de conteúdo precisa ser adicionada à compactação. Somente os arquivos de texto são qualificados para compactação. Por exemplo, text/plain, text/html.
- **Método de compactação** - os métodos de compactação com suporte são gzip/deflate/bzip2, um método com suporte deve ser definido no Cabeçalho da Solicitação Accept-Encoding. 
- **Status do cache** - o status do cache identifica se o conteúdo solicitado já está armazenado em cache no POP mais próximo ao solicitante.  
- **Tamanho do arquivo** - por padrão, a compactação só oferece suporte a arquivos com mais de 1 byte e com menos de 1 MB.  

##Fluxo de trabalho

1. O solicitante envia uma solicitação de conteúdo.
2. Um servidor de borda verifica se há um cabeçalho **Accept-Encoding**.
	1. Se tiver sido incluído, esse cabeçalho identificará o método de compactação solicitado.
	1. Se estiver ausente, esse tipo de solicitação será servido em um formato não compactado.
3.	O POP de borda mais próximo verifica o status do cache, o método de compactação e se ele ainda tem um TTL válido.
	1.	Erro de Cache: se a versão solicitada não for armazenada em cache, a solicitação será encaminhada para a origem.
	2.	Acerto de Cache com o mesmo método de compactação: o servidor de borda entregará o conteúdo compactado para o cliente imediatamente.
	3.	Acertos de cache com métodos de compactação diferentes: o servidor de borda transcodificará o ativo para o método de compactação solicitado. 
	4.	Acertos de cache e não compactados: se a solicitação inicial fez com que o ativo fosse armazenado em cache em um formato não compactado, será executada uma verificação para ver se a solicitação está qualificada para a compactação no servidor de borda (com base nos critérios da seção de definição/requisito acima).
		1.	Se estiver qualificado, o servidor de borda compactará o arquivo e o servirá ao cliente.
		2.	Se não estiver qualificado: o servidor de borda fornecerá o conteúdo não compactado para o cliente imediatamente. 

![Compactação de arquivos](./media/cdn-file-compression/cdn-compress-files.png)

##Considerações 

1. Para pontos de extremidade de streaming habilitado para a CDN de Serviços de Mídia, a compactação está habilitada por padrão para os seguintes tipos de conteúdo: application/vnd.ms-sstr+xml,application/dash+xml,application/vnd.apple.mpegurl,application/f4m+xml. Você não pode habilitar/desabilitar a compactação para os tipos mencionados usando o portal do Azure.  
2. Somente uma versão de arquivo (compactado ou não compactado) será armazenada em cache no servidor de borda. Uma solicitação para uma versão diferente resultará na transcodificação do conteúdo no servidor de borda.  

<!---HONumber=August15_HO7-->