---
title: Gerenciamento de estado dos Reliable Actors | Microsoft Docs
description: Descreve como o estado dos Reliable Actors é gerenciado, persistido e replicado para alta disponibilidade.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2016
ms.author: vturecek

---
# Gerenciamento de estado dos Reliable Actors
Reliable Actors são objetos single-threaded que podem encapsular a lógica e o estado. Como os atores são executados nos Reliable Services, eles podem manter o estado de modo confiável usando os mesmos mecanismos de persistência e replicação usados pelos Reliable Services. Dessa forma, os atores não perdem seu estado após falhas, reativação após a coleta de lixo ou quando são movidos entre nós em um cluster devido a balanceamento de recursos ou atualizações.

## Replicação e persistência de estado
Todos os Reliable Actors são considerados *com estado*, pois cada instância de ator é mapeada para uma ID exclusiva. Isso significa que chamadas repetidas para a mesma ID de ator serão encaminhadas para a mesma instância de ator. Isso é diferente de um sistema sem estado no qual as chamadas de cliente não têm garantia de serem encaminhadas sempre para o mesmo servidor. Por esse motivo, os serviços de ator são sempre serviços com estado.

No entanto, mesmo que os atores sejam considerados com estado, isso não significa que eles devem armazenar o estado de modo confiável. Os atores podem escolher o nível de persistência de estado e replicação com base em seus requisitos de armazenamento de dados:

* **Estado persistente:** o estado é persistido em disco e replicado para três ou mais réplicas. Essa é a opção de armazenamento de estado mais duradoura, na qual o estado pode persistir após a interrupção completa do cluster.
* **Estado volátil:** o estado é replicado para três ou mais réplicas e mantido apenas na memória. Isso proporciona resiliência contra falhas de nó e de ator, e durante atualizações e balanceamento de recursos. No entanto, o estado não é persistido em disco e, portanto, se todas as réplicas forem perdidas ao mesmo tempo, o estado também será perdido.
* **Nenhum estado persistente:** o estado não é replicado nem é gravado em disco. Para atores que simplesmente não precisam manter o estado de modo confiável.

Cada nível de persistência é apenas um *provedor de estado* e uma configuração de *replicação* diferentes do serviço. A decisão de gravar ou não o estado em disco depende do *provedor de estado* - o componente em um Reliable Service que armazena o estado - e a replicação depende do número de réplicas com as quais um serviço é implantado. Assim como ocorre com os Reliable Services, o provedor de estado e a contagem de réplicas podem ser definidos manualmente com facilidade. A estrutura de ator fornece um atributo, que, quando usado em um ator, selecionará automaticamente um provedor de estado padrão e gerará automaticamente as configurações de contagem de réplicas, a fim de obter uma dessas três configurações de persistência.

