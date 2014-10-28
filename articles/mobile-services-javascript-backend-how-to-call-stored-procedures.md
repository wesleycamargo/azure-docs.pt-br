<properties pageTitle="Calling SQL stored procedures with a JavaScript back end" metaKeywords="stored procedures, SQL, mobile devices, Azure" description="explains how to use SQL stored procedures in a mobile services JavaScript backed." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Calling SQL stored procedures with a JavaScript back end" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Chamando processos SQL armazenados com um back-end do JavaScript

Motivos para você desejar chamar procedimentos armazenados do SQL Server:

-   seu serviço móvel usa procedimentos armazenados existentes que você deve acessar
-   a arquitetura de seu aplicativo impõe o uso de procedimentos armazenados para acesso do banco de dados
-   preferência pessoal

Este tópico descreve como:

-   [Chamar um procedimento armazenado simples][Chamar um procedimento armazenado simples]
-   [Chamar um procedimento armazenado com parâmetros][Chamar um procedimento armazenado com parâmetros]
-   [Saiba mais][Saiba mais]

Este artigo aborda os procedimentos armazenados em um serviço móvel com um back-end do JavaScript.

No entanto, você pode em vez disso criar serviços móveis com um back-end do .Net, que tem uma arquitetura completamente diferente com base no Entity Framework. Para obter mais informações, consulte [Como o back-end do .NET dos Serviços Móveis do Azure funciona][Como o back-end do .NET dos Serviços Móveis do Azure funciona].

## Chamar um procedimento armazenado simples

**"Meu banco de dados usa procedimentos armazenados: como os chamo por meio de um serviço móvel?"**

Você faz isso com o [objeto mssql][objeto mssql], que permite executar as instruções *Transact-SQL* ("T-SQL") que chamam o procedimento.

### Gravando e testando o código Transact SQL

Suponha que o procedimento armazenado seja denominado *GetAll* e seu serviço móvel, **todolist**.

1.  Selecione o ícone **Bancos de dados** na barra lateral do portal do Azure e selecione seu banco de dados, cujo nome é o mesmo de seu serviço móvel, com o sufixo "**_db**".

2.  Na página Início rápido do banco de dados, pressione o link **Gerenciar** na parte inferior da página.

3.  Faça logon no banco de dados.

4.  Pressione **Nova Consulta** na barra superior e adicione o seguinte código:

            EXEC todolist.GetAll

5.  Clique em **Executar** e verifique os resultados.

Observe como o nome do procedimento tem o prefixo do nome do esquema do banco de dados, que por padrão é o nome de seu serviço móvel.

### Onde coloco a chamada para o objeto mssql?

Se precisar chamar o procedimento armazenado diretamente, a maneira mais flexível é gravar e chamar uma [API personalizada][API personalizada].

