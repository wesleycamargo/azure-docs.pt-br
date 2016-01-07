<properties
   pageTitle="Particionando serviços da Malha do Serviço | Microsoft Azure"
   description="Descreve como particionar os serviços de malha do serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="bscholl"/>

# Como particionar os Reliable Services de Malha de Serviço
Este artigo fornece uma introdução aos conceitos básicos de particionamento de Reliable Services do Service Fabric. O código-fonte usado no artigo também está disponível no [Github](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/AlphabetPartitions).

## O que é o particionamento
Particionamento não é exclusivo à Malha de Serviço, na verdade é um padrão de núcleo de criação de serviços escalonáveis. Em um sentido mais amplo, podemos pensar em particionamento como um conceito de divisão de estado (dados) e computar unidades menores acessíveis para melhorar o desempenho e escalabilidade. Uma forma conhecida de particionamento é o [particionamento de dados][wikipartition], também conhecido como fragmentação.


### Particionando serviços sem estado da Malha de Serviço
Para serviços sem estado, você pode pensar em uma partição como sendo uma unidade lógica que contém uma ou mais instâncias de um serviço. A Figura 1 mostra um serviço sem estado com 5 instâncias distribuídas em um cluster usando uma partição.

![Serviço sem estado](./media/service-fabric-concepts-partitioning/statelessservice.png)

Há realmente dois tipos de soluções de serviço sem estado. O primeiro sendo um serviço que mantém o estado externamente, por exemplo, no banco de dados SQL do Azure (como um site que armazena as informações de sessão e dados) e o outro sendo serviços somente de computação (como uma calculadora ou criação de miniaturas de imagem) que não gerenciam nenhum estado persistente. Em ambos os casos o particionamento de um serviço sem estado é um cenário muito raro e a escalabilidade e disponibilidade normalmente são obtidas pela adição de mais instâncias. As únicas vezes você deve considerar várias partições para instâncias de serviço sem estado é quando você precisa atender solicitações especiais de roteamento. Por exemplo, considere um caso onde os usuários com ids de um determinado intervalo só devem ser atendidos por uma instância de determinado serviço. Outro exemplo de quando é possível particionar um serviço sem estado é quando você tem um back-end realmente particionado, por exemplo, um banco de dados SQL fragmentado e você deseja controlar qual instância de serviço deve gravar o fragmento do banco de dados ou executar outras tarefas de preparação dentro do serviço sem estado que requer as mesmas informações de particionamento como usadas no back-end. Esses tipos de cenários também podem ser resolvidos de diferentes maneiras e não exigem, necessariamente, particionamento de serviço.

O restante deste passo a passo se concentra nos serviços sem estado.

### Particionando de serviços com estado da Malha de Serviço
A Malha de Serviço facilita o desenvolvimento serviços com estado escalonáveis, oferecendo uma ótima forma para o estado de partição (dados). Conceitualmente, uma partição de um serviço com estado é uma unidade de escala altamente confiável por causa de [réplicas](service-fabric-availability-services.md) distribuídas e balanceadas entre os nós no cluster. No contexto dos serviços com estado do Service Fabric, o particionamento se refere ao processo de determinar que uma partição de serviço específica (como mencionado anteriormente, a partição é um conjunto de [réplicas](service-fabric-availability-services.md)) é responsável por uma parte do estado completo do serviço. Uma grande vantagem da Malha de Serviço é que ela coloca as partições em nós diferentes, permitindo que elas atinjam o limite de recurso do nó. Conforme os dados precisam crescer, as partições crescem e a Malha de Serviço equilibra novamente as partições entre nós garantindo o uso eficiente contínuo dos recursos de hardware.

Para dar um exemplo digamos que você começa com um cluster de 5 nós e um serviço configurado para ter 10 partições e um destino de três réplicas. Nesse caso, o Service Fabric deveria balancear e distribuir as réplicas no cluster e você acabaria com duas [réplicas](service-fabric-availability-services.md) principais por nó. Caso você precise expandir nosso cluster para 10 nós, o Service Fabric rebalanceará as [réplicas](service-fabric-availability-services.md) principais entre todos os 10 nós. Da mesma forma se dimensionado para 5 nós, a Malha de Serviço rebalançaria todas as réplicas entre todos os 5 nós.

A Figura 2 mostra a distribuição de 10 partições antes e depois do dimensionamento do cluster.

