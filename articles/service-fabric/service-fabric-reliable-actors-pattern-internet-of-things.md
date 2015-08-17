
<properties
   pageTitle="Atores Confiáveis para a Internet das Coisas"
   description="Os Atores Confiável do Service Fabric são os principais blocos de construção (como uma camada intermediária) em um sistema que combina um front-end de sistema de mensagens que dá suporte a vários transportes como HTTPS, MQTT ou AMQP, e então se comunica com atores que representam dispositivos individuais."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="claudioc"/>

# Padrão de design de Atores Confiáveis: Internet das Coisas
Desde que a IoT se tornou a nova tendência juntamente com os avanços tecnológicos em dispositivos e serviços de nuvem, os desenvolvedores começaram a examinar os principais blocos de construção nos quais desenvolver seus sistemas. O diagrama a seguir ilustra os principais cenários obtidos usando os Atores Confiáveis do Service Fabric:

![][1]

Os Atores Confiável do Service Fabric são os principais blocos de construção (como uma camada intermediária) em um sistema que combina um front-end de sistema de mensagens que dá suporte a vários transportes como HTTPS, MQTT ou AMQP, e então se comunica com atores que representam dispositivos individuais. Como os atores podem manter o estado, modelar fluxos (especialmente processamento de fluxo com estado) e agregação por dispositivo é uma ação direta. Se os dados devem ser persistidos, também podemos liberá-los facilmente sob demanda ou de acordo com um temporizador, enquanto ainda mantemos facilmente os últimos N bits de dados em outra variável para consulta rápida. Observe que em nossos exemplos, omitimos deliberadamente os detalhes da camada de evento/mensagens, o que permite que os atores se comuniquem com dispositivos, a fim de manter o foco no modelo de ator. Basicamente, existem dois cenários que geralmente são compostos juntos:

* *Coletando dados de estado e telemetria de um único dispositivo ou de um conjunto de dispositivos e mantendo seu estado*. Pense em dezenas de milhares de ratoeiras (sim, esse é um cenário de cliente real) enviando dados, tão simples como se a armadilha tivesse capturado um desses bichos asquerosos. Os dados são agregados por região, e com ratos suficientes apanhados em armadilhas em uma região, um engenheiro é despachado para limpar as ratoeiras (dispositivos). Uma ratoeira como um ator? Com certeza. Um ator de grupo por região como o agregador? Pode apostar que sim.

* *Enviando por push o comportamento e a configuração do dispositivo para um único dispositivo ou um conjunto de dispositivos*. Pense em dispositivos movidos por energia solar, em que o fornecedor envia por push diferentes configurações baseadas em padrões de consumo e sazonalidade.

## Dados de telemetria e agrupamento de dispositivos

Primeiramente, vamos examinar o caso em que dispositivos, pense em dezenas de milhares, são agrupados e todos estão enviando dados de telemetria ao respectivo grupo associado. No exemplo a seguir, o cliente implantou dispositivos em cada região. Quando o dispositivo é ativado, a primeira coisa que ele faz é enviar uma mensagem ActivateMe com informações relevantes, como local, versão, etc. Por sua vez, o ator associado ao dispositivo (por meio da ID do Dispositivo) define o estado inicial do dispositivo, como salvando estado localmente (poderia ser persistido também) e registrando um ator de grupo. Nesse caso, atribuímos um grupo aleatório para nossa simulação.

Como parte do processo de inicialização, podemos configurar o dispositivo recuperando dados de configuração de um ator de grupo ou de algum outro agente. Dessa forma, inicialmente, os dispositivos podem parecer bastante “verdes”, mas ficam mais “maduros” na inicialização. Depois que isso é feito, o dispositivo e o ator estão prontos para enviar e processar dados de telemetria.

Todos os dispositivos enviam periodicamente suas informações de telemetria para o ator correspondente. Se o ator já estiver ativado, o mesmo ator será usado. Caso contrário, ele será ativado. Nesse ponto, ele pode recuperar o estado de um repositório estável, se necessário. Quando o ator recebe informações de telemetria, ele as armazena em uma variável local. Estamos fazendo isso para simplificar o exemplo. Em uma implementação real, provavelmente, nós as salvaríamos em um repositório externo para permitir que as operações monitorassem e diagnosticassem a integridade e o desempenho do dispositivo. Por fim, enviamos por push dados de telemetria para o ator de grupo ao qual o ator desse dispositivo pertence de forma lógica.

