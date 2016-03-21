<properties
   pageTitle="Uso avançado do modelo de programação de Reliable Services | Microsoft Azure"
   description="Saiba mais sobre o uso avançado do modelo de programação de Serviços Confiáveis da Malha de Serviços para obter maior flexibilidade em seus serviços."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/28/2016"
   ms.author="jesseb"/>

# Uso avançado do modelo de programação de Serviços Confiáveis
O Service Fabric do Azure simplifica o desenvolvimento e o gerenciamento de serviços confiáveis com e sem estado. Este guia tratará dos usos avançados do modelo de programação de Serviços Confiáveis para obter mais controle e flexibilidade sobre seus serviços. Antes de ler este guia, familiarize-se com [o do modelo de programação de Serviços Confiáveis](service-fabric-reliable-services-introduction.md).

## Classes base para serviços sem estado
A classe base StatelessService fornece o RunAsync() e o CreateServiceInstanceListeners(), que são suficientes para a maioria dos serviços sem estado. A classe StatelessServiceBase fica subjacente ao StatelessService e expõe eventos adicionais de ciclo de vida do serviço. Você pode derivar de StatelessServiceBase se precisar de controle adicional ou flexibilidade. Consulte a documentação de referência do desenvolvedor em [StatelessService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservice.aspx) e [StatelessServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservicebase.aspx) para obter mais informações.

- `void OnInitialize(StatelessServiceInitializiationParameters)` OnInitialize é o primeiro método chamado pela Malha de Serviço. São fornecidas informações de inicialização do serviço como o nome, ID de partição, ID de instância e informações do pacote de códigos. Nenhum processamento complexo deve ser feito aqui. Inicialização longa deve ser feita em OnOpenAsync.

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` OnOpenAsync é chamado quando a instância do serviço sem monitoração de estado está prestes a ser usada. As tarefas de inicialização do serviço estendido podem ser iniciadas nesse momento.

- `Task OnCloseAsync(CancellationToken)` OnCloseAsync é chamado quando a instância do serviço sem estado está prestes a ser desligado de modo normal. Isso pode ocorrer quando o código de serviço estiver sendo atualizado, quando a instância do serviço estiver sendo movida devido ao balanceamento de carga ou quando for detectada uma falha temporária. OnCloseAsync pode ser usado para fechar todos os recursos com segurança, interromper todos os processamentos em segundo plano, terminar de salvar o estado externo ou fechar conexões existentes.

- `void OnAbort()` OnAbort é chamado quando a instância do serviço sem estado está sendo desligado de modo forçado. Ele geralmente é chamado quando é detectada uma falha permanente no nó ou quando a malha de serviço não pode gerenciar confiavelmente o ciclo de vida da instância do serviço devido a falhas internas.

## Classes base para serviços com estado
A classe base StatefulService deve ser suficiente para a maioria dos serviços com monitoração de estado. De maneira semelhante aos serviços sem monitoração de estado, a classe StatelessServiceBase fica subjacente ao StatefulService e expõe eventos adicionais de ciclo de vida do serviço. Além disso, você pode usá-la para fornecer um provedor de estado confiável personalizado e, opcionalmente, o suporte a ouvintes de comunicação em secundários. Você pode derivar de StatefulServiceBase se precisar de controle adicional ou flexibilidade. Consulte a documentação de referência do desenvolvedor em [StatefulService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservice.aspx) e [StatefulServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservicebase.aspx) para obter mais informações.

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync é chamado quando o serviço com estado está alterando funções, por exemplo para primário ou secundário. Réplicas primárias recebem status de gravação (têm permissão para criar as coleções confiáveis e gravar nelas). Réplicas secundárias recebem status de leitura (podem somente ler das coleções confiáveis existentes). É possível iniciar ou atualizar as tarefas em segundo plano em resposta às alterações de função, como realizar validação somente leitura, geração de relatórios ou mineração de dados em um banco de dados secundário.

- `IStateProviderReplica CreateStateProviderReplica()` Um serviço com estado deve ter um provedor de estado confiável. StatefulService usa a classe ReliableStateManager, que fornece as coleções confiáveis (por exemplo, dicionários e filas). Você pode substituir esse método para configurar a classe ReliableStateManager, passando um ReliableStateManagerConfiguration para seu construtor. Você pode, então, fornecer serializadores de estado personalizado, especificar o que acontece quando dados talvez tenham sido perdidos e configurar os provedores de estado/replicador.

StatefulServiceBase também fornece os mesmos quatro eventos do ciclo de vida que StatelessServiceBase, com a mesma semântica e casos de uso:

- `void OnInitialize(StatefulServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## Próximas etapas
Para tópicos mais avançados relacionados ao Service Fabric, consulte os artigos a seguir:

- [Configurando Reliable Services com estado](service-fabric-reliable-services-configuration.md)

- [Introdução à integridade da Malha do Serviço](service-fabric-health-introduction.md)

- [Usando relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Configurando serviços com o Gerenciador de Recursos de Cluster do Service Fabric](service-fabric-cluster-resource-manager-configure-services.md)

<!---HONumber=AcomDC_0309_2016-->