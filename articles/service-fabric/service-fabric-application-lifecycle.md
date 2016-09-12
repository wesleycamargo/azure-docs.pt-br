<properties
   pageTitle="Ciclo de vida do aplicativo no Service Fabric | Microsoft Azure"
   description="Descreve o desenvolvimento, implantação, testes, atualização, manutenção e remoção de aplicativos da Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# Ciclo de vida do aplicativo Service Fabric
Semelhante a outras plataformas, um aplicativo no Azure Service Fabric geralmente passa pelas seguintes fases: design, desenvolvimento, teste, implantação, atualização, manutenção e remoção. O Service Fabric dá um excelente suporte ao ciclo de vida completo dos aplicativos em nuvem, desde o desenvolvimento até a implantação, gerenciamento diário, manutenção e possível encerramento. O modelo de serviço permite que várias funções diferentes participem do ciclo de vida do aplicativo de forma independente. Este artigo fornece uma visão geral das APIs e como elas são usadas pelas diferentes funções em todas as fases do ciclo de vida de um aplicativo da Malha do Serviço.

## Funções do modelo de serviço
As funções do modelo de serviço são:

- **Desenvolvedor de serviço**: desenvolve serviços modulares e genéricos que podem ser redefinidos e usados em vários aplicativos do mesmo tipo ou de tipos diferentes. Por exemplo, um serviço de fila pode ser usado para criar um aplicativo de emissão de tíquetes (assistência técnica) ou um aplicativo de comércio eletrônico (carrinho de compras).

- **Desenvolvedor de aplicativos**: cria aplicativos integrando uma coleção de serviços para atender a determinados requisitos ou cenários. Por exemplo, um site de comércio eletrônico pode integrar o “Serviço de Front-End JSON sem Monitoração de Estado”, "Serviço de Monitoração de Estado do Leilão" e "Serviço de Monitoração de Estado da Fila" para criar uma solução de leilão.

- **Administrador do aplicativo**: toma decisões sobre a configuração do aplicativo (preenchendo os parâmetros de configuração do modelo), implantação (mapeamento os recursos disponíveis) e qualidade do serviço. Por exemplo, um administrador do aplicativo decide a localidade do idioma do aplicativo (inglês para os EUA ou japonês para o Japão, por exemplo). Outro aplicativo implantado pode ter configurações diferentes.

- **Operador**: implanta os aplicativos baseados na configuração do aplicativo e nos requisitos especificados pelo administrador do aplicativo. Por exemplo, um operador provisiona, implanta o aplicativo e verifica se ele está em execução no Azure. Os operadores de monitoram as informações de integridade e de desempenho do aplicativo e mantêm a infra-estrutura física conforme necessário.


## Desenvolver
1. Um *desenvolvedor de serviço* desenvolve tipos diferentes de serviços usando o modelo de programação [Reliable Actors](service-fabric-reliable-actors-introduction.md) ou [Reliable Services](service-fabric-reliable-services-introduction.md).
2. Um *desenvolvedor de serviço* descreve declarativamente os tipos de serviço desenvolvidos em um arquivo de manifesto do serviço que consiste em um ou mais pacotes de código, de configuração e de dados.
3. Um *desenvolvedor de aplicativos* cria um aplicativo usando diferentes tipos de serviço.
4. Um *desenvolvedor de aplicativos* descreve declarativamente o tipo de aplicativo em um manifesto de aplicativo referenciando os manifestos do serviço dos serviços membros e substituindo e parametrizando adequadamente as diferentes configurações de implantação e configurações dos serviços membros.

Confira [Introdução aos Reliable Actors](service-fabric-reliable-actors-get-started.md) e [Introdução aos Reliable Services](service-fabric-reliable-services-quick-start.md) para obter exemplos.

## Implantar
1. Um *administrador do aplicativo* personaliza o tipo de aplicativo para um aplicativo específico a ser implantado para um cluster Service Fabric especificando os parâmetros apropriados do elemento **ApplicationType** no manifesto do aplicativo.

