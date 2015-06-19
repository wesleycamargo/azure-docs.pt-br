<properties 
	pageTitle="Interações RESTful com recursos do Banco de Dados de Documentos | Azure" 
	description="Saiba como executar as interações RESTful com os recursos do Banco de Dados de Documentos do Microsoft Azure usando verbos HTTP." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="mimig"/>

#Interações RESTful com recursos do Banco de Dados de Documentos 

O Banco de Dados de Documentos dá suporte ao uso de métodos HTTP para criar, ler, substituir, obter e excluir recursos do Banco de Dados de Documentos.

Após ler este artigo, você poderá responder as perguntas a seguir:

- Como funcionam os métodos HTTP padrão com recursos do Banco de Dados de Documentos?
- Como criar um novo recurso usando POST?
- Como registrar um procedimento armazenado usando POST?
- Como o Banco de Dados de Documentos dá suporte ao controle de moeda?
- Quais são as opções de conectividade para HTTPS e TCP?

##Visão geral de verbos HTTP
Os recursos do Banco de Dados de Documentos dão suporte aos seguintes verbos HTTP com sua interpretação padrão:

1.	POST significa criar um novo recurso de item. 
2.	GET significa ler um item existente ou um recurso de feed 
3.	PUT significa substituir um recurso de item existente 
4.	DELETE significa excluir um item de recurso existente
5.	HEAD significa GET sem a carga de resposta (ou seja, apenas os cabeçalhos) 

>[AZURE.NOTE] No futuro, pretendemos adicionar suporte para atualizações seletivas via PATCH.  

Conforme ilustrado no diagrama abaixo, POST só pode ser executado em um recurso feed; PUT e DELETE só podem ser executados em um recurso Item; GET e HEAD podem ser executados em recursos feed ou item. 

![][1]  

**Modelo de interação usando os métodos HTTP padrão**

##Criar um novo recurso usando POST 
Para entender melhor o modelo de interação, vamos considerar o caso de criar um novo recurso (também conhecido como INSERT). Para criar um novo recurso, você precisa emitir uma solicitação HTTP POST com o corpo da solicitação contendo a representação do recurso em relação ao URI do feed do contêiner ao qual o recurso pertence. A única propriedade exigida para a solicitação é a ID do recurso.  

Como exemplo, para criar um novo banco de dados, você usa POST em um recurso do banco de dados (configurando a propriedade de ID com um nome exclusivo) no /dbs. Do mesmo modo, para criar uma nova coleção, você usa POST em um recurso da coleção no /dbs/_rid/colls/ e assim por diante. A resposta contém o recurso totalmente confirmado com as propriedades geradas pelo sistema, incluindo o link _self do recurso usando o que você pode navegar para outros recursos. Como exemplo do modelo de interação baseado no HTTP simples, um cliente pode emitir uma solicitação HTTP para criar um novo banco de dados dentro de uma conta.  

	POST http://fabrikam.documents.azure.net/dbs
	{
	      "id":"MyDb"
	}

O serviço do Banco de Dados de Documentos responde com uma resposta de êxito e um código de status indicando a criação bem-sucedida do banco de dados.  

	[201 Created]
	{
	      "id": "MyDb",
	      "_rid": "UoEi5w==",
	      "_self": "dbs/MyDb/",
	      "_ts": 1407370063,
	      "_etag": ""00002100-0000-0000-0000-50e71fda0000"",
	      "_colls": "colls/",
	      "_users": "users/"
	}
  
##Registrar um procedimento armazenado usando POST
Como outro exemplo de criação e execução de um recurso, considere o seguinte procedimento armazenado gravado inteiramente em JavaScript.   

	function sproc(docToCreate, addedPropertyName, addedPropertyValue) {
	    var collectionManager = getContext().getCollection();
	    collectionManager.createDocument(collectionManager.getSelfLink(), docToCreate,   
	    function(err, docCreated) {
	        if(err) throw new Error('Error while creating document: ' + err.message);
	        
	        docCreated.addedPropertyName = addedPropertyValue;
	        getContext().getResponse().setBody(docCreated);
	    });
	}

