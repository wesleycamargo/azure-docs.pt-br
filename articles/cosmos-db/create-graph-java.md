---
title: Compilar um aplicativo Java do BD Cosmos do Azure usando a API do Graph | Microsoft Docs
description: "Apresenta um exemplo de código Java que pode ser usado para, usando o Gremlin, conectar-se aos dados de gráfico no BD Cosmos do Azure e consultá-los."
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/14/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 8eac406c6ef96d7ae8dd5f4931c7d16edb723be8
ms.contentlocale: pt-br
ms.lasthandoff: 07/17/2017

---
# <a name="azure-cosmos-db-build-a-java-application-using-the-graph-api"></a>BD Cosmos do Azure: compilar um aplicativo Java usando a API do Graph

O BD Cosmos do Azure é o serviço multimodelo de banco de dados distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do BD Cosmos do Azure. 

Este início rápido demonstra como criar uma conta do BD Cosmos do Azure para a API do Graph (versão prévia), um banco de dados e um gráfico usando o Portal do Azure. Em seguida, você compila e executa um aplicativo de console usando o driver [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) do OSS.  

## <a name="prerequisites"></a>Pré-requisitos

* Antes que possa executar esta amostra, você deverá ter os seguintes pré-requisitos:
   * JDK 1.7+ (Execute `apt-get install default-jdk` se você não tiver o JDK), e configure variáveis de ambiente como `JAVA_HOME`
   * Maven (execute `apt-get install maven` se você não tiver o Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Crie uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um gráfico

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, clonaremos um aplicativo da API do Graph (versão prévia) do GitHub, definiremos a cadeia de conexão e o executaremos. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra uma janela de terminal do Git, como git bash, e `cd` para um diretório de trabalho.  

2. Execute o comando a seguir para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Examine o código

Façamos uma rápida análise do que está acontecendo no aplicativo. Abra o arquivo `Program.java` e você descobrirá que essas linhas de código. 

* O `Client` Gremlin é inicializado da configuração em `src/remote.yaml`.

    ```java
    Cluster cluster = Cluster.build(new File("src/remote.yaml")).create();
    
    Client client = cluster.connect();
    ```

* Uma série de etapas do Gremlin são executadas usando o método `client.submit`.

    ```java
    ResultSet results = client.submit("g.V()");

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```
## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

1. Abra o arquivo src/remote.yaml. 

3. Preencha as configurações de *host*, *porta*, *nome de usuário*, *senha*, *connectionPool* e *serializador* no arquivo src/remote.yaml:

    Configuração|Valor sugerido|Descrição
    ---|---|---
    Hosts|[***.graphs.azure.com]|Confira a captura de tela abaixo. Esse é o valor de URI Gremlin na página Visão geral do portal do Azure, entre colchetes, com :443 / à direita removido.<br><br>Esse valor também pode ser recuperado da guia de chaves usando o valor de URI removendo https://, alterando os documentos para gráficos e removendo :443/ à direita.
    Porta|443|Definir para 443.
    Nome de Usuário|*Seu nome de usuário*|O recurso do formulário `/dbs/<db>/colls/<coll>` onde `<db>` é o nome do banco de dados e `<coll>` é o nome da coleção.
    Senha|*Sua chave mestra principal*|Confira a segunda captura de tela abaixo. Esta é sua chave primária, que você pode recuperar da página de chaves do portal do Azure, na caixa de chave Primária. Use o botão de cópia no lado esquerdo da caixa para copiar o valor.
    ConnectionPool|{enableSsl: true}|Sua configuração do pool de conexão para SSL.
    serializador|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Defina para esse valor e exclua todas as quebras de linha `\n` ao colar o valor.

    Para obter o valor de Hosts, copie o valor do **URI Gremlin** na página **Visão geral**: ![Exibir e copiar o valor do URI Gremlin na página de Visão geral no portal do Azure](./media/create-graph-java/gremlin-uri.png)

    Para obter o valor da Senha, copie a **Chave primária** na página **Chaves**: ![Exiba e copie sua chave primária no portal do Azure, página de chaves](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>Execute o aplicativo de console

1. Executar `mvn package` em um terminal para instalar os pacotes Java.

2. Execute `mvn exec:java -D exec.mainClass=GetStarted.Program` em um terminal para iniciar o aplicativo Java.

Agora, é possível voltar ao Data Explorer e ver a consulta, modificar e trabalhar com esses novos dados. 

## <a name="browse-using-the-data-explorer"></a>Navegar usando o Data Explorer

Agora você pode voltar ao Data Explorer no Portal do Azure e procurar e consultar seus novos dados de gráfico.

* No Data Explorer, o novo banco de dados aparece no painel Coleções. Expanda **graphdb**, **graphcoll** e, em seguida, clique em **Gráfico**.

    Os dados gerados pelo aplicativo de exemplo são exibidos no painel Gráficos.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar usando este aplicativo, exclua todos os recursos criados por esse início rápido no portal do Azure com as seguintes etapas: 

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do BD Cosmos do Azure, como criar um gráfico usando o Data Explorer e como executar um aplicativo. Agora, você pode criar consultas mais complexas e implementar uma lógica de passagem de gráfico avançada usando o Gremlin. 

> [!div class="nextstepaction"]
> [Consultar usando o Gremlin](tutorial-query-graph.md)