2. Um *operador* carrega o pacote de aplicativos para o armazenamento de imagens do cluster usando o método [**CopyApplicationPackage**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) ou o [**cmdlet Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx). O pacote de aplicativo contém o manifesto do aplicativo e a coleção de pacotes de serviço. O Service Fabric implanta os aplicativos do pacote de aplicativos colocado no armazenamento de imagens, que pode ser um armazenamento de blobs do Azure ou o serviço do sistema Service Fabric.

3. O *operador*, então, configura o tipo de aplicativo no cluster de destino do pacote de aplicativos carregado usando o método [**ProvisionApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), o cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) ou a [**operação REST** Provisionar um aplicativo](https://msdn.microsoft.com/library/azure/dn707672.aspx).

4. Após a configuração do aplicativo, um *operador* inicia o aplicativo com os parâmetros fornecidos pelo *administrador do aplicativo* usando o método [**CreateApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), o cmdlet [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) ou a operação REST [**Criar Aplicativo**](https://msdn.microsoft.com/library/azure/dn707676.aspx).

5. Depois do aplicativo ser implantado, um *operador* usa o método [**CreateServiceAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), o cmdlet [**New-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125874.aspx) ou a operação REST [**Criar Serviço**](https://msdn.microsoft.com/library/azure/dn707657.aspx) para criar novas instâncias de serviço para o aplicativo com base nos tipos de serviço disponíveis.

6. O aplicativo está sendo executado no cluster da Malha do Serviço.

Confira [implantar um aplicativo](service-fabric-deploy-remove-applications.md) para obter exemplos.

## Teste
1. Depois de implantar o cluster de desenvolvimento local ou um cluster de teste, um *desenvolvedor de serviço* executa o cenário de teste de failover interno usando as classes [**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) e [**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) ou o cmdlet [**Invoke-ServiceFabricFailoverTestScenario**](https://msdn.microsoft.com/library/azure/mt644783.aspx). O cenário de teste de failover executa um determinado serviço através de transições e failovers importantes para garantir que ainda estará disponível e funcionando.

2. O *desenvolvedor de serviço* executa o cenário de teste de caos interno usando as classes [**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) e [**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) ou o cmdlet [**Invoke-ServiceFabricChaosTestScenario**](https://msdn.microsoft.com/library/azure/mt644774.aspx). O cenário de teste caos induz vários nós, pacote de código e falhas de réplica aleatoriamente no cluster.

3. O *desenvolvedor de serviço* [testa a comunicação entre serviços](service-fabric-testability-scenarios-service-communication.md) por meio da criação de cenários de teste que movem réplicas primárias pelo cluster.

Consulte [Introduction to the Fault Analysis Service (Introdução ao Fault Analysis Service)](service-fabric-testability-overview.md) para obter mais informações.

## Atualizar
1. Um *desenvolvedor de serviço* atualiza os serviços membros do aplicativo instanciado e/ou corrige erros e fornece uma nova versão do manifesto do serviço.

2. Um *desenvolvedor de aplicativos* substitui e parametriza as configurações de implantação e configuração dos serviços membros e fornece uma nova versão do manifesto do aplicativo. O desenvolvedor do aplicativo incorpora as novas versões dos manifestos do serviço no aplicativo e fornece uma nova versão do tipo de aplicativo em um pacote de aplicativo atualizado.

3. Um *administrador do aplicativo* incorpora a nova versão do tipo de aplicativo no aplicativo de destino atualizando os parâmetros apropriados.

5. Um *operador* carrega o pacote de aplicativos atualizado no armazenamento de imagens de cluster usando o método [**CopyApplicationPackage**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) ou o cmdlet [**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx). O pacote de aplicativo contém o manifesto do aplicativo e a coleção de pacotes de serviço.

6. Um *operador* configura a nova versão do aplicativo no cluster de destino usando o método [**ProvisionApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), o cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) ou a operação REST [**Configurar um Aplicativo**](https://msdn.microsoft.com/library/azure/dn707672.aspx).

7. Um *operador* atualiza o aplicativo de destino para a nova versão usando o [**método UpgradeApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), o [**cmdlet Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) ou a operação REST [**Atualizar um Aplicativo**](https://msdn.microsoft.com/library/azure/dn707633.aspx).

8. Um *operador* verifica o progresso de atualização usando o método [**GetApplicationUpgradeProgressAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), o cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) ou a operação REST [**Obter Progresso de Atualização do Aplicativo**](https://msdn.microsoft.com/library/azure/dn707631.aspx).

9. Se necessário, o *operador* modifica e reaplica os parâmetros da atualização do aplicativo atual usando o método [**UpdateApplicationUpgradeAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), o cmdlet [**Update-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt126030.aspx) ou a operação REST [**Fazer Atualização do Aplicativo**](https://msdn.microsoft.com/library/azure/mt628489.aspx).

10. Se necessário, o *operador* reverte a atualização do aplicativo atual usando o método [**RollbackApplicationUpgradeAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), o cmdlet [**Start-ServiceFabricApplicationRollback**](https://msdn.microsoft.com/library/azure/mt125833.aspx) ou a operação REST [**Reverter Atualização do Aplicativo**](https://msdn.microsoft.com/library/azure/mt628494.aspx).

11. A Malha do Serviço atualiza o aplicativo de destino em execução no cluster sem perder a disponibilidade de qualquer um dos seus serviços membros.

Consulte [Tutorial sobre a atualização do aplicativo](service-fabric-application-upgrade-tutorial.md) para obter exemplos.

## Manter
1. Para patches e atualizações do sistema operacional, a Malha do Serviço faz a interface com a infraestrutura do Azure para garantir a disponibilidade de todos os aplicativos executados no cluster.

2. Para ver as atualizações e as correções para a plataforma Service Fabric, o Service Fabric se atualiza automaticamente sem perder a disponibilidade de qualquer aplicativo em execução no cluster.

3. Um *administrador do aplicativo* aprova a adição ou remoção de nós de um cluster depois de analisar os dados do histórico de utilização da capacidade e demanda futura projetada.

4. Um *operador* adiciona e remove nós especificados pelo *administrador do aplicativo*.

5. Quando novos nós são adicionados ou os nós existentes são removidos do cluster, o Service Fabric balanceia a carga automaticamente dos aplicativos em execução em todos os nós do cluster para obter o desempenho ideal.

## Remover
1. Um *operador* pode excluir uma instância específica de um serviço em execução no cluster sem remover o aplicativo inteiro usando o método [**DeleteServiceAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), o cmdlet [**Remove-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt126033.aspx) ou a operação REST [**Excluir Serviço**](https://msdn.microsoft.com/library/azure/dn707687.aspx).

2. Um *operador* também pode excluir uma instância do aplicativo e todos os seus serviços usando o método [**DeleteApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), o cmdlet [**Remove-ServiceFabricApplication** ](https://msdn.microsoft.com/library/azure/mt125914.aspx) ou a operação REST [**Excluir Aplicativo**](https://msdn.microsoft.com/library/azure/dn707651.aspx).

3. Depois dos aplicativos e serviços serem interrompidos, o *operador* pode desconfigurar o tipo de aplicativo usando o método [**UnprovisionApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), o cmdlet [**Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx) ou a operação REST [**Desconfigurar um Aplicativo**](https://msdn.microsoft.com/library/azure/dn707671.aspx). Desconfigurar o tipo de aplicativo não remove o pacote de aplicativos do ImageStore. Você deve remover manualmente o pacote de aplicativos.

4. Um *operador* remove o pacote de aplicativos do ImageStore usando o método [**RemoveApplicationPackage**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) ou o cmdlet [**Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx).

Confira [implantar um aplicativo](service-fabric-deploy-remove-applications.md) para obter exemplos.

## Próximas etapas

Para saber mais sobre o desenvolvimento, o teste e o gerenciamento de aplicativos e serviços da Malha do Serviço, confira:

- [Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)
- [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)
- [Atualização do aplicativo](service-fabric-application-upgrade.md)
- [Visão geral da Possibilidade de Teste](service-fabric-testability-overview.md)
- [Exemplo do ciclo de vida de aplicativo baseado em REST](service-fabric-rest-based-application-lifecycle-sample.md)

<!---HONumber=AcomDC_0831_2016-->