1.  No painel do serviço móvel, clique em **API** e em **CRIAR**, nomeie seu script ***getall*** e adicione esse código para chamar o procedimento armazenado:

        exports.get = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "EXEC todolist.GetAll";
            mssql.query(sql, {
                success: function(results) {                          
                        response.send(200, results); 
                }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

2.  Teste-o em um navegador colocando essa URL na linha de endereço:

        https://todolist.azure-mobile.net/api/getall

Em vez disso, você pode colocar um código similar em um script do servidor *ler*, *inserir*, *atualizar* ou *excluir* de uma tabela e seu código desviará do comportamento padrão e chamará o procedimento armazenado. O código similar pode ir dentro do script do *Agendador*. Entretanto, criar uma API personalizada é mais flexível.

### Chamando o código do cliente

Modifique o código do cliente para chamar o método **invokeApi** no objeto **MobileServiceClient**. A sintaxe do código exato é específica para seu dispositivo cliente e é explicada nos seguintes tópicos:

-   [C# da Windows Store][C# da Windows Store]
-   [JavaScript da Windows Store][JavaScript da Windows Store]
-   [Windows Phone][Windows Phone]
-   [iOS][iOS]
-   [Android][Android]
-   [HTML][HTML]

## <a name="parameters"></a>Chamar um procedimento armazenado com parâmetros

Vamos presumir que o procedimento armazenado seja denominado *ItemsByStatus*, que ele tenha um parâmetro único denominado *Status* e que desejamos chamá-lo usando uma API personalizada.

1.  Siga as etapas na seção anterior para gravar e testar o código T-SQL usando o portal do banco de dados do Azure, mas use esse código T-SQL em seu lugar:

        EXEc todolist.ItemsByStatus @Status = 1

2.  Clique no símbolo **Executar** e verifique os resultados.

3.  Como na etapa anterior, crie uma nova API personalizada denominada **completeall** com o seguinte código que chama o procedimento armazenado. Observe que no código abaixo, você substitui o valor real do parâmetro **@Status** que você usou no teste por **"?"**, que será preenchido no tempo de execução com o parâmetro fornecido.

        exports.get = function(request, response) { 
            var mssql = request.service.mssql;
            var param1 = parseInt(request.query.status);
            var sql = "EXEC todolist.ItemsByStatus @Status = ?";
            mssql.query(sql, [param1], {
                success: function(results) { 
                         response.send(200, results[0]); 
                    }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

4.  Teste a API com essa URL em um navegador:

        https://todolist.azure-mobile.net/api/completeall?status=1

5.  Chame a API do cliente conforme descrito na seção anterior.

### Assinaturas de parâmetro mais complexas

O procedimento armazenado na seção anterior tinha somente um parâmetro. Aqui está a sintaxe para usar para uma assinatura mais longa:

        function read(query, user, request) {
            var sql = 'EXEC MySProc @Param1 = ?, @Param2 = ?, @Param3 = ?';
            var param1 = request.parameters.first;
            var param2 = request.parameters.second;
            var param3 = request.parameters.third;
            mssql.query(sql, [param1, param2, param3], {
                success: function(results) {
                    request.respond(200, results);
                }
            });
        }

Observe que na instrução **EXEC** os parâmetros são chamados por nome em vez de pela posição. Essa é a sintaxe recomendada e isola seu código de alterações mínimas no procedimento armazenado, como alteração na ordem do parâmetro ou adição de parâmetros opcionais.

## <a name="more"></a>Saiba mais

Este tópico contém somente informações superficiais.

Para obter referências sobre o código usado, consulte estes tópicos:

-   -   

Veja aqui outros cenários que você pode encontrar:

-   [Como faço mais de uma operação de leitura por script][Como faço mais de uma operação de leitura por script] descreve como ler condicionalmente uma tabela ou, como alternativa, como chamar um procedimento armazenado. Esse blogger, Carlos Figueira, frequentemente posta sobre Serviços Móveis do Azure e bancos de dados.

-   [Acessando um procedimento armazenado por meio de um esquema diferente][Acessando um procedimento armazenado por meio de um esquema diferente] descreve soluções para problemas que você pode encontrar ao acessar procedimentos armazenados que residem em esquemas diferentes do mesmo banco de dados de serviços móveis

Você também pode usar o portal do Azure para gerenciar e criar novos procedimentos armazenados.

<!-- Anchors. -->
<!-- URLs. -->

  [Chamar um procedimento armazenado simples]: #simple
  [Chamar um procedimento armazenado com parâmetros]: #parameters
  [Saiba mais]: #more
  [Como o back-end do .NET dos Serviços Móveis do Azure funciona]: http://curah.microsoft.com/64518/how-the-azure-mobile-services-net-backend-works
  [objeto mssql]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554212.aspx
  [API personalizada]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn280974.aspx
  [C# da Windows Store]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [JavaScript da Windows Store]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-windows-store-javascript-call-custom-api/
  [Windows Phone]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-windows-phone-call-custom-api/
  [iOS]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-ios-call-custom-api/
  [Android]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-android-call-custom-api/
  [HTML]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-html-call-custom-api/
  [Como faço mais de uma operação de leitura por script]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/fccf4ae7-f43c-4c2d-8518-32e2df84a824/how-do-i-do-more-than-1-read-operation-by-scripting?forum=azuremobile
  [Acessando um procedimento armazenado por meio de um esquema diferente]: http://blogs.msdn.com/b/jpsanders/archive/2013/05/02/windows-azure-mobile-services-accessing-a-stored-procedure-from-a-different-schema.aspx