### Estado persistente
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```  
Essa configuração usa um provedor de estado que armazena dados em disco e define automaticamente a contagem de réplicas do serviço como 3.

### Estado volátil
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
Essa configuração usa um provedor de estado somente na memória e define a contagem de réplicas como 3.

### Nenhum estado persistente
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
Essa configuração usa um provedor de estado somente na memória e define a contagem de réplicas como 1.

### Padrões e configurações geradas
Ao usar o atributo `StatePersistence`, um provedor de estado será selecionado automaticamente para você no tempo de execução quando o serviço de ator for iniciado. No entanto, a contagem de réplicas é definida no tempo de compilação pelas ferramentas de build de ator do Visual Studio. As ferramentas de build geram automaticamente um *serviço padrão* para o serviço de ator no ApplicationManifest.xml. Os parâmetros são criados para **tamanho mín. do conjunto de réplicas** e **tamanho de destino do conjunto de réplicas**. Evidentemente, é possível alterar esses parâmetros manualmente, porém, sempre que o atributo `StatePersistence` for alterado, os parâmetros serão definidos como os valores padrão de tamanho do conjunto de réplicas para o atributo `StatePersistence` selecionado, substituindo quaisquer valores anteriores. Em outras palavras, os valores definidos em ServiceManifest.xml serão substituídos **somente** em tempo de compilação quando você alterar o valor `StatePersistence` do atributo.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="2" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## Gerenciador de Estado
Cada instância de ator tem seu próprio Gerenciador de Estado: uma estrutura de dados semelhante a um dicionário que armazena pares chave-valor de forma confiável. O Gerenciador de Estado é um wrapper de um provedor de estado. Ele pode ser usado para armazenar dados, independentemente da configuração de persistência utilizada, mas não dá nenhuma garantia de que um serviço de ator em execução pode ser alterado de uma configuração de estado volátil (somente na memória) para uma configuração de estado persistente por meio de uma atualização sem interrupção, ao mesmo tempo que preserva os dados. No entanto, é possível alterar a contagem de réplicas de um serviço em execução.

As chaves do Gerenciador de Estado devem ser cadeias de caracteres, enquanto os valores são genéricos e podem ser de qualquer tipo, incluindo tipos personalizados. Os valores armazenados no Gerenciador de Estado devem ser serializáveis pelo Contrato de Dados, pois podem ser transmitidos pela rede a outros nós durante a replicação e gravados em disco, dependendo da configuração de persistência do estado de um ator.

O Gerenciador de Estado expõe métodos comuns de dicionário para gerenciamento do estado, semelhantes àqueles encontrados no Dicionário Confiável.

### Acessando o estado
O estado pode ser acessado por chave por meio do Gerenciador de Estado. Os métodos do Gerenciador de Estado são todos assíncronos, pois exigem E/S de disco quando os atores têm um estado persistente. No primeiro acesso, os objetos de estado são armazenados em cache na memória. Operações de acesso repetidas acessam os objetos diretamente da memória e são retornadas de forma síncrona sem incorrer em E/S de disco ou sobrecarga de troca de contexto assíncrona. Um objeto de estado é removido do cache nos seguintes casos:

* Um método de ator gera uma exceção sem tratamento depois de recuperar um objeto do Gerenciador de Estado.
* Um ator é reativado depois de ser desativado ou devido a uma falha.
* Se o provedor de estado paginar o estado em disco. Esse comportamento depende da implementação do provedor de estado. O provedor de estado padrão para a configuração `Persisted` apresenta esse comportamento.

O estado pode ser recuperado usando uma operação *Get* padrão que gerará `KeyNotFoundException` caso não exista uma entrada para a chave especificada:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```

O estado também pode ser recuperado usando um método *TryGet* que não gerará o seguinte, caso não exista uma entrada para determinada chave:

```csharp
class MyActor : Actor, IMyActor
{
    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```

### Salvando o estado
Os métodos de recuperação do Gerenciador de Estado retornam uma referência a um objeto na memória local. A modificação desse objeto na memória local por si só não faz com que ele seja salvo permanentemente. Quando um objeto é recuperado do Gerenciador de Estado e é modificado, ele deve ser reinserido no Gerenciador de Estado para ser salvo permanentemente.

O estado pode ser inserido com um *Set* incondicional, que é o equivalente da sintaxe `dictionary["key"] = value`:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```

O estado pode ser adicionado com um método *Add*, que gerará `InvalidOperationException` ao tentar adicionar uma chave já existente:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```

O estado também pode ser adicionado com um método *TryAdd*, que não gerará o seguinte ao tentar adicionar uma chave já existente:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```

No final de um método de ator, o Gerenciador de Estado salva automaticamente todos os valores que foram adicionados ou modificados por uma operação de inserção ou atualização. A operação “salvar” pode incluir a persistência em disco e a replicação, dependendo das configurações utilizadas. Valores que não foram modificados não serão persistentes nem replicados. Caso nenhum valor tenha sido modificado, a operação salvar não terá nenhum efeito. Se houver uma falha na operação salvar, o estado modificado será descartado e o estado original será recarregado.

O estado também pode ser salvo manualmente com a chamada do método `SaveStateAsync` na base do ator:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```

### Removendo o estado
O estado pode ser removido permanentemente do Gerenciador de Estado de um ator com a chamada do método *Remove*. Esse método gerará `KeyNotFoundException` ao tentar remover uma chave não existente:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```

O estado também pode ser removido permanentemente com o método *TryRemove*, que não gerará o seguinte ao tentar remover uma chave não existente:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```

## Próximas etapas
* [Serialização de tipo de ator](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
* [Polimorfismo de ator e padrões de design orientado a objeto](service-fabric-reliable-actors-polymorphism.md)
* [Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API do Ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Exemplo de código](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0713_2016-->