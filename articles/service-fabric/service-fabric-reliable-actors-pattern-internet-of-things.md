
<properties
   pageTitle="Reliable Actors para a Internet das Coisas | Microsoft Azure"
   description="Os Reliable Actors do Service Fabric são os blocos de construção principais em um sistema que combina um front-end de sistema de mensagens que oferece suporte a vários transportes, como HTTPS, MQTT e AMQP."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/14/2015"
   ms.author="vturecek"/>

# Padrão de design de Reliable Actors: Internet das Coisas
Desde que a IoT (Internet das Coisas) tornou-se a nova tendência ao lado do avanço tecnológico em dispositivos e serviços de nuvem, os desenvolvedores começaram a olhar para os principais blocos de construção para desenvolver seus sistemas. O diagrama a seguir ilustra os principais cenários obtidos usando os Reliable Actors do Service Fabric do Azure:

![Principais cenários de Reliable Actors do Service Fabric][1]

Os Reliable Actors do Service Fabric são os principais blocos de construção (como uma camada intermediária) em um sistema que combina um front-end de sistema de mensagens que oferece suporte a vários transportes, como HTTPS, MQTT e AMQP, e a comunicação com atores que representam dispositivos individuais. Como os atores podem manter o estado, modelar fluxos (especialmente o processamento de fluxo com estado) e a agregação por dispositivo é uma ação direta. Se os dados tiverem de ser mantidos, você também poderá liberá-los com facilidade sob demanda ou usando um temporizador. Isso pode ocorrer enquanto você ainda mantém os últimos N bits de dados em outra variável para consulta rápida.

Observe que, para manter o foco no modelo dos Reliable Actors em nossos exemplos, omitimos intencionalmente os detalhes da camada de eventos/mensagens, o que permite aos atores se comunicarem com dispositivos. Existem, basicamente, dois cenários e normalmente eles são compostos juntos:

* **Coletar dados de estado e telemetria de um único dispositivo ou de um conjunto de dispositivos e manter seu estado**. Pense em dezenas de milhares de ratoeiras (esse é um cenário real de cliente) enviando dados. Esses dados são tão simples de ler quanto saber se a armadilha pegou ou não o rato. Tais dados são agregados por região, e quando ratos suficientes tiverem sido pegos em uma região, um engenheiro será enviado para fazer a limpeza das armadilhas. Uma ratoeira como um ator? Sim. Um ator de grupo por região como o agregador? Com certeza.

* **Enviar por push o comportamento e a configuração do dispositivo para um único dispositivo ou um conjunto de dispositivos**. Pense em aparelhos movidos por energia solar, em que o fornecedor envia por push diferentes configurações baseadas em padrões de consumo e sazonalidade.

## Dados de telemetria e agrupamento de dispositivos

Primeiramente, vamos examinar o caso em que dispositivos (pense em dezenas de milhares) são agrupados e todos estão enviando dados de telemetria ao respectivo grupo associado. No exemplo a seguir, o cliente implantou dispositivos em cada região. Quando um dispositivo é ativado, a primeira coisa que ele faz é enviar uma mensagem **ActivateMe** com informações relevantes que incluem local e versão. O ator associado ao dispositivo (por meio da ID do dispositivo) define o estado inicial do dispositivo, como salvando estado localmente (isso também pode ser persistido) e registrando um ator de grupo. Nesse caso, atribuímos um grupo aleatório para nossa simulação.

Como parte do processo de inicialização, podemos configurar o dispositivo recuperando dados de configuração de um ator de grupo ou de algum outro agente. Dessa forma, inicialmente, os dispositivos podem parecer bastante "verdes", mas ficam mais "maduros" na inicialização. Depois que isso é feito, o dispositivo e o ator estão prontos para enviar e processar dados de telemetria.

Todos os dispositivos enviam periodicamente suas informações de telemetria para os atores correspondentes. Se um ator já estiver ativado, o mesmo ator será usado. Caso contrário, ele será ativado. Nesse ponto, ele pode recuperar o estado de um repositório estável, se isso for necessário. Quando o ator recebe informações de telemetria, ele as armazena em uma variável local.

Estamos fazendo isso para simplificar o exemplo. Em uma implementação real, provavelmente, poderíamos salvá-las em um repositório externo. Isso permitiria que as operações monitorassem e diagnosticassem a integridade e o desempenho do dispositivo. Por fim, enviamos por push dados de telemetria para o ator de grupo ao qual o ator do dispositivo pertence de forma lógica.

## Exemplo de código de IoT: telemetria

```csharp
public interface IThing : IActor
{
    Task ActivateMe(string region, int version);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingState
{
	[DataMember]
	public List<ThingTelemetry> _telemetry;
	[DataMember]
	public ThingInfo _deviceInfo;
	[DataMember]
	long _deviceGroupId;
}

public class Thing : StatefulActor<ThingState>, IThing
{

    protected override Task OnActivateAsync()
    {
        State._telemetry = new List<ThingTelemetry>();
        State._deviceGroupId = -1; // not activated
        return base.OnActivateAsync();
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        State._telemetry.Add(telemetry); // saving data at the device level
        if (State._deviceGroupId != -1)
        {
            var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
            return deviceGroup.SendTelemetryAsync(telemetry); // sending telemetry data for aggregation
        }
        return Task.FromResult(true);
    }

    public Task ActivateMe(string region, int version)
    {
        State._deviceInfo = new ThingInfo()
        {
            DeviceId = this.GetPrimaryKeyLong(),
            Region = region,
            Version = version
        };

        // based on the info, assign a group... for demonstration we are assigning a random group
        State._deviceGroupId = new Random().Next(10, 12);

        var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
        return deviceGroup.RegisterDevice(State._deviceInfo);
    }
}
```