O procedimento armazenado pode ser registrado em uma coleção em MyDb executando um POST no /dbs/_rid-db/colls/_rid-coll/sprocs. 

	POST /dbs/MyDb/colls/MyColl/sprocs HTTP/1.1
	
	{
	  "id": "sproc1",
	  "body": "function (docToCreate, addedPropertyName, addedPropertyValue) {
	                var collectionManager = getContext().getCollection();
	                collectionManager.createDocument(collectionManager.getSelfLink(), 
	                            docToCreate, function(err, docCreated) {
	                    if(err) throw new Error('Error while creating document: ' + 
	                                                     err.message);
	                    
	                    docCreated.addedPropertyName = addedPropertyValue;
	                    getContext().getResponse().setBody(docCreated);
	                });
	            }"
	}
O serviço do Banco de Dados de Documentos responde com uma resposta de êxito e um código de status indicando o registro bem-sucedido do procedimento armazenado.  

	[201 Created]
	{
	      "id": "sproc1",
	      "_rid": "EoEi5w==",
	      "_self": "dbs/MyDb/colls/MyColl/sprocs/sproc1",
	      "_etag": ""00002100-0000-0000-0000-50f71fda0000"",
	       ...
	}

##Executar um procedimento armazenado usando POST
Por fim, para executar o procedimento armazenado no exemplo acima, é necessário executar um POST no URI do recurso do procedimento armazenado (/dbs/_rid-db/colls/_rid-coll/sprocs/sproc1). Isso é ilustrado no código a seguir.  

	POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
	 [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]
 

O serviço do Banco de Dados de Documentos dá a resposta a seguir.  

	HTTP/1.1 200 OK
	 { 
	  "id": "TestDocument",  
	  "_rid": "ZTlcANiwqwIBAAAAAAAAAA==",
	  "_ts": 1407370063,
	  "_self": "dbs/ZTlcAA==/colls/ZTlcANiwqwI=/docs/ZTlcANiwqwIBAAAAAAAAAA==/",
	  "_etag": "00000900-0000-0000-0000-53e2c34f0000",
	  "_attachments": "attachments/",
	  "book": "Autumn of the Patriarch", 
	  "price": 200
	}

Observe que o verbo POST é usado para criação de um novo recurso, para executar um procedimento armazenado e para emitir uma consulta SQL. Para ilustrar a execução da consulta SQL, considere o seguinte.  

	POST /dbs/MyDb/colls/MyColl/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/sql
	
	SELECT * FROM root.children

O serviço responde com os resultados da consulta SQL.   

	HTTP/1.1 200 Ok
	x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
	x-ms-item-count: 2
	x-ms-session-token: 81
	x-ms-request-charge: 1.43
	Content-Length: 287
	
	{"_rid":"sehcAIE2Qy4=","Documents":[[{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}],[{"familyName":"Merriam","givenName":"Jesse","gender":"female","grade":1},{"familyName":"Miller","givenName":"Lisa","gender":"female","grade":8}]],"count":2}


##Usando PUT, GET e DELETE
Substituir ou ler uma quantidade de recursos para executar verbos PUT (com um corpo de solicitação válido) e GET no link _self do recurso, respectivamente. Do mesmo modo, excluir uma quantidade de recursos para executar um verbo DELETE no link _self do recurso. É válido destacar que a organização hierárquica de recursos no modelo de recursos do Banco de Dados de Documentos necessita de suporte para exclusões em cascata, em que a exclusão do recurso proprietário causa a exclusão dos recursos dependentes. Os recursos dependentes podem ser distribuídos em outros nós diferentes dos recursos proprietários e, desse modo, a exclusão pode ocorrer lentamente. Independentemente da mecânica da coleta de lixo, ao excluir um recurso, a cota é liberada instantaneamente e fica disponível para uso. Observe que a integridade referencial é preservada pelo sistema. Por exemplo, você não pode inserir uma coleção em um banco de dados que é excluído ou substituído nem consultar um documento de uma coleção que não existe mais.  
 
A execução de um GET em um feed de recursos ou a consulta de uma coleção pode, potencialmente, resultar em milhões de itens, tornando impraticável para o servidor materializá-los e para os clientes consumi-los como parte de uma única viagem de ida e volta/solicitação e troca de resposta. Para lidar com isso, o Banco de Dados de Documentos permite aos clientes paginar pelo feed grande com uma página por vez. Os clientes podem usar o cabeçalho de resposta [x-ms-continuationToken] como um cursor para navegar até a próxima página.   

