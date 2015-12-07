<properties
   pageTitle="Uso avançado do modelo de programação de Serviços Confiáveis"
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
   ms.date="11/17/2015"
   ms.author="jesseb"/>

# Uso avançado do modelo de programação de Serviços Confiáveis
A Malha de Serviços simplifica o desenvolvimento e o gerenciamento de serviços confiáveis com e sem estado. Este guia tratará dos usos avançados do modelo de programação de Serviços Confiáveis para obter mais controle e flexibilidade sobre seus serviços. Antes de ler este guia, familiarize-se com [o do modelo de programação de Serviços Confiáveis](service-fabric-reliable-services-introduction.md).

## Classes base de serviço sem monitoração de estado
A classe base StatelessService fornece o RunAsync() e o CreateServiceInstanceListeners(), que são suficientes para a maioria dos serviços sem estado. A classe StatelessServiceBase fica subjacente ao StatelessService e expõe eventos adicionais de ciclo de vida do serviço. Você pode derivar de StatelessServiceBase se precisar de controle adicional ou flexibilidade. Consulte a documentação de referência do desenvolvedor em [StatelessService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservice.aspx) e [StatelessServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservicebase.aspx) para obter mais informações.

- `void OnInitialize(StatelessServiceInitializiationParameters)` OnInitialize é o primeiro método chamado pela Malha de Serviço. São fornecidas informações de inicialização do serviço como o nome, id de partição, id de instância e informações do pacote de códigos. Nenhum processamento complexo deve ser feito aqui. Inicialização longa deve ser feita em OnOpenAsync.

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` OnOpenAsync é chamado quando a instância do serviço sem monitoração de estado está prestes a ser usada. As tarefas de inicialização do serviço estendido podem ser iniciadas nesse momento.

- `Task OnCloseAsync(CancellationToken)` OnCloseAsync é chamado quando a instância do serviço sem monitoração de estado está para ser normalmente desligada. Isso pode ocorrer quando o código de serviço estiver sendo atualizado, quando a instância do serviço estiver sendo movida devido ao balanceamento de carga ou quando for detectada uma falha temporária. OnCloseAsync pode ser usado para fechar todos os recursos com segurança, interromper todos os processamentos em segundo plano, terminar de salvar o estado externo ou fechar conexões existentes.

- `void OnAbort()` OnAbort é chamado quando a instância do serviço sem monitoração de estado está para ter um desligamento forçado. Ele geralmente é chamado quando é detectada uma falha permanente no nó ou quando a malha de serviço não pode gerenciar confiavelmente o ciclo de vida da instância do serviço devido a falhas internas.

## Classes base de serviço com monitoração de estado
A classe base StatefulService deve ser suficiente para a maioria dos serviços com monitoração de estado. De maneira semelhante aos serviços sem monitoração de estado, a classe StatelessServiceBase fica subjacente ao StatefulService e expõe eventos adicionais de ciclo de vida do serviço. Além disso, ela permite o fornecimento de um provedor de estado confiável personalizado e, opcionalmente, o suporte a ouvintes de comunicação em secundários. Você pode derivar de StatefulServiceBase se precisar de controle adicional ou flexibilidade. Consulte a documentação de referência do desenvolvedor em [StatefulService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservice.aspx) e [StatefulServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservicebase.aspx) para obter mais informações.

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync é chamado quando o serviço com estado está alterando funções, por exemplo para Primário ou Secundário. Réplicas primárias recebem status de gravação (têm permissão para criar e gravar nas coleções confiáveis), enquanto as réplicas Secundárias recebem status de leitura (pode somente ler o conteúdo das coleções confiáveis existentes). É possível iniciar ou atualizar as tarefas em segundo plano em resposta às alterações de função, como realizar validação somente leitura, geração de relatórios ou mineração de dados em um banco de dados Secundário.

- `IStateProviderReplica CreateStateProviderReplica()` Um serviço com estado deve ter um provedor de estado confiável. StatefulService usa a classe ReliableStateManager, que fornece as coleções confiáveis (por exemplo, dicionários e filas). Você pode substituir esse método para configurar a classe ReliableStateManager, passando um ReliableStateManagerConfiguration para seu construtor. Isso permite que você forneça serializadores de estado personalizado, especifique o que acontece quando os dados podem ter sido perdidos e configure os provedores de estado/replicador.

StatefulServiceBase também fornece os mesmos quatro eventos do ciclo de vida que StatelessServiceBase, com a mesma semântica e casos de uso:

- `void OnInitialize(StatefulServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## Próximas etapas
Para tópicos mais avançados relacionados à Malha de Serviço, consulte os artigos a seguir.

- [Configurando Serviços Confiáveis com estado](service-fabric-reliable-services-configuration.md)

- [Introdução à integridade da Malha do Serviço](service-fabric-health-introduction.md)

- [Usando relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Visão geral de Restrições de Posicionamento](service-fabric-placement-constraint.md)

<!---HONumber=AcomDC_1125_2015-->