![Serviço com estado](./media/service-fabric-concepts-partitioning/scaledcluster.png)

Como resultado a expansão é atingida pois as solicitações de clientes são distribuídas entre máquinas, o desempenho geral do aplicativo é aprimorado e a contenção no acesso a partes de dados é reduzida.

## Planejamento de particionamento
Antes de implementar um serviço sempre considere a estratégia de particionamento necessária para expansão. Há maneiras diferentes, mas todos elas voltadas para o que o aplicativo precisa atingir. Para o contexto deste artigo vamos considerar alguns dos aspectos mais importantes.

Uma boa abordagem é pensar sobre a estrutura do estado que precisará ser particionado como a primeira etapa.

Veja abaixo um exemplo simples Se você fosse criar um serviço para uma pesquisa em toda um município você poderia criar uma partição para cada cidade no município e, em seguida, armazenar os votos para todas as pessoas da cidade na partição correspondente à cidade. A Figura 3 ilustra um conjunto de pessoas e a cidade em que residem.

![Partição simples](./media/service-fabric-concepts-partitioning/cities.png)

Como a população das cidades varia muito você poderia acabar com algumas partições que contêm muitos dados (por exemplo, Seattle) e outras partições com muito pouco estado (por exemplo, Kirkland). Então, qual é o impacto de ter partições com quantidades irregulares de estado?

Se você pensar sobre o exemplo novamente você pode facilmente ver que a partição que contém os votos de Seattle obterá mais tráfego do que a de Kirkland. Por padrão as Malha de Serviços garante que há aproximadamente o mesmo número de réplicas principais e secundárias réplicas em cada nó, portanto você pode acabar nós que contêm réplicas que servem mais tráfego e outros que servem menos tráfego. Você deve, de preferência, evitar pontos altos e baixos assim em um cluster.

Para evitar isso, você deve fazer duas coisas do ponto de vista do particionamento:

- Tente particionar o estado que ele seja distribuído igualmente entre todas as partições.
- [Relate as métricas de cada uma das réplicas para o serviço](service-fabric-resource-balancer-dynamic-load-reporting.md). A Malha de Serviço oferece a capacidade de relatar métricas, tais como quantidade de memória ou o número de registros, em um serviço. Com base nas métricas relatadas a Malha de Serviço detecta que algumas partições estão atendendo a cargas mais altas que outras e faz um novo balanceamento do cluster movendo réplicas para nós mais adequados.

Às vezes, você pode não saber a quantidade de dados que terá em uma determinada partição e então uma recomendação geral é fazer ambas as coisas, primeiro, adotando uma estratégia de particionamento que distribui dados uniformemente entre as partições e em segundo lugar, relatar a carga. O primeiro método impede as situações descritas no exemplo de votação, enquanto o segundo ajuda a amenizar diferenças temporárias no acesso ou carga ao longo do tempo.

Outro aspecto do planejamento de partição é escolher o número correto de partições para começar. Do ponto de vista de Malha de Serviço não há nada que impede que você comece com um número maior de partições que previa para seu cenário. Na verdade, suponha que o número máximo de partições é uma abordagem válida.

Em casos raros, você acabaria precisando de mais partições que escolheu inicialmente. Como você não pode alterar a contagem de partições após o fato de que você precisaria aplicar algumas abordagens avançadas de partição como a criação de uma nova instância de serviço do mesmo tipo de serviço e implementar uma lógica de cliente que direcionasse as solicitações para a instância de serviço correta com base no conhecimento do lado do cliente que o código do cliente deve manter.

Outra consideração de planejamento de particionamento são os recursos de máquina disponível. Como o estado precisa ser acessado e armazenados você deve fazer o seguinte:

- Limites da largura de banda de rede
- Limites de memória do sistema
- Limites de armazenamento em disco

O que acontece se você tiver restrições de recursos em um cluster em execução? A resposta é que você pode simplesmente dimensionar o cluster para acomodar os novos requisitos.

[O guia de planejamento de capacidade](service-fabric-capacity-planning.md) oferece orientação sobre como determinar a quantidade necessária de nós no cluster.

## Como particionar
Esta seção descreve como começar com o particionamento do seu serviço.

Em primeiro lugar a Malha de Serviço possui uma opção de três esquemas de partição.