No nível de grupo, a meta em nosso exemplo é monitorar os dispositivos no grupo e agregar dados de telemetria a fim de produzir alertas para engenheiros. Nesse caso, nosso cliente deseja enviar engenheiros a regiões específicas onde um determinado número de dispositivos está com defeito. É óbvio que nosso cliente também deseja reduzir os custos, minimizando o tempo que a engenharia gasta em trânsito. Por esse motivo, cada ator de grupo mantém um estado agregado de dispositivos com falhas por região. Quando esse número atinge um limite, nosso cliente despacha um engenheiro para a região a fim de reparar ou substituir os dispositivos com defeito.

Vamos examinar como isso é feito:

## Exemplo de código de IoT: agrupamento e agregação

```csharp
public interface IThingGroup : IActor
{
    Task RegisterDevice(ThingInfo deviceInfo);
    Task UnregisterDevice(ThingInfo deviceInfo);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingGroupState
{
    [DataMember]
    public List<ThingInfo> _devices;
    [DataMember]
    public Dictionary<string, int> _faultsPerRegion;
    [DataMember]
    public List<ThingInfo> _faultyDevices;
}

public class ThingGroup : StatefulActor<ThingGroupState>, IThingGroup
{

    protected override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return Task.FromResult(true);
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return Task.FromResult(true);
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        if (telemetry.DevelopedFault)
        {
            if (false == _faultsPerRegion.ContainsKey(telemetry.Region))
            {
                State._faultsPerRegion[telemetry.Region] = 0;
            }
            State._faultsPerRegion[telemetry.Region]++;
            State._faultyDevices.Add(_devices.Where(d => d.DeviceId == telemetry.DeviceId).FirstOrDefault());

            if (State._faultsPerRegion[telemetry.Region] > _devices.Count(d => d.Region == telemetry.Region) / 3)
            {
                Console.WriteLine("Sending an engineer to repair/replace devices in {0}", telemetry.Region);
                foreach(var device in State._faultyDevices.Where(d => d.Region == telemetry.Region).ToList())
                {
                    Console.WriteLine("\t{0}", device);
                }
            }
        }

        return Task.FromResult(true);
    }
}
```

Como você pode ver, é um processo relativamente simples e direto. Depois de executar testes simples, o resultado se parecerá com este:

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

Você pode pensar que teria sido bem melhor se os dispositivos fossem agrupados por região. É inteiramente de sua responsabilidade como você escolhe agrupar e/ou particionar dispositivos, se por localização geográfica, tipo de dispositivo, versão, locatário ou outros critérios.

Um ponto que deve ser observado no estado do dispositivo versus relatório e análise: é preciso evitar consultas do tipo fan-out aos atores para criar atores de relatório. Isso pode causar problemas desnecessários de desempenho e instanciação, apenas para citar duas desvantagens. Por isso tornamos explícita a ilustração do padrão. Duas abordagens são recomendadas para relatórios:

* Use um ator no nível de grupo, como um agregador, para manter uma exibição para o grupo. Permita que cada ator envie por push de modo proativo apenas dados relevantes a esse ator. O ator no nível de grupo pode ser usado para exibir o status dos dispositivos no grupo.

* Mantenha um repositório explícito que foi projetado para relatórios. Um agregador pode armazenar em buffer e enviar dados por push periodicamente a um repositório de relatórios para análise e consulta posteriores.

## Operação do dispositivo
Até agora, tudo bem. Mas e quanto às operações nesses dispositivos? Assim como acontece com dispositivos, os atores podem expor interfaces operacionais para que um administrador possas executar operações nos dispositivos. Por exemplo, pense em um administrador que queira enviar por push uma nova configuração para um grupo de aparelhos domésticos movidos por energia solar (outro cenário da vida real) com base nas mudanças sazonais e regionais.

A principal ideia aqui é manter controle granular sobre cada aparelho usando atores "Thing", bem como o controle sobre as operações do grupo usando atores "ThingGroup". Isso se aplica se você estiver agregando dados, como os de telemetria enviados por dispositivos, ou se estiver enviando dados, como configuração para um grande número de aparelhos. A plataforma se encarrega da distribuição dos atores do dispositivo, bem como do serviço de mensagens entre eles. Isso permanece totalmente transparente para o desenvolvedor.

Para comunicações M2M (computador para computador), o padrão hub e spoke, que abordamos [na seção sobre redes e gráficos distribuídos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md), e a interação direta de ator para ator funciona bem. Em cenários M2M, você pode modelar um ator "Directory/Index" para um grupo de dispositivos que os permitiria descobrir e enviar mensagens uns aos outros, conforme ilustrado abaixo:

![Modelo de um ator Diretório/Índice para um grupo de dispositivos][2]

O modelo dos Reliable Actors do Service Fabric também cuida dos tempos de vida dos atores. Pense dessa forma: se você tem dispositivos sempre ativos, bem como dispositivos que são ocasionalmente conectados, por que manter na memória um ator que tome conta de um dispositivo que se conecta a cada 14 horas? O Service Fabric permite salvar e restaurar o estado de um dispositivo quando você o desejar e onde o desejar.

Cada vez mais clientes olharão para os Reliable Actors do Service Fabric como blocos de construção principais para as respectivas implementações da IoT.

## Próximas etapas
[Padrão: cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Padrão: redes e gráficos distribuídos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Padrão: governança de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Padrão: composição de serviço com estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Padrão: computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

[Introdução aos Atores da Malha do Serviço](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=AcomDC_0121_2016-->