## Exemplo de código de IoT – telemetria

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

public class Thing : Actor<ThingState>, IThing
{

    public override Task OnActivateAsync()
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
        return TaskDone.Done;
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

No nível de grupo, de acordo com o nosso exemplo, nossa meta é monitorar os dispositivos no grupo e agregar dados de telemetria a fim de produzir alertas para engenheiros. Nesse caso, nosso cliente deseja enviar engenheiros a regiões específicas onde há um determinado número de dispositivos com falha. É óbvio que nosso cliente deseja reduzir os custos minimizando o tempo que a engenharia gasto em trânsito. Por esse motivo, cada ator de grupo mantém um estado agregado de dispositivos com falhas por região. Quando esse número atinge um limite, nosso cliente despacha um engenheiro para a região a fim de substituir/reparar esses dispositivos. Vamos examinar como isso é feito:

## Exemplo de código de IoT – agrupamento e agregação

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

public class ThingGroup : Actor<ThingGroupState>, IThingGroup
{

    public override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return TaskDone.Done;
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return TaskDone.Done;
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

        return TaskDone.Done;
    }
}
```

Como você pode ver, é um processo bastante direto. Depois de executar testes simples, a saída se parece com esta:

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

A propósito, você pode pensar que teria sido melhor se os dispositivos fossem agrupados por região. Naturalmente, é de nossa inteira responsabilidade como agrupar/particionar dispositivos — se por localização geográfica, tipo de dispositivo, versão, locatário, etc. Há um ponto delicado aqui: estado do dispositivo x relatório/análise. Por isso tornamos explícita a ilustração do padrão. Devemos evitar consultas do tipo fan-out a atores para criar atores de relatório; desempenho e instanciações desnecessárias, apenas para citar duas desvantagens. Duas abordagens são recomendadas para relatórios:

* Use ator no nível de grupo, como um agregador, para manter uma exibição para o grupo. Permita que cada ator envie por push apenas dados relevantes a esse ator de modo proativo. Em seguida, esse ator no nível de grupo poderá ser usado para exibir o status dos dispositivos no grupo.

* Mantenha um repositório explícito que foi projetado para relatórios. Um agregador pode armazenar em buffer e enviar dados por push periodicamente a um repositório de relatórios para análise e consulta posteriores.

## Operação do dispositivo
Até aqui tudo bem, mas e quanto às operações nesses dispositivos? Assim como em dispositivos, os atores podem expor interfaces operacionais para que um administrador possa realizar operações em dispositivos. Por exemplo, um administrador deseja enviar por push uma nova configuração para um grupo de dispositivos que funcionam por energia solar (sim, outro cenário da vida real) com base nas mudanças sazonais/regionais.

A principal ideia aqui é que temos controle granular sobre cada dispositivo que está usando atores “Thing”, bem como sobre as operações de grupo que estão usando atores “ThingGroup” — seja para agregar dados que chegam de dispositivos, como telemetria, seja para enviar dados, como configuração para um grande número de dispositivos. A plataforma se encarrega da distribuição dos atores de dispositivo e das mensagens entre eles, que é totalmente transparente para o desenvolvedor.

Quando se trata de comunicações de computador para computador (M2M), o padrão Hub e Spoke, que discutimos na seção de gráficos e redes distribuídos ou na interação direta de ator para ator, funciona bem. Em cenários M2M, podemos modelar um ator “Directory/Index” para um grupo de dispositivos, permitindo que eles detectem e enviem mensagens uns aos outros, conforme ilustrado abaixo:

![][2]

Os Atores da Malha do Serviço do Azure também cuidam do tempo de vida dos Atores. Pense nisso dessa maneira: teremos dispositivos sempre conectados e dispositivos conectados ocasionalmente. Por que manteríamos na memória o ator que cuida do dispositivo que se conecta a cada 14 horas? A Malha do Serviço do Azure permite salvar e restaurar o estado do dispositivo quando o queremos e onde o queremos.

Concluímos que mais e mais clientes olharão para os Atores da Malha do Serviço do Azure como um importante bloco de construção para suas implementações de IoT.

## Próximas etapas
[Padrão: cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Padrão: redes e gráficos distribuídos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Padrão: controle de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Padrão: composição de serviço com estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Padrão: computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

[Introdução aos Atores da Malha do Serviço](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=August15_HO6-->