- Intervalo de particionamento (também conhecido como UniformInt64Partition)
- Particionamento nomeado. Aplicativos que usam esse modelo geralmente possuem dados de bucket, dentro de um conjunto limitado. Alguns exemplos comuns de campos de dados usados como chaves de partição nomeada seriam regiões, códigos postais, grupos de clientes ou outros limites de negócios.
- Particionamento de singleton. Partições de singleton normalmente são usadas quando o serviço não requer qualquer roteamento adicional. Por exemplo, serviços sem estado usam esse esquema de particionamento por padrão.

Os esquemas de particionamento Singleton e Nomeado são formulários especiais de partições de intervalos. Por padrão os modelos do Visual Studio para a Malha de Serviço usam o particionamento nomeado já que é o mais comum e o mais útil. O restante deste artigo se concentra no esquema de particionamento por intervalo.

### Esquema de particionamento por intervalos
Usado para especificar um intervalo inteiro (identificado por uma chave alta e baixa) e um número de partições (n). Cria n partições, cada uma responsável por um subintervalo sem sobreposição do intervalo de chave de partição geral. Por exemplo: Um esquema de particionamento por intervalos com uma chave baixa de 0, uma chave de alta de 99 e uma contagem de 4 criaria 4 partições conforme mostrado abaixo.

![Particionamento por intervalos](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Uma abordagem comum é criar um hash com base em uma chave exclusiva dentro do conjunto de dados. Alguns exemplos comuns de chaves seriam um número de identificação de veículo (VIN), ID do funcionário ou uma cadeia de caracteres exclusiva. Usando essa chave exclusiva você criaria um código de hash longo, o módulo do intervalo de chaves, para usar como sua chave. Você pode especificar os limites superior e inferior do intervalo de chave permitido.


### Selecionando um algoritmo de hash
Uma parte importante de hash é selecionar o algoritmo de hash. Uma consideração é se o objetivo é agrupar chaves semelhantes próximas umas das outras (hash sensível à localidade) ou se a atividade deve ser distribuída amplamente em todas as partições (hash de distribuição), que é o mais comum.

As características de um bom algoritmo de hash de distribuição são que são fáceis de computar, possui poucas colisões e que distribui igualmente as chaves. Um bom exemplo de algoritmo de hash eficiente é o algoritmo de hash [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function).


Um bom recurso para opções gerais de algoritmo de código de hash é [a página do Wikipedia sobre funções de hash](http://en.wikipedia.org/wiki/Hash_function).

## Criando um serviço com estado com várias partições
Vamos criar seu primeiro serviço com estado confiável com várias partições. Neste exemplo, você criará um aplicativo muito simples no qual você deseja armazenar todos os sobrenomes que começam com a mesma letra na mesma partição.

Antes de escrever qualquer código você precisa pensar sobre as chaves de partição e partições. Você precisa de 26 partições, uma para cada letra no alfabeto, mas e as chaves baixas e altas? Como queremos literalmente ter uma partição por letra podemos usar 0 como a chave baixa e 25 como a chave alta já cada letra é sua própria chave.


>[AZURE.NOTE]Esse é um cenário simplificado pois na realidade a distribuição seria irregular. Sobrenomes começando com a letra S ou M são mais comuns do que os que começam com X ou Y.


1. Abra o Visual Studio -> Arquivo Novo -> Projeto
2. Na caixa de diálogo Novo Projeto escolha um aplicativo da Malha de Serviço
3. Nomeie o projeto como AlphabetPartitions
4. Na caixa de diálogo Criar um Serviço, escolha o Serviço com estado e nomeie-o como Alphabet.Processing, como mostrado na imagem abaixo. ![alphabetstateful](./media/service-fabric-concepts-partitioning/alphabetstatefulnew.png)
5. Defina o número de partições. Abra o ApplicationManifest.xml no projeto AlphabetPartitions e atualize o parâmetro Processing\_PartitionCount para 26, conforme mostrado abaixo.

    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
    Você deve também atualizar as propriedades LowKey e HighKey do elemento StatefulService, como mostrado abaixo. ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```

6. Para que o serviço fique acessível abra um ponto de extremidade em uma porta, adicionando e atualizando o elemento de ponto de extremidade do ServiceManifest.xml (localizado na pasta PackageRoot) para o serviço Alphabet.Processing, conforme mostrado abaixo:

    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Protocol="http" Type="Internal" />
    ```

    Agora o serviço está configurado para escutar um ponto de extremidade interno com 26 partições.

7. Em seguida, substitua o método `CreateServiceReplicaListeners()` da classe Processing.

    >[AZURE.NOTE]Para este exemplo, pressupomos que você está usando um HttpCommunicationListener simples. Clique [aqui](service-fabric-reliable-services-communication.md) para saber mais sobre comunicação de Reliable Service.

8. O padrão recomendado para a url em que uma réplica escuta está no seguinte formato: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}` e, portanto, convém configurar o ouvinte de comunicação para escutar nos pontos de extremidade corretos e com esse padrão. Várias réplicas desse serviço podem ser hospedadas na mesma máquina, então este endereço precisa ser exclusivo para a réplica que é o motivo pelo qual temos ID de partição + ID de réplica na URL. HttpListener pode escutar em vários endereços na mesma porta, se o prefixo de URL for exclusivo. O GUID extra existe para um caso avançado em que as réplicas secundárias também escutam solicitações de somente leitura. Quando esse for o caso, você deve certificar-se de que um novo endereço exclusivo é usado durante a transição do principal para o secundário para forçar os clientes a resolver o endereço novamente. '+' é usado como o endereço aqui para que a réplica escute em todos os hosts disponíveis (IP, FQDM, localhost, etc.) O código abaixo mostra um exemplo.

    ```CSharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
            return new[] { new ServiceReplicaListener(CreateInternalListener, "Internal", false) };
    }
    private ICommunicationListener CreateInternalListener(StatefulServiceInitializationParameters args)
    {
        EndpointResourceDescription internalEndpoint = args.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");

        string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                this.ServiceInitializationParameters.PartitionId,
                this.ServiceInitializationParameters.ReplicaId,
                Guid.NewGuid());

        string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
        string uriPublished = uriPrefix.Replace("+", nodeIP);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
