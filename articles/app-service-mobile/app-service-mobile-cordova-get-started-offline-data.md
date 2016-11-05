---
title: Habilitar a sincronização offline para seu Aplicativo Móvel do Azure (Cordova) | Microsoft Docs
description: Aprenda a usar o Aplicativo Móvel do Serviço de Aplicativo para o cache e a sincronização de dados offline no aplicativo Cordova
documentationcenter: cordova
author: mikejo5000
manager: erikre
editor: ''
services: app-service\mobile

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 08/14/2016
ms.author: mikejo

---
# Habilitar a sincronização offline para seu aplicativo móvel Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Este tutorial apresenta o recurso de sincronização offline dos Aplicativos Móveis do Azure para o Cordova. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local; quando o dispositivo estiver online novamente, essas alterações são sincronizadas ao serviço remoto.

Este tutorial se baseia na solução de início rápido do Cordova para os Aplicativos Móveis criados quando você conclui o tutorial [Início rápido do Apache Cordova]. Neste tutorial, você atualizará a solução de início rápido para adicionar recursos offline dos Aplicativos Móveis do Azure. Também destacaremos o código especificamente offline no aplicativo.

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de dados offline nos Aplicativos Móveis do Azure]. Para obter detalhes sobre o uso da API, confira o arquivo [README] no plug-in.

## Adicionar a sincronização offline à solução de início rápido
O código da sincronização offline deve ser adicionado ao aplicativo. A sincronização offline requer o plug-in cordova-sqlite-storage, que é adicionado automaticamente ao seu aplicativo quando o plug-in dos Aplicativos Móveis do Azure está incluído no projeto. O projeto de Início Rápido inclui os dois plug-ins.

1. No Gerenciador de Soluções do Visual Studio, abra o index.js e substitua o código a seguir
   
        var client,            // Connection to the Azure Mobile App backend
          todoItemTable;      // Reference to a table endpoint on backend
   
    por este código:
   
        var client,             // Connection to the Azure Mobile App backend
          todoItemTable, syncContext; // Reference to table and sync context
2. Em seguida, substitua o código a seguir:
   
        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
   
    por este código:
   
        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
   
        // Note: Requires at least version 2.0.0-beta6 of the Azure Mobile Apps plugin
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');
   
        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              deleted: 'boolean',
              complete: 'boolean'
          }
        });
   
        // Get the sync context from the client
        syncContext = client.getSyncContext();
   
    As adições de código anteriores inicializam o armazenamento local e definem uma tabela local que corresponde aos valores da coluna usados no back-end do Azure. (Você não precisa incluir todos os valores da coluna nesse código.)
   
    Você obtém uma referência para o contexto de sincronização chamando **getSyncContext**. O contexto de sincronização ajuda a preservar as relações da tabela controlando e enviando por push as alterações em todas as tabelas que um aplicativo cliente modificou quando o **push** é chamado.
3. Atualize a URL do aplicativo para a URL do Aplicativo Móvel.
4. Em seguida, substitua este código:
   
        todoItemTable = client.getTable('todoitem'); // todoitem is the table name
   
    por este código:
   
        // todoItemTable = client.getTable('todoitem');
   
        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {
   
        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');
   
        syncContext.pushHandler = {
            onConflict: function (serverRecord, clientRecord, pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };
   
        // Call a function to perform the actual sync
        syncBackend();
   
        // Refresh the todoItems
        refreshDisplay();
   
        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);
   
    O código anterior inicializa o contexto de sincronização e, em seguida, chama getSyncTable (em vez de getTable) para obter uma referência para a tabela local.
   
    Esse código usa o banco de dados local para todas as operações da tabela para criar, ler, atualizar e excluir (CRUD).
   
    Este exemplo executa o tratamento de erros simples nos conflitos de sincronização. Um aplicativo real trataria os diversos erros, como condições de rede, conflitos do servidor e outros. Para obter exemplos de código, confira o [exemplo de sincronização offline].
5. Em seguida, adicione esta função para executar a sincronização real.
   
        function syncBackend() {
   
          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed
   
          });
   
          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }
   
    Você decide quando enviar as alterações para o back-end do Aplicativo Móvel chamando **push** no objeto **syncContext** usado pelo cliente. Por exemplo, você poderia adicionar uma chamada para **syncBackend** a um manipulador de eventos do botão no aplicativo, como um novo botão de Sincronização, ou poderia adicionar a chamada à função **addItemHandler** para sincronizar sempre que um novo item é adicionado.

## Considerações sobre a sincronização offline
No exemplo, o método **push** de **syncContext** apenas é chamado na inicialização do aplicativo na função de callback do logon. Em um aplicativo real, você também poderia disparar manualmente essa funcionalidade de sincronização ou quando o estado da rede muda.

Quando um pull é executado em uma tabela que tenha atualizações locais pendentes controladas pelo contexto, e essa operação de pull automaticamente disparará um envio por push de contexto anterior. Ao atualizar, adicionar e concluir os itens neste exemplo, você pode omitir a chamada explícita **push**, pois ela pode ser redundante (verifique primeiro o arquivo [README] para ver o status atual do recurso).

No código fornecido, todos os registros na tabela remota todoItem são consultados, mas também é possível filtrar os registros passando uma id de consulta e uma consulta para **push**. Para obter mais informações, consulte a seção *Sincronização Incremental* em [Sincronização de Dados Offline nos Aplicativos Móveis do Azure].

## (Opcional) Desabilitar a autenticação
Se você ainda não configurou a autenticação e não deseja configurar a autenticação antes da sincronização offline de teste, comente a função de callback para logon, mas deixe o código dentro dessa função sem comentário.

O código deve ter esta aparência após comentar as linhas de logon.

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Agora, o aplicativo será sincronizado com o back-end do Azure quando você o executar, em vez de quando fizer logon.

## Execute o aplicativo cliente
Com a sincronização offline agora habilitada, você pode executar o aplicativo cliente pelo menos uma vez em cada plataforma para preencher o banco de dados do armazenamento local. Posteriormente, você simulará um cenário offline e modificará os dados no armazenamento local enquanto o aplicativo estiver offline.

## (Opcional) Testar o comportamento da sincronização
Nesta seção, você modificará o projeto do cliente para simular um cenário offline usando uma URL de aplicativo inválida para o back-end. Quando você adicionar ou alterar itens de dados, essas alterações serão mantidas no armazenamento local, mas não sincronizadas com o armazenamento de dados back-end até que a conexão for restabelecida.

1. No Gerenciador de Soluções, abra o arquivo de projeto index.js e altere a URL do aplicativo para apontar para uma URL inválida, como a seguir:
   
        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');
2. Em index.html, atualize o elemento CSP `<meta>` com a mesma URL inválida.
   
        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">
3. Compile e execute o aplicativo cliente, e observe que uma exceção é registrada no console quando o aplicativo tenta sincronizar com o back-end após o logon. Os novos itens adicionados existirão apenas no armazenamento local até que possam ser enviados por push para o back-end móvel. O aplicativo cliente se comporta como se estivesse conectado ao back-end, dando suporte a todas as operações CRUD (criar, ler, atualizar e excluir).
4. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.
5. (Opcional) Use o Visual Studio para exibir a tabela de Banco de Dados SQL do Azure para ver se os dados no banco de dados do back-end não foram alterados.
   
    No Visual Studio, abra **Gerenciador de Servidores**. Navegue até o banco de dados em **Azure**->**Bancos de dados SQL**. Clique com o botão direito do mouse em seu banco de dados e selecione **Abrir no Gerenciador de Objetos do SQL Server**. Agora você pode navegar até sua tabela de banco de dados SQL e seu conteúdo.

## (Opcional) Testar a reconexão com seu back-end móvel
Nesta seção, você vai reconectar o aplicativo ao back-end móvel, que simula o aplicativo voltando ao estado online. Quando você fizer logon, os dados serão sincronizados com o back-end móvel.

1. Reabra o index.js e corrija a URL do aplicativo para apontar para a URL correta.
2. Reabra o index.html e corrija a URL do aplicativo no elemento CSP `<meta>`.
3. Recompile e execute o aplicativo cliente. O aplicativo tenta sincronizar com o back-end do aplicativo móvel após fazer logon. Verifique se nenhuma exceção está registrada no console de depuração.
4. (Opcional) Exiba os dados atualizados usando o Pesquisador de Objetos do SQL Server ou uma ferramenta REST, como o Fiddler. Observe que os dados foram sincronizados entre o banco de dados de back-end e o repositório local.
   
    Observe que os dados foram sincronizados entre o banco de dados e o armazenamento local e contém os itens que você adicionou enquanto seu aplicativo estava desconectado.

## Recursos adicionais
* [Sincronização de dados offline em Aplicativos Móveis do Azure]
* [Cobertura em nuvem: sincronização offline nos serviços móveis do Azure] \(observação: o vídeo está nos Serviços Móveis, mas a sincronização offline funciona de maneira semelhante nos Aplicativos Móveis do Azure)
* [Ferramentas do Visual Studio para o Apache Cordova]

## Próximas etapas
* Veja os recursos mais avançados de sincronização offline, como a resolução de conflitos no [exemplo de sincronização offline]
* Veja a referência da API de sincronização offline em [README]

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Início rápido do Apache Cordova]: app-service-mobile-cordova-get-started.md
[README]: https://github.com/Azure/azure-mobile-apps-js-client#offline-data-sync-preview
[exemplo de sincronização offline]: https://github.com/shrishrirang/azure-mobile-apps-quickstarts/tree/samples/client/cordova/ZUMOAPPNAME
[Sincronização de dados offline em Aplicativos Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Sincronização de dados offline nos Aplicativos Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Cobertura em nuvem: sincronização offline nos serviços móveis do Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Ferramentas do Visual Studio para o Apache Cordova]: https://www.visualstudio.com/pt-BR/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0824_2016-->