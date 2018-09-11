---
title: Configurar o pipeline de CI/CD com a tarefa de build do emulador do Azure Cosmos DB
description: Tutorial sobre como configurar o fluxo de trabalho de build e versão usando o VSTS (Visual Studio Team Services) com a tarefa de build do emulador do Cosmos DB
services: cosmos-db
keywords: Emulador do Azure Cosmos DB
author: deborahc
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 8/28/2018
ms.author: dech
ms.openlocfilehash: 37bb43435c34f14145b3642aa12c5cb0f16d780c
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783666"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-visual-studio-team-services"></a>Configurar um pipeline de CI/CD com a tarefa de build do emulador do Azure Cosmos DB no Visual Studio Team Services

O emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço do Azure Cosmos DB para fins de desenvolvimento. O emulador permite desenvolver e testar seu aplicativo localmente sem criar uma assinatura do Azure ne, incorrer em custos. 

A tarefa de build do emulador do Azure Cosmos DB para o VSTS (Visual Studio Team Services) permite que você faça o mesmo em um ambiente de CI. Com a tarefa de build, você pode executar testes no emulador como parte dos seus fluxos de trabalho de build e versão. A tarefa gira um contêiner do Docker com o emulador já em execução e fornece um ponto de extremidade que pode ser usado pelo restante da definição de build. Você pode criar e iniciar quantas instâncias do emulador precisar, cada uma executando em um contêiner separado. 

Este artigo demonstra como configurar um pipeline de CI do VSTS para um aplicativo ASP.NET que usa a tarefa de build do emulador do Cosmos DB para executar testes. 

## <a name="install-the-emulator-build-task"></a>Instalar a tarefa de build do emulador

Para usar a tarefa de build, primeiro precisamos instalá-la em nossa organização do VSTS. Localize a extensão **Emulador do Azure Cosmos DB** no [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) e clique em **Obter gratuitamente.**

![Localizar e instalar a tarefa de build do Emulador do Azure Cosmos DB no Marketplace do VSTS](./media/tutorial-setup-ci-cd/addExtension_1.png)

Em seguida, escolha a organização na qual instalar a extensão. 

> [!NOTE]
> Para instalar uma extensão para uma organização do VSTS, você deve ser um proprietário da conta ou administrador de coleção de projeto. Se você não tiver permissões, mas for um membro da conta, poderá solicitar as extensões em vez disso. [Saiba mais.](https://docs.microsoft.com/vsts/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions) 

![Escolha uma organização do VSTS na qual instalar uma extensão](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Criar a definição de build

Observe que a extensão está instalada, precisamos adicioná-la a uma [definição de build.](https://docs.microsoft.com/vsts/pipelines/get-started-designer?view=vsts&tabs=new-nav) Você pode modificar uma definição de build existente ou criar uma nova. Se você já tiver uma definição de build, poderá pular para [Adicionar a tarefa de build do Emulador para uma definição de build](#addEmulatorBuildTaskToBuildDefinition).

Para criar uma nova definição de build, navegue até a guia **Build e Versão** no VSTS. Selecione **+Novo.**

![Criar uma nova definição de build](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png) Selecione o projeto de equipe, o repositório e o branch desejados para habilitar builds. 

![Selecione o projeto de equipe, o repositório e o branch para a definição de build ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

Por fim, selecione o modelo desejado para a definição de build. Vamos selecionar o modelo do **ASP.NET** neste tutorial. 

![Selecione o modelo de definição de build desejado ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_3.png)

Agora temos uma definição de build que podemos configurar para usar a tarefa de build do emulador do Azure Cosmos DB que se parece com aquela abaixo. 

![Modelo de definição de build do ASP.NET](./media/tutorial-setup-ci-cd/CreateNewBuildDef_4.png)

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>Adicionar a tarefa a uma definição de build

Para adicionar a tarefa de build do emulador, pesquise **cosmos** na caixa de pesquisa e selecione **Adicionar.** A tarefa de build inicializará um contêiner com uma instância do emulador do Cosmos DB já em execução, de modo que para que a tarefa precisa ser colocada antes de quaisquer outras tarefas que esperam o que emulador esteja em execução.

![Adicionar a tarefa de build do Emulador à definição de build](./media/tutorial-setup-ci-cd/addExtension_3.png) Neste tutorial, vamos adicionar a tarefa ao início da fase 1 para garantir que o emulador esteja disponível antes da execução dos testes.
A definição de build concluída agora se parece com isto. 

![Modelo de definição de build do ASP.NET](./media/tutorial-setup-ci-cd/CreateNewBuildDef_5.png)

## <a name="configure-tests-to-use-the-emulator"></a>Configurar testes para usar o emulador
Agora, vamos configurar nossos testes para usar o emulador. A tarefa de build do emulador exporta uma variável de ambiente – 'CosmosDbEmulator.Endpoint' – para a qual qualquer tarefa no pipeline de build pode emitir solicitações. 

Neste tutorial, vamos usar a [tarefa de Teste do Visual Studio](https://github.com/Microsoft/vsts-tasks/blob/master/Tasks/VsTestV2/README.md) para executar testes de unidade configuradas por meio de um arquivo **.RunSettings**. Para saber mais sobre a configuração do teste de unidade, leia a [documentação](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017).

Abaixo está um exemplo de um arquivo **.RunSettings** que define os parâmetros a serem passados para os testes de unidade do aplicativo. Observe que a variável `authKey` usada é a [chave bem conhecida](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests) para o emulador. Esta `authKey` é a chave esperada pela tarefa de build do emulador e deve ser definida em seu arquivo **.RunSettings**.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```
Estes parâmetros `TestRunParameters` são referenciados por meio de uma propriedade `TestContext` no projeto de teste do aplicativo. Aqui está um exemplo de um teste executado no Cosmos DB. 

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Navegue até as Opções de Execução na tarefa de Teste do Visual Studio. Na opção **Arquivo de configurações**, especifique que os testes são configurados usando o arquivo **.RunSettings**. Na opção **Substituir parâmetros de execução de teste**, adicione ` -endpoint $(CosmosDbEmulator.Endpoint)`. Fazer isso configurará a tarefa de Teste para referir-se ao ponto de extremidade da tarefa de build do emulador, em vez daquele definido no arquivo **.RunSettings**.  

![Substituir a variável de ponto de extremidade com o ponto de extremidade de tarefa de build do Emulador](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>Executar a compilação
Agora, salvar e coloque o build na fila. 

![Salvar e executar o build](./media/tutorial-setup-ci-cd/runBuild_1.png)

Depois que o build tiver sido iniciado, observe que a tarefa do emulador do Cosmos DB começou a obter a imagem do Docker com o emulador instalado. 

![Salvar e executar o build](./media/tutorial-setup-ci-cd/runBuild_4.png)

Depois que o build for concluído, observe que seus testes são aprovados, todos em execução no emulador do Cosmos DB da tarefa de build!

![Salvar e executar o build](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o emulador para teste e desenvolvimento locais, veja [Usar o emulador do Azure Cosmos DB para desenvolvimento e teste locais](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Para exportar certificados SSL do emulador, veja [Exportar os certificados do Emulador do Azure Cosmos DB para uso com Java, Python e Node.js](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)