Também vale a pena observar que a URL publicada é ligeiramente diferente do prefixo da URL de escuta. A URL de escuta é dada ao HttpListener. A URL publicada é a URL que será publicada para o Serviço de Nomenclatura da Malha de Serviço, que é usado para descoberta de serviço. Os clientes pedirão esse endereço por meio desse serviço de descoberta. O endereço que os clientes obtêm precisam ter o IP real ou FQDN do nó para conectar-se, por isso você precisa substituir '+' pelo IP do nó ou FQDN como mostrado acima.
9. A última etapa é adicionar a lógica de processamento ao serviço, conforme mostrado abaixo.

    ```CSharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
          string output = null;
          string user = context.Request.QueryString["lastname"].ToString();

          try
          {
              output = await this.AddUserAsync(user);
          }
          catch (Exception ex)
          {
              output = ex.Message;
          }

          using (HttpListenerResponse response = context.Response)
          {
              if (output != null)
              {
                  byte[] outBytes = Encoding.UTF8.GetBytes(output);
                  response.OutputStream.Write(outBytes, 0, outBytes.Length);
              }
          }
      }
      private async Task<string> AddUserAsync(string user)
      {
          IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");

          using (ITransaction tx = this.StateManager.CreateTransaction())
          {
              bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);

              await tx.CommitAsync();

              return String.Format(
                  "User {0} {1}",
                  user,
                  addResult ? "sucessfully added" : "already exists");
          }
      }
    ```

    `ProcessInternalRequest` lê os valores do parâmetro de cadeia de caracteres da consulta usada para chamar a partição e chama `AddUserAsync` para adicionar o sobrenome ao dicionário confiável `m_name`.

