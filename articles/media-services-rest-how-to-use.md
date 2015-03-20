<properties 
	pageTitle="Visão geral da API de REST de serviços de mídia - Azure" 
	description="Visão geral da API de REST de serviços de mídia" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="juliako"/>


# Visão geral da API de REST de serviços de mídia 

Os serviços de mídia do Microsoft Azure é um serviço que aceita solicitações HTTP com base em OData e pode responder em verbose JSON ou atom+pub. Como os serviços de mídia estão em conformidade com as diretrizes de design do Azure, há um conjunto de cabeçalhos HTTP necessários que cada cliente deve usar ao se conectar aos serviços de mídia, bem como um conjunto de cabeçalhos opcionais que podem ser usados. As seções a seguir descrevem os cabeçalhos e verbos HTTP que podem ser usados ao criar solicitações e receber respostas dos serviços de mídia.


## Os cabeçalhos de solicitação HTTP padrão suportados pelos serviços de mídia

Para todas as chamadas feitas nos serviços de mídia, há um conjunto de cabeçalhos necessários que você deve incluir na solicitação e também um conjunto de cabeçalhos opcionais você talvez queira incluir. A tabela a seguir lista os cabeçalhos necessários:


<table border="1">
<tr><th>Cabeçalho</th><th>Tipo</th><th>Valor</th></tr>
<tr><td>Autorização</td><td>Portador</td><td>Portador é o único mecanismo de autorização aceito. O valor também deve incluir o token de acesso fornecido pelo ACS.</td></tr>
<tr><td>x-ms-version</td><td>Decimal</td><td>2.9</td></tr>
<tr><td>DataServiceVersion</td><td>Decimal</td><td>3.0</td></tr>
<tr><td>MaxDataServiceVersion</td><td>Decimal</td><td>3.0</td></tr>
</table><br/>


>[AZURE.NOTE] Como os serviços de mídia usam o OData para expor seu repositório de metadados de ativo subjacente por meio de APIs REST, os cabeçalhos DataServiceVersion e MaxDataServiceVersion devem ser incluídos em qualquer solicitação. No entanto, se não forem, então os serviços de mídia assumem que o valor DataServiceVersion em uso é 3.0.

Este é um conjunto de cabeçalhos opcional:

<table border="1">
<tr><th>Cabeçalho</th><th>Tipo</th><th>Valor</th></tr>
<tr><td>Data</td><td>Data do RFC 1123</td><td>Carimbo de hora da solicitação</td></tr>
<tr><td>Aceitar</td><td>Tipo de conteúdo</td><td>O tipo de conteúdo solicitado para a resposta como a seguir:
<ul><li>application/json;odata=verbose</li><li>application/atom+xml</li></ul></br> As respostas podem ter diferentes tipos de conteúdo, como uma busca de blob, onde uma resposta bem-sucedida irá conter o fluxo de blob como a carga.</td></tr>
<tr><td>Codificação aceita</td><td>Gzip, deflate</td><td>Codificar GZIP e DEFLATE, quando aplicável. Observação: Para grandes recursos, os serviços de mídia podem ignorar este cabeçalho e retornar dados não compactados.
</td></tr>
<tr><td>Idioma aceito</td><td>"en", "es", e assim por diante.</td><td>Especifica o idioma preferencial para a resposta.</td></tr>
<tr><td>Conjunto de caracteres aceito</td><td>Tipo de conjunto de caracteres como "UTF-8"</td><td>Padrão é UTF-8.</td></tr>
<tr><td>Método X-HTTP</td><td>Método HTTP</td><td>Permite que os clientes ou firewalls que não suportam métodos HTTP como PUT ou DELETE usem esses métodos, desviados via uma chamada GET.</td></tr>
<tr><td>Tipo de conteúdo</td><td>Tipo de conteúdo</td><td>Tipo de conteúdo do corpo da solicitação em solicitações PUT ou POST.</td></tr>
<tr><td>ID da solicitação de cliente</td><td>Cadeia de caracteres</td><td>Um valor definido pelo chamador que identifica a solicitação em questão. Se especificado, esse valor será incluído na mensagem de resposta como uma maneira de mapear a solicitação. <br/><br/>
<b>Importante</b><br/>
Valores devem ser vinculados a 2096b (2K).</td></tr>
</table><br/>


## Cabeçalhos de resposta HTTP padrão suportados pelos serviços de mídia

Este é um conjunto de cabeçalhos que podem ser retornados para você, dependendo do recurso que você solicitou e da ação que você pretende executar.


<table border="1">
<tr><th>Cabeçalho</th><th>Tipo</th><th>Valor</th></tr>
<tr><td>ID da solicitação</td><td>Cadeia de caracteres</td><td>Um identificador exclusivo para a operação atual, serviço gerado.</td></tr>
<tr><td>ID da solicitação de cliente</td><td>Cadeia de caracteres</td><td>Um identificador especificado pelo chamador na solicitação original, se presente.</td></tr>
<tr><td>Data</td><td>Data do RFC 1123</td><td>A data em que a solicitação foi processada.</td></tr>
<tr><td>Tipo de conteúdo</td><td>Varia</td><td>O tipo de conteúdo do corpo da resposta.</td></tr>
<tr><td>Codificação de conteúdo</td><td>Varia</td><td>Gzip ou deflate, conforme apropriado.</td></tr>
</table><br/>

## Verbos HTTP padrão suportados pelos serviços de mídia.

A seguir está uma lista completa de verbos HTTP que podem ser usados quando fazem solicitações HTTP:


<table border="1">
<tr><th>Verbo</th><th>Descrição</th></tr>
<tr><td>GET</td><td>Retorna o valor atual de um objeto.</td></tr>
<tr><td>POST</td><td>Cria um objeto com base nos dados fornecidos ou envia um comando.</td></tr>
<tr><td>PUT</td><td>Substitui um objeto ou cria um objeto nomeado (quando aplicável).</td></tr>
<tr><td>EXCLUIR</td><td>Exclui um objeto.</td></tr>
<tr><td>MESCLAR</td><td>Atualiza um objeto existente com alterações de propriedade nomeada.</td></tr>
<tr><td>HEAD</td><td>Retorna metadados de um objeto para uma resposta GET.</td></tr>
</table><br/>

## Descobrindo o modelo de serviços de mídia

Para tornar as entidades de serviços de mídia mais detectáveis, a operação de $metadata pode ser usada. Ele permite que você recupere todos os tipos de entidade válidos, propriedades da entidade, associações, funções, ações e assim por diante. O exemplo a seguir mostra como construir o URI: https://media.windows.net/API/$metadata.

Você deve acrescentar "?api-version=2.x" ao final do URI se desejar exibir os metadados em um navegador, ou não incluir o cabeçalho x-ms-version na solicitação.


<!-- Anchors. -->


<!-- URLs. -->
  
  [Portal de Gerenciamento]: http://manage.windowsazure.com/




<!--HONumber=47-->
