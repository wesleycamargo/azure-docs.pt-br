---
title: Tutorial – Atualizar sortimento de estoque de varejo usando canais de publicação/assinatura e filtros de tópicos com o portal do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá como enviar e receber mensagens de um tópico e uma assinatura e como adicionar e usar regras de filtro usando .NET
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 05/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 2496c931da734c019801e3fa6deee127d8e571df
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697046"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Tutorial: Atualizar estoque usando o portal do Azure e tópicos/assinaturas

O Barramento de Serviço do Microsoft Azure é um serviço de mensagens na nuvem multilocatário que envia informações entre aplicativos e serviços. Operações assíncronas oferecem um sistema de mensagens agenciado e flexível, juntamente com recursos de mensagens PEPS (primeiro a entrar, primeiro a sair) e de publicação/assinatura. Este tutorial mostra como usar tópicos e assinaturas do Barramento de Serviço em um cenário de estoque de varejo com canais de publicação/assinatura usando o portal do Azure e .NET.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um tópico de Barramento de Serviço e uma ou mais assinaturas para esse tópico usando o portal do Azure
> * Adicionar filtros de tópicos usando código .NET
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar se chegaram nas assinaturas esperadas
> * Receber mensagens das assinaturas

Um exemplo desse cenário é uma atualização de sortimento de estoque para várias lojas de varejo. Nesse cenário, cada loja ou conjunto de lojas recebe mensagens que são destinadas para atualizar os respectivos sortimentos. Este tutorial mostra como implementar esse cenário usando assinaturas e filtros. Primeiro, você cria um tópico com 3 assinaturas, adiciona algumas regras e filtros e envia e recebe mensagens do tópico e das assinaturas.

![topic](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita][] antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem o seguinte instalado:

- [Atualização 3 do Visual Studio 2017 (versão 15.3, 26730.01)](http://www.visualstudio.com/vs) ou posterior.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

## <a name="service-bus-topics-and-subscriptions"></a>Tópicos e assinaturas do Barramento de Serviço

Cada [assinatura a um tópico](service-bus-messaging-overview.md#topics) pode receber uma cópia de cada mensagem. Os tópicos tem total compatibilidade de protocolo e semântica com filas do Barramento de Serviço. Os tópicos do Barramento de Serviço dão suporte a uma ampla matriz de regras de seleção com condições de filtro, com ações opcionais que definem ou modificam as propriedades da mensagem. Cada vez que uma regra corresponde, é produzida uma mensagem. Para saber mais sobre regras, filtros e ações, consulte este [link](topic-filters.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Primeiro, vá para o [portal do Azure][Azure portal] e entre usando a assinatura do Azure. A primeira etapa é criar um namespace de Barramento de Serviço do tipo **Mensagens**.

## <a name="create-a-service-bus-namespace"></a>Criar um namespace do Barramento de Serviço

Um namespace de mensagens do Barramento de Serviço fornece um contêiner de escopo exclusivo, referenciado pelo [nome de domínio totalmente qualificado][], em que você cria uma ou mais filas, tópicos e assinaturas. O exemplo a seguir cria um namespace de mensagens do Barramento de Serviço em um [grupo de recursos](/azure/azure-resource-manager/resource-group-portal):

1. No painel de navegação esquerdo do portal, clique em **+ Criar um recurso**, depois em **Integração Corporativa** e em **Barramento de Serviço**.
2. Na caixa de diálogo **Criar um namespace** , digite um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível.
3. Após verificar se o nome do namespace está disponível, escolha o tipo de preço (Standard ou Premium).
4. No campo **Assinatura** , escolha uma assinatura do Azure na qual criar o namespace.
5. No campo **Grupo de Recursos** , escolha um grupo de recursos existente no qual o namespace residirá, ou então crie um novo.      
6. Em **Localização**, escolha o país ou região no qual o namespace deve ser hospedado.
7. Clique em **Criar**. Agora, o sistema cria o seu namespace e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.

  ![namespace](./media/service-bus-tutorial-topics-subscriptions-portal/create-namespace.png)

### <a name="obtain-the-management-credentials"></a>Obter as credenciais de gerenciamento

A criação de um novo namespace gera automaticamente uma regra de SAS (assinatura de acesso compartilhado) inicial com um par de chaves primárias e secundárias associado que concede, cada um, controle total sobre todos os aspectos do namespace. Para copiar a regra inicial, siga estas etapas:

1. Clique em **Todos os recursos** e depois clique no nome do namespace recém-criado.
2. Na janela namespace, clique em **Políticas de acesso compartilhado**.
3. Na tela **Políticas de acesso compartilhado**, clique em **RootManageSharedAccessKey**.
4. Na janela **Política: RootManageSharedAccessKey**, clique no botão **Copiar** próximo à **Cadeia de Conexão Primária**, para copiar a cadeia de conexão na área de transferência para uso posterior. Cole esse valor no Bloco de notas ou em outro local temporário.

    ![connection-string][connection-string]
5. Repita a etapa anterior, copiando e colando o valor de **chave primária** para um local temporário para uso posterior.

## <a name="create-a-topic-and-subscriptions"></a>Criar um tópico e assinaturas

Para criar um tópico de Barramento de Serviço, especifique o namespace sob o qual você deseja criá-lo. O exemplo a seguir mostra como criar um tópico no portal:

1. No painel de navegação à esquerda do portal, clique em **Barramento de Serviço** (se a opção **Barramento de Serviço** não estiver visível, clique em **Todos os serviços**).
2. Clique no namespace no qual você gostaria de criar o Tópico.
3. Na janela do namespace, clique em **Tópicos** e, em seguida, na janela **Tópicos**, clique em **+ Tópicos**.
4. Insira o **Nome** do Tópico e deixe os outros valores com os padrões.
5. Na parte inferior da janela, clique em **Criar**.
6. Anote o nome do tópico.
7. Selecione o tópico que você acabou de criar.
8. Clique em **+ Assinatura**, insira o nome da assinatura **S1** e deixe todos os outros valores com os padrões.
9. Repita a etapa anterior mais duas vezes, criando assinaturas nomeadas como **S2** e **S3**.

## <a name="create-filter-rules-on-subscriptions"></a>Criar regras de filtro nas assinaturas

Depois que o namespace e tópicos/assinaturas forem provisionados e você tiver as credenciais necessárias, você está pronto para criar regras de filtro nas assinaturas e depois enviar e receber mensagens. É possível examinar o código [nesta pasta de exemplo do GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/GettingStarted\BasicSendReceiveTutorialwithFilters).

### <a name="send-and-receive-messages"></a>Enviar e receber mensagens

Para executar o código, faça o seguinte:

1. Em um prompt de comando ou no prompt do PowerShell, clone o [repositório GitHub do Barramento de Serviço](https://github.com/Azure/azure-service-bus/), emitindo o comando a seguir:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Navegue até a pasta de exemplo `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveTutorialwithFilters`.

3. Obtenha a cadeia de conexão que você copiou para o Bloco de Notas na seção [ Obter as credenciais de gerenciamento ](#obtain-the-management-credentials) deste tutorial. Também será necessário o nome do tópico que você criou na seção anterior.

4. No prompt de comando, digite o comando a seguir:

   ```shell
   dotnet build
   ```

5. Navegue até a pasta `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp2.0`.

6. Digite o comando a seguir para executar o programa. Certifique-se de substituir `myConnectionString` pelo valor obtido anteriormente e `myTopicName` pelo nome do tópico que você criou:

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. Siga as instruções no console para selecionar a criação do filtro primeiro. Parte da criação do filtro é remover os filtros padrão. Ao utilizar o PowerShell ou a CLI não será necessário remover o filtro padrão mas, se fizer isso no código, deverá removê-los. Os comandos dos consoles 1 e 3 ajudam a gerenciar os filtros nas assinaturas criadas anteriormente:

   - Execute 1: para remover os filtros padrão.
   - Execute 2: para adicionar seus próprios filtros.
   - Execute 3: para, opcionalmente, remover seus próprios filtros. Observe que isso não irá recriar os filtros padrão.

    ![Mostrar a saída de 2](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. Após a criação do filtro, será possível enviar mensagens. Pressione 4 e observe 10 mensagens sendo enviadas para o tópico:

    ![Enviar saída](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. Pressione 5 e observe as mensagens sendo recebidas. Se você não recebeu 10 mensagens de volta, pressione "m" para exibir o menu e pressione 5 novamente.

    ![Receber saída](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o namespace e a fila. Para fazer isso, selecione esses recursos no portal e clique em **Excluir**.

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Esta seção contém mais detalhes sobre o que o código de exemplo faz.

### <a name="get-connection-string-and-topic"></a>Obter cadeia de conexão e tópico

Primeiro, o código declara um conjunto de variáveis, que conduz a execução restante do programa.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

A cadeia de conexão e o nome do tópico são passados por meio dos parâmetros da linha de comando, conforme mostrado e, em seguida, são lidos no método `Main()`:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Remover filtros padrão

Quando você cria uma assinatura, o Barramento de Serviço cria um filtro padrão por assinatura. Este filtro permite receber todas as mensagens enviadas para o tópico. Se você quiser usar filtros personalizados, poderá remover o filtro padrão, conforme mostrado no código a seguir:

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        foreach (var subscription in Subscriptions)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            await s.RemoveRuleAsync(RuleDescription.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
            await s.CloseAsync();
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Criar filtros

O código a seguir inclui os filtros personalizados definidos neste tutorial:

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, Subscriptions[i]);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Remover os filtros personalizados criados

Se você quiser remover todos os filtros da assinatura, o código a seguir mostra como fazer isso:

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            IEnumerable<RuleDescription> rules = await s.GetRulesAsync();
            foreach (RuleDescription r in rules)
            {
                await s.RemoveRuleAsync(r.Name);
                Console.WriteLine($"Rule {r.Name} has been removed.");
            }
            await s.CloseAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>Enviar mensagens

Enviar mensagens para um tópico é semelhante ao envio de mensagens para uma fila. Este exemplo mostra como enviar mensagens usando uma lista de tarefas e um processamento assíncrono:

```csharp
public async Task SendMessages()
{
    try
    {
        TopicClient tc = new TopicClient(ServiceBusConnectionString, TopicName);

        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(tc, Store[i]));
        }

        await Task.WhenAll(taskList);
        await tc.CloseAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(TopicClient tc, string store)
{
    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        Message message = item.AsMessage();
        message.To = store;
        message.UserProperties.Add("StoreId", store);
        message.UserProperties.Add("Price", item.getPrice().ToString());
        message.UserProperties.Add("Color", item.getColor());
        message.UserProperties.Add("Category", item.getItemCategory());

        await tc.SendAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>Receber mensagens

As mensagens são recebidas novamente por meio de uma lista de tarefas e o código usa envio em lote. Você pode enviar e receber usando envio em lote, mas este exemplo mostra apenas como receber em lote. Na realidade, você não iria interromper o loop, mas, continuaria em loop e definiria um período de tempo maior, como um minuto. A chamada de recebimento para o agente é mantida aberta durante esse período e, se as mensagens chegarem, serão retornadas imediatamente e uma nova chamada de recebimento será emitida. Esse conceito é chamado de *sondagem longa*. Usar a bomba de recebimento que você pode ver no [início rápido](service-bus-quickstart-portal.md), e em vários outros exemplos no repositório, é uma opção mais típica.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    var entityPath = EntityNameHelper.FormatSubscriptionPath(TopicName, subscription);
    var receiver = new MessageReceiver(ServiceBusConnectionString, entityPath, ReceiveMode.PeekLock, RetryPolicy.Default, 100);

    while (true)
    {
        try
        {
            IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            if (messages.Any())
            {
                foreach (var message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IDictionary<string, object> myUserProperties = message.UserProperties;
                        Console.WriteLine($"StoreId={myUserProperties["StoreId"]}");

                        if (message.Label != null)
                        {
                            Console.WriteLine($"Label={message.Label}");
                        }

                        Console.WriteLine(
                            $"Item data: Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}");
                    }

                    await receiver.CompleteAsync(message.SystemProperties.LockToken);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }

    await receiver.CloseAsync();
}
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você provisionou recursos usando o portal do Azure e, em seguida, enviou e recebeu mensagens de um tópico do Barramento de Serviço e as respectivas assinaturas. Você aprendeu como:

> [!div class="checklist"]
> * Criar um tópico de Barramento de Serviço e uma ou mais assinaturas para esse tópico usando o portal do Azure
> * Adicionar filtros de tópicos usando código .NET
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar se chegaram nas assinaturas esperadas
> * Receber mensagens das assinaturas

Para obter mais exemplos de envio e recebimento de mensagens, comece com os [Exemplos de Barramento de Serviço no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Avance para o próximo tutorial para saber mais sobre como usar os recursos de publicação/assinatura do Barramento de Serviço.

> [!div class="nextstepaction"]
> [Atualizar estoque usando o PowerShell e tópicos/assinaturas](service-bus-tutorial-topics-subscriptions-powershell.md)

[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[nome de domínio totalmente qualificado]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png