<properties
   pageTitle="Serialização de serviço confiável | Microsoft Azure"
   description="Documentação conceitual para serialização de Service Fabric do serviço confiável"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson,tyadam"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="mcoskun"/>

# Serialização em serviços confiáveis
O Gerenciador de estado confiável pode conter vários objetos confiáveis. Alguns desses objetos confiáveis podem ser estruturas de dados genéricos como o Dicionário confiável e a Fila confiável recém-lançados. Como essas são estruturas de dados genérico confiáveis, os objetos genéricos devem ser serializados para que possam ser replicados e persistidos no disco.

O Gerenciador de Estado Confiável oferece suporte a três tipos de serializadores; * Serializadores personalizados, * Serializadores internos para um número limitado de tipos, * DataContractSerilizer

Quando um Objeto confiável precisar serializar um objeto, ele consultará o Gerenciador de estado confiável para um serializador para o tipo especificado. Primeiro, o Gerenciador de estado confiável verificará se há um serializador personalizado registrado para o tipo de entrada. Caso contrário, ele verificará se um dos serializadores internos pode serializar o tipo. O Gerenciador de estado confiável tem serializadores internos para os seguintes tipos: guid, bool, byte, sbyte, char, decimal, double, float, int, uint, long, ulong, short, ushort e cadeia de caracteres. Caso contrário, ele retornará o DataContractSerializer.

Este artigo enfoca quando e como usar a serialização personalizada.

## Quando usar a serialização personalizada
Há duas razões comuns para usar a serialização personalizada * desempenho * criptografia

Para tipos que não são cobertos pelos tipos internos, podem ser obtidas melhorias consideráveis de desempenho usando serializadores internos personalizados em vez do DataContractSerializer. Uma razão é que os serializadores personalizados não precisam serializar informações de tipo. O Service Fabric garante que um serializador de estado para um determinado tipo só terá esse tipo para serializar e desserializar.

Os dados serializados gerados pelos serializadores são replicados e persistidos no disco como está. Para dados confidenciais, pode ser desejável certificar-se de que os bits na transmissão e disco sejam criptografados.

## Como usar a serialização personalizada
Para usar um serializador personalizado para um determinado tipo, precisamos * compilar um serializador de estado personalizado * registrar o serializador de estado personalizado no Serviço confiável

### Como implementar um serializador personalizado
Serializadores personalizados precisam implementar a interface IStateSerializer<T>. Os dois métodos principais da interface são * T Read(BinaryReader binaryReader); * void Write(T value, BinaryWriter binaryWriter);

Primeiro é usado pelo ReliableObject para ler o objeto serializado em uma transmissão usando BinaryReader. Em segundo lugar, é usado para a operação inversa: gravar o objeto em uma transmissão usando um gravador binário.

Segue um exemplo de classe personalizada e um serializador para ele.

```C#
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }
    public short District { get; set; }
    public int Customer { get; set; }
    public long Order { get; set; }
```

```C#
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
    void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
    {
        writer.Write(value.Warehouse);
        writer.Write(value.District);
        writer.Write(value.Customer);
        writer.Write(value.Order);
    }

    OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
    {
        OrderKey value = new OrderKey();
        value.Warehouse = reader.ReadByte();
        value.District = reader.ReadInt16();
        value.Customer = reader.ReadInt32();
        value.Order = reader.ReadInt64();

        return value;
    }

    void IStateSerializer<OrderKey>.Write(OrderKey currentValue, OrderKey newValue, BinaryWriter writer)
    {
        ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
    }

    OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
    {
        return ((IStateSerializer<OrderKey>)this).Read(reader);
    }
}
```
>[AZURE.NOTE]No exemplo acima, a implementação das sobrecargas de Leitura e Gravação simplesmente chamam suas sobrecargas de contraparte. Isso ocorre porque os dois métodos a seguir serão usados para um recurso que não está disponível.

### Como registrar um serializador de cliente
Para registrar um serializador personalizado, primeiro precisamos de um método que possa registrar todos os serializadores personalizados. Esse método não precisa usar argumentos e ser capaz de retornar uma Tarefa. Nesse método, IReliableStateManager.TryAddStateSerializer<T> deve ser usado para registrar todos os serializadores personalizados para o Serviço confiável.

```C#
protected Task InitializeStateSerializers()
{
    this.StateManager.TryAddStateSerializer(new OrderKeySerializer());
    return Task.FromResult(false);
}
```

A próxima etapa é registrar o método acima como um delegado a ser chamado pelo Gerenciador de estado confiável no momento em que todos os serializadores de estado personalizado devem ser registrados. Esse método é chamado somente no início da réplica de um Serviço confiável antes de iniciar a recuperação local, pois os serializadores podem ser necessários para ler os dados serializados do disco. Depois que o serializador for registrado, o tipo relevante de todos os Objetos confiáveis usará este serializador para serializar e desserializar os objetos.

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            onInitializeStateSerializersEvent : this.InitializeStateSerializers));
}
```
### Controle de versão
O Service Fabric espera que os serializadores sejam infinitamente compatíveis para frente e para trás. Para os tipos que estão usando serializadores internos, o Service Fabric garante a compatibilidade em todos os sentidos. Para os tipos que utilizam o DataContractSerializer ou o serializador personalizado, é necessário que o usuário nunca faça uma alteração significativa. Para o controle de versão do DataContract, consulte [Controle de versão do contrato de dados](https://msdn.microsoft.com/library/ms731138.aspx). Se uma alteração significativa for necessária, o estado precisará ser movido de uma instância de serviço com a versão antiga de dados para um serviço com a nova versão de dados no nível do aplicativo.

### Quando é usado um serializador
 * As Operações de Gravação em Objetos confiáveis farão com que eles sejam serializados, duplicados e armazenados em um Log.
 * Após um número de operações suficiente ser registrado, os últimos dados da memória serão serializados e receberão um ponto de verificação no disco.
 * Para a criação de uma nova réplica, os arquivos de ponto de verificação no disco e os dados recentes do Log serão enviados diretamente byte por byte (ou seja, os dados não serão serializados novamente) de uma réplica primária. Esses bytes serão desserializados em objetos C# na réplica secundária.
 * Durante a recuperação, os arquivos de ponto de verificação e os dados recentes Log serão desserializados.
 * Durante o backup, os arquivos de ponto de verificação e os dados de log recentes serão copiados byte por byte.
 * Durante a restauração, o backup anterior de arquivos de ponto de verificação e os dados de log serão copiados de volta para o local e serão desserializados.

## Próximas etapas
 * [Uso avançado do modelo de programação de Serviços Confiáveis](service-fabric-reliable-services-advanced-usage.md)

<!---HONumber=AcomDC_1203_2015-->