10. Vamos adicionar um serviço sem estado ao projeto para ver como você pode chamar uma partição específica. Esse serviço serve como uma interface web simples que aceita lastname como um parâmetro de cadeia de caracteres de consulta, determina a chave de partição e o envia-a para o serviço Alphabet.Processing para processamento.
11. Na caixa de diálogo Criar um Serviço, escolha o Serviço sem estado e nomeie-o como Alphabet.WebApi, como mostrado abaixo. ![alphabetstateless](./media/service-fabric-concepts-partitioning/alphabetstatelessnew.png).
12. Atualize as informações de ponto de extremidade no ServiceManifest.xml do serviço Alphabet.WebApi para abrir uma porta, conforme mostrado abaixo

    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8090"/>
    ```

13. Você precisa retornar uma coleção de ServiceInstanceListeners. Novamente você pode optar por implementar uma HttpCommunicationListener simples.

    ```CSharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
           return new[] {new ServiceInstanceListener(this.CreateInputListener, "Input")};
    }
    private ICommunicationListener CreateInputListener(StatelessServiceInitializationParameters args)
    {
           // Service instance's URL is the node's IP & desired port
           EndpointResourceDescription inputEndpoint = args.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
           string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
           var uriPublished = uriPrefix.Replace("+", m_nodeIP);
           return new HttpCommunicationListener(uriPrefix, uriPublished, ProcessInputRequest);
     }
     ```
14. Agora você precisa implementar a lógica de processamento. O HttpCommunicationListener chama `ProcessInputRequest` quando chega uma solicitação. Vamos prosseguir e adicionar o código a abaixo

    ```CSharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
           String output = null;
           try
           {
               string lastname = context.Request.QueryString["lastname"];
               char firstLetterOfLastName = lastname.First();
               int partitionKey = Char.ToUpper(firstLetterOfLastName) - 'A';

               ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
               ResolvedServiceEndpoint ep = partition.GetEndpoint();
               JObject addresses = JObject.Parse(ep.Address);
               string primaryReplicaAddress = addresses["Endpoints"].First()["Value"].Value<string>();

               UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
               primaryReplicaUriBuilder.Query = "lastname=" + lastname;

               string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);

               output = String.Format(
               "Result: {0}. Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. Processing service partition ID: {4}. Processing service replica address: {5}",
               result,
               partitionKey,
               firstLetterOfLastName,
               lastname,
               partition.Info.Id,
               primaryReplicaAddress);
    }
    catch (Exception ex) { output = ex.Message; }
    using (var response = context.Response)
    {
               if (output != null)
               {
                   output = output + "added to Partition: " + primaryReplicaAddress;
                   byte[] outBytes = Encoding.UTF8.GetBytes(output);
                   response.OutputStream.Write(outBytes, 0, outBytes.Length);
               }
           }
      }
      ```

    Vamos ver o passo a passo. O código lê a primeira letra do parâmetro da cadeia de caracteres de consulta `lastname` dentro de um char. Isso determina a chave de partição para essa letra, subtraindo o valor hex. de `A` do valor hex. da primeira letra do último nome.

    ```CSharp
    string lastname = context.Request.QueryString["lastname"]; char firstLetterOfLastName = lastname.First(); int partitionKey = Char.ToUpper(firstLetterOfLastName) - 'A'; ```

    Lembre-se que neste exemplo, estamos usando 26 partições com uma chave de partição por partição. Em seguida, obtemos a partição de serviço `partition` para essa chave usando o método `ResolveAsync` no objeto `servicePartitionResolver`. `servicePartitionResolver` é definido como

    ```CSharp
    private static readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```

    O método `ResolveAsync` usa o uri do serviço, a chave da partição e um token de cancelamento como parâmetros. O uri do serviço de processamento é `fabric:/AlphabetPartitions/Processing`. Em seguida, obtemos o ponto de extremidade da partição.

    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```

    Finalmente, criamos a url de ponto de extremidade mais querystring e chamamos o serviço de processamento.

    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = addresses["Endpoints"].First()["Value"].Value<string>();

    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;

    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```

    Após o processamento ter sido realizado podemos gravar a saída de volta.

15. A última etapa é testar o serviço. O Visual Studio usa parâmetros do aplicativo para implantação local e na nuvem. Para testar o serviço com 26 partições localmente, atualize o arquivo `Local.xml` na pasta ApplicationParameters do projeto AlphabetPartitions, como mostrado abaixo:

    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
  </Parameters>
  ```

16. Após a implantação, verifique o serviço e todas as respectivas partições no Gerenciador do Service Fabric. ![O Barramento de](./media/service-fabric-concepts-partitioning/alphabetservicerunning.png)
17. Insira `http://localhost:8090/?lastname=somename` em um navegador para testar a lógica de particionamento. Você verá que cada sobrenome iniciado com a mesma letra será armazenado na mesma partição. ![Navegador](./media/service-fabric-concepts-partitioning/alphabetinbrowser.png)

O código-fonte completo do exemplo está disponível no [Github](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/AlphabetPartitions)

## Próximas etapas

Para obter informações sobre os conceitos de malha do serviço, consulte:

- [Disponibilidade dos serviços de malha do serviço](service-fabric-availability-services.md)

- [Escalabilidade dos serviços da malha de serviço](service-fabric-concepts-scalability.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

<!----HONumber=AcomDC_1125_2015-->