##Controle de simultaneidade otimista
A maioria dos aplicativos Web confia na marca da entidade baseada no controle de simultaneidade otimista para evitar os problemas infames de "Atualização Perdida" e "Exclusão Perdida". A marca da entidade é um carimbo de data/hora lógico e amigável para HTTP, associado a um recurso. O Banco de Dados de Documentos suporta nativamente o controle de concorrência otimista, garantindo que cada resposta HTTP contenha a versão (permanente) associada ao recurso específico. Os conflitos de controle de concorrência são detectados corretamente para os seguintes casos:  

1.	Se dois clientes emitirem simultaneamente solicitações mutantes (através dos verbos PUT/DELETE) em um recurso com a versão mais recente do recurso (especificada através do cabeçalho da solicitação [if-match]), o mecanismo do Banco de Dados de Documentos os sujeita ao controle de concorrência transacional.
2.	Se um cliente apresentar uma versão mais antiga do recurso (especificada pelo cabeçalho da solicitação [if-match]), a solicitação será rejeitada.  

##Opções de conectividade
O Banco de Dados de Documentos expõe um modelo de endereçamento lógico, em que cada recurso tem um URI lógico e estável identificado pelo link _self. Como um sistema de armazenamento distribuído espalha-se pelas regiões, os recursos em várias contas do banco de dados no Banco de Dados de Documentos são particionados em inúmeros computadores e cada partição é replicada para alta disponibilidade. As réplicas que gerenciam os recursos de uma determinada partição registram endereços físicos. Enquanto os endereços físicos mudam com o tempo devido a falhas, seus endereços lógicos permanecem estáveis e constantes. A conversão do endereço lógico em físico é mantida em uma tabela de roteamento que também está disponível internamente como um recurso. O Banco de Dados de Documentos expõe dois modos de conectividade:  

1.	**Modo de Gateway:** os clientes ficam protegidos contra a conversão entre endereços lógicos e físicos ou os detalhes do roteamento. Eles lidam somente com URIs lógicos e navegam com RESTful no modelo de recurso. Os clientes emitem as solicitações usando URI lógico e as máquinas de borda convertem o URI lógico para o endereço físico da réplica que gerencia o recurso e encaminha a solicitação. Com as máquinas de borda armazenando em cache a tabela de roteamento (atualizada periodicamente), o roteamento é extremamente eficiente. 
2.	**Modo de Conectividade Direto:** os clientes gerenciam diretamente a tabela de roteamento em seu espaço de processo e a atualizam periodicamente. O cliente pode conectar-se às réplicas diretamente e ignorar as máquinas de borda.   


<table width="300">
    <tbody>
        <tr>
            <td width="120" valign="top">
                <p>
                    <strong>Modo de conectividade</strong>
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    <strong>Protocolo</strong>
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    <strong>Detalhes</strong>
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    <strong>SDKs do Banco de Dados de Documentos</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    Gateway
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    Os aplicativos se conectam diretamente aos nós de borda usando URIs lógicos. Isso implica um salto de rede extra.
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    APIs REST
                </p>
                <p>
                    .NET, JavaScript, Node.js, Python
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    Conectividade direta
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS e
                </p>
                <p>
                    TCP
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    Os aplicativos podem acessar diretamente a tabela de roteamento e executar o roteamento do lado do cliente para se conectar diretamente às réplicas.
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    .NET
                </p>
            </td>
        </tr>
    </tbody>
</table>

##Próximas etapas
Explore a [referência da API REST do Banco de Dados de Documentos do Azure](https://msdn.microsoft.com/library/azure/dn781481.aspx) para saber mais sobre como trabalhar com recursos usando a API REST.

##Referências
-   [Referência da API REST do Banco de Dados de Documentos do Azure](https://msdn.microsoft.com/library/azure/dn781481.aspx) 
-	REST [http://en.wikipedia.org/wiki/Representational_state_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
-	Especificação JSON   [http://www.ietf.org/rfc/rfc4627.txt](http://www.ietf.org/rfc/rfc4627.txt)
-	Especificação HTTP [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
-	Marcas da entidade [http://en.wikipedia.org/wiki/HTTP_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)
-	[Consulta ao Banco de Dados de Documentos](documentdb-sql-query.md)
-	[Referência SQL do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn782250.aspx)
-	[Programando o Banco de Dados de Documentos: Procedimentos armazenados, gatilhos e UDFs](documentdb-programming.md)
-	[Documentação de referência do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781482.aspx)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png

<!--HONumber=49--> 