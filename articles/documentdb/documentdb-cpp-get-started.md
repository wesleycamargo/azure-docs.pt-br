---
title: Tutorial do NoSQL C++ para o DocumentDB | Microsoft Docs
description: "Um tutorial do NoSQL C++ que cria um aplicativo de banco de dados e console em C++ usando um SDK aprovado pelo DocumentDB para C++. O DocumentDB é um serviço de banco de dados NoSQL em escala mundial."
services: documentdb
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: 
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: aasthan
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 78c3da6fd83a6fca0351a90846d10acd82924be3
ms.lasthandoff: 04/18/2017


---
# <a name="nosql-c-tutorial-documentdb-c-console-application"></a>Tutorial do NoSQL C++: aplicativo de console em C++ do DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js para MongoDB](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 
 

Bem-vindo ao tutorial do C++ para o SDK aprovado pelo DocumentDB do Azure para C++! Após seguir este tutorial, você terá um aplicativo de console que cria e consulta recursos do DocumentDB, incluindo um banco de dados em C++.

Abordaremos:

* Criando e se conectando a uma conta do Banco de Dados de Documentos
* Configurando o aplicativo
* Criar um banco de dados do DocumentDB em C++
* Criar uma coleção
* Criando documentos JSON
* Consultar a coleção
* Substituição de um documento
* Exclusão de um documento
* Excluir o banco de dados do DocumentDB em C++

Você não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/stalker314314/DocumentDBCpp). Consulte [Obter a solução completa](#GetSolution) para as instruções rápidas.

Depois de concluir o tutorial do C++, use os botões de votação na parte inferior desta página para nos enviar comentários. 

Se quiser que entremos em contato com você diretamente, inclua seu endereço de email em seu comentário ou [entre em contato conosco aqui](https://www.research.net/r/8BKRJ3Z). 

Agora vamos começar!

## <a name="prerequisites-for-the-c-tutorial"></a>Pré-requisitos para o tutorial do C++
Certifique-se que você tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/), com os componentes de linguagem C++ instalados.

## <a name="step-1-create-a-documentdb-account"></a>Etapa 1: Criar uma conta do Banco de Dados de Documentos
Vamos criar uma conta de Banco de Dados de Documentos. Se você já tiver uma conta que deseja usar, poderá pular para [Configurar seu aplicativo em C++](#SetupNode).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupC++"></a>Etapa 2: configurar o aplicativo C++
1. Abra o Visual Studio e, no menu **Arquivo**, clique em **Novo** e clique em **Projeto**. 
2. Na janela **Novo Projeto**, no painel **Instalado**, expanda **Visual C++**, clique em **Win32** e em **Aplicativo de Console Win32**. Nomeie o projeto como hellodocumentdb e clique em **OK**. 
   
    ![Captura de tela do Assistente de novo projeto](media/documentdb-cpp-get-started/hellodocumentdb.png)
3. Quando o Assistente de Aplicativo Win32 for iniciado, clique em **Concluir**.
4. Quando o projeto tiver sido criado, abra o gerenciador de pacotes NuGet clicando com o botão direito do mouse no projeto **hellodocumentdb** no **Gerenciador de Soluções** e clicando em **Gerenciar Pacotes NuGet**. 
   
    ![Captura de tela mostrando Gerenciar Pacote NuGet no menu projeto](media/documentdb-cpp-get-started/nuget.png)
5. Na guia **NuGet: hellodocumentdb**, clique em **Procurar** e pesquise *documentdbcpp*. Nos resultados, selecione DocumentDbCPP, conforme mostrado na captura de tela a seguir. Este pacote instala referências ao SDK REST C++, que é uma dependência para o DocumentDbCPP.  
   
    ![Captura de tela mostrando o pacote DocumentDbCpp realçado](media/documentdb-cpp-get-started/documentdbcpp.png)
   
    Depois que os pacotes forem adicionados ao projeto, estaremos prontos para começar a escrever código.   

## <a id="Config"></a>Etapa 3: copiar detalhes da conexão do portal do Azure para o banco de dados do DocumentDB
Exiba o [portal do Azure](https://portal.azure.com) e vá para a conta de banco de dados NoSQL (DocumentDB) que você criou. Serão necessários o URI e a chave primária no portal do Azure na próxima etapa para estabelecer uma conexão do trecho de código C++. 

![URI do DocumentDB e chaves no portal do Azure](media/documentdb-cpp-get-started/nosql-tutorial-keys.png)

## <a id="Connect"></a>Etapa 4: conectar-se a uma conta do DocumentDB
1. Adicione os cabeçalhos e os namespaces a seguir ao código-fonte após `#include "stdafx.h"`.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. Em seguida, adicione o seguinte código à função principal e substitua a configuração da conta e a chave primária para corresponder às configurações do DocumentDB da etapa 3. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    Agora que você tem o código para inicializar o cliente do banco de dados de documentos, veremos como trabalhar com os recursos do Banco de Dados de Documentos.

## <a id="CreateDBColl"></a>Etapa 5: criar um banco de dados em C++ e uma coleção
Antes de executar esta etapa, vamos ver como um banco de dados, uma coleção e documentos interagem para aqueles que são iniciantes no DocumentDB. Um [banco de dados](documentdb-resources.md#databases) é um contêiner lógico de armazenamento de documentos particionado em coleções. Uma [coleção](documentdb-resources.md#collections) é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. Você pode aprender mais sobre conceitos e o modelo de recurso hierárquico do DocumentDB em [Conceitos e modelo de recurso hierárquico do DocumentDB](documentdb-resources.md).

Para criar um banco de dados e uma coleção correspondente, adicione o código a seguir ao fim da função principal. Isso cria um banco de dados chamado 'FamilyRegistry' e uma coleção chamada 'FamilyCollection' usando a configuração do cliente declarada na etapa anterior.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>Etapa 6: Criar um documento
Os [documentos](documentdb-resources.md#documents) são conteúdo JSON (arbitrário) definido pelo usuário. Agora você pode inserir um documento no Banco de Dados de Documentos. Você pode criar um documento copiando o código a seguir para o fim da função principal. 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

Para resumir, esse código cria um banco de dados do DocumentDB, uma coleção e documentos, que podem ser consultados no Gerenciador de Documentos no portal do Azure. 

![Tutorial do C++ - diagrama que ilustra a relação hierárquica entre a conta, o banco de dados, a coleção e os documentos](media/documentdb-cpp-get-started/documentdbdocs.png)

## <a id="QueryDB"></a>Etapa 7: Recursos de consulta do Banco de Dados de Documentos
O Banco de Dados de Documentos tem suporte para [consultas](documentdb-sql-query.md) avançadas de documentos JSON armazenados em cada coleção. O código de exemplo a seguir mostra uma consulta feita usando a sintaxe SQL do DocumentDB que pode ser executada em relação aos documentos criado na etapa anterior.

A função usa como argumentos a identificação exclusiva ou a id do recurso do banco de dados e a coleção, juntamente com o cliente do documento. Adicione esse código antes da função principal.

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Replace"></a>Etapa 8: substituir um documento
O DocumentDB dá suporte à substituição de documentos JSON, conforme demonstrado no código a seguir. Adicione este código após a função executesimplequery.

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a id="Delete"></a>Etapa 9: excluir um documento
O DocumentDB dá suporte à exclusão de documentos JSON. Você pode fazer isso copiando e colando o código a seguir após a função replacedocument. 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="DeleteDB"></a>Etapa 10: excluir um banco de dados
Excluir o banco de dados criado remove o banco de dados e todos os recursos filho (coleções, documentos etc.).

Copie e cole o trecho de código a seguir (função cleanup) após a função deletedocument para remover o banco de dados e todos os recursos filho.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Run"></a>Etapa 11: executar o aplicativo C++ em conjunto!
Agora adicionamos código para criar, consultar, modificar e excluir recursos diferentes do DocumentDB.  Agora vamos conectar tudo isso adicionando chamadas para essas diferentes funções da função principal em hellodocumentdb.cpp, juntamente com algumas mensagens de diagnóstico.

Você pode fazer isso substituindo a função principal do aplicativo pelo código a seguir. Isso substitui account_configuration_uri e primary_key que você copiou para o código na etapa 3. Então, salve essa linha ou copie os valores novamente do portal. 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

Agora você deve poder compilar e executar o código no Visual Studio pressionando F5 ou, como alternativa, na janela do terminal, localizando o aplicativo e executando o arquivo executável. 

Você deverá ver a saída do aplicativo iniciado. A saída deve corresponder à captura de tela a seguir.

![Saída do aplicativo C++ do DocumentDB](media/documentdb-cpp-get-started/docdbconsole.png)

Parabéns! Você concluiu o tutorial do C++ e tem seu primeiro aplicativo de console do DocumentDB!

## <a id="GetSolution"></a>Obter a solução completa do tutorial de C++
Para criar a solução de Introdução que contém todos os exemplos neste artigo, você precisa do seguinte:

* [Conta de DocumentDB][documentdb-create-account].
* A solução [GetStarted](https://github.com/stalker314314/DocumentDBCpp) disponível no GitHub.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [monitorar uma conta do Banco de Dados de Documentos](documentdb-monitor-accounts.md).
* Executar consultas em nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Saiba mais sobre o modelo de programação na seção Desenvolvimento da [Página de documentação do Banco de Dados de Documentos](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md



