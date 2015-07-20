<properties
   pageTitle="Ciclo de vida de aplicativo da Malha de Serviços do Azure | Microsoft Azure"
   description="Descreve o desenvolvimento, implantação, testes, atualização, manutenção e remoção de aplicativos da Malha de Serviços."
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
   ms.date="07/08/2015"
   ms.author="ryanwi; mani-ramaswamy"/>


# Ciclo de vida de aplicativos da Malha do Serviço
Semelhante a outras plataformas, um aplicativo na Malha do Serviço geralmente passa pelas seguintes fases: design, desenvolvimento, teste, implantação, atualização, manutenção e remoção. A Malha do Serviço dá suporte de primeira classe ao ciclo de vida completo dos aplicativos em nuvem: desenvolvimento, implantação, gerenciamento diário, manutenção e possível encerramento. O modelo de serviço permite que várias funções diferentes participem do ciclo de vida do aplicativo de forma independente. Este artigo fornece uma visão geral das APIs usadas pelas diferentes funções em todas as fases do ciclo de vida de um aplicativo da Malha do Serviço.

## Funções do modelo de serviço
As funções do modelo de serviço são:

- **Desenvolvedor de serviço**: desenvolve serviços modulares e genéricos que podem ser redefinidos e usados em vários aplicativos do mesmo tipo ou de tipos diferentes. Por exemplo, um serviço de fila pode ser usado para criar um aplicativo de emissão de tíquetes (assistência técnica) ou um aplicativo de comércio eletrônico (carrinho de compras).

- **Desenvolvedor de aplicativos**: cria aplicativos integrando uma coleção de serviços para atender a determinados requisitos ou cenários específicos. Por exemplo, um site de comércio eletrônico pode integrar “serviço front-end JSON sem monitoração de estado”, "Serviço de monitoração de estado de leilão" e "Serviço de monitoração de estado da fila" para criar uma solução de leilão.

- **Administrador do aplicativo**: toma decisões sobre a configuração do aplicativo (preenchendo os parâmetros de configuração de modelo), a implantação (mapeamento de recursos disponíveis) e a qualidade do serviço. Por exemplo, um administrador do aplicativo decide a localidade do idioma para o aplicativo (inglês para os EUA ou japonês para o Japão, por exemplo). Outro aplicativo implantado pode ter configurações diferentes.

- **Operador**: implanta aplicativos baseados na configuração do aplicativo e em requisitos especificados pelo administrador do aplicativo. Por exemplo, um operador provisiona, implanta o aplicativo e verifica se ele está em execução no Azure. Os operadores de monitoram as informações de integridade e de desempenho do aplicativo e mantêm a infra-estrutura física conforme necessário.


## Desenvolver
1. Um *desenvolvedor de serviço* desenvolve tipos diferentes de serviços usando o modelo de programação [Atores confiáveis](service-fabric-reliable-actors-introduction.md) ou [Serviços confiáveis](../Service-Fabric/service-fabric-reliable-services-introduction.md).
2. Um *desenvolvedor de serviço* descreve declarativamente os tipos de serviço desenvolvidos em um arquivo de manifesto do serviço que consiste em um ou mais pacotes de código, de configuração e de dados.
3. Um *desenvolvedor de aplicativos* cria um aplicativo usando diferentes tipos de serviço.
4. Um *desenvolvedor de aplicativos* descreve declarativamente o tipo de aplicativo em um manifesto de aplicativo referenciando os manifestos do serviço dos serviços membros e substituindo e parametrizando adequadamente as diferentes configurações de implantação e configurações dos serviços membros.

Confira [Introdução aos atores confiáveis](service-fabric-reliable-actors-get-started.md) e [Introdução aos serviços confiáveis](service-fabric-reliable-services-quick-start.md) para obter exemplos.

## Implantar
1. Um *administrador do aplicativo* personaliza o tipo de aplicativo para a função específica de implantação em um cluster da Malha do Serviço especificando os parâmetros apropriados do elemento **ApplicationType** no manifesto do aplicativo.

2. Um *operador* carrega o pacote de aplicativo para o cluster ImageStore usando o método [CopyApplicationPackage](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) ou o [cmdlet Copy-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt125905.aspx). O pacote de aplicativo contém o manifesto do aplicativo e a coleção de pacotes de serviço. A Malha do Serviço implanta aplicativos do pacote de aplicativo armazenado no ImageStore, que pode ser um armazenamento de blob do Azure ou o serviço do sistema da Malha do Serviço.

3. O *operador* configura o tipo de aplicativo no cluster de destino do pacote de aplicativo carregado usando o [método ProvisionApplicationAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), o [cmdlet Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125958.aspx) ou a [operação Create Application REST](https://msdn.microsoft.com/library/azure/dn707692.aspx).

3. Após a configuração do aplicativo, um *operador* o inicia com os parâmetros fornecidos pelo *administrador do aplicativo* usando o [método CreateApplicationAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), o [cmdlet New-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt125913.aspx) ou a [operação Create Application REST](https://msdn.microsoft.com/library/azure/dn707692.aspx).

4. Depois que o aplicativo foi implantado, uma *operador* usa o [método CreateServiceAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), o [cmdlet New-ServiceFabricService](https://msdn.microsoft.com/library/azure/mt125874.aspx) ou a [operação Create Application REST](https://msdn.microsoft.com/library/azure/dn707692.aspx) para criar novas instâncias de serviço para o aplicativo baseados nos tipos de serviço disponíveis.

5. O aplicativo está sendo executado no cluster da Malha do Serviço.

Confira [implantar um aplicativo](service-fabric-deploy-remove-applications.md) para obter exemplos.

## Teste
1. Após a implantação em um grupo de desenvolvimento local ou em um cluster de teste, um *desenvolvedor de serviço* executa o cenário de teste de failover interno usando as classes [FailoverTestScenarioParameters](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) e [FailoverTestScenario](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) ou o [cmdlet Invoke-ServiceFabricFailoverTestScenario](https://msdn.microsoft.com/library/azure/mt125935.aspx). O cenário de teste de failover executa um determinado serviço através de failovers e transições importantes para garantir que ainda estará disponível e funcionando.

2. O *desenvolvedor de serviço* executa o cenário de teste de caos interno usando as classes [ChaosTestScenarioParametersscenarioParameters](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) e [ChaosTestScenario](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) ou o [cmdlet Invoke-ServiceFabricChaosTestScenario](https://msdn.microsoft.com/library/azure/mt126036.aspx). O cenário de teste caos induz vários nós, pacote de código e falhas de réplica aleatoriamente no cluster.

Confira [cenários de teste](service-fabric-testability-scenarios.md) para obter exemplos.

## Atualizar
1. Um *desenvolvedor de serviço* atualiza os serviços membros do aplicativo instanciado e/ou corrige bugs e fornece uma nova versão do manifesto do serviço.

2. Um *desenvolvedor de aplicativos* substitui e parametriza as configurações de implantação e configuração dos serviços membros e fornece uma nova versão do manifesto do aplicativo. O desenvolvedor do aplicativo incorpora as novas versões dos manifestos do serviço no aplicativo e fornece uma nova versão do tipo de aplicativo em um pacote de aplicativo atualizado.

3. Um *administrador do aplicativo* incorpora a nova versão do tipo de aplicativo no aplicativo de destino atualizando os parâmetros apropriados.

4. Um *operador* carrega o pacote de aplicativo atualizado no cluster ImageStore usando o [método CopyApplicationPackage](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) ou o [cmdlet Copy-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt125905.aspx). O pacote de aplicativo contém o manifesto do aplicativo e a coleção de pacotes de serviço.

5. Um *operador* provisiona a nova versão do aplicativo no cluster de destino usando o [método ProvisionApplicationAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), o [cmdlet de registro ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125958.aspx) ou a [operação Provisionar um Aplicativo REST](https://msdn.microsoft.com/library/azure/dn707692.aspx).

6. Um *operador* atualiza o aplicativo de destino para a nova versão usando o [método UpgradeApplicationAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), o [cmdlet Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125975.aspx) ou a [operação Upgrade Application by Application Type REST](https://msdn.microsoft.com/library/azure/dn707692.aspx).

7. Um *operador* verifica o progresso de atualização usando o [método GetApplicationUpgradeProgressAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), o [cmdlet Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) ou a [operação Get Application Upgrade Progress REST](https://msdn.microsoft.com/library/azure/dn707692.aspx).

8. Se necessário, o *operador* modifica e aplica novamente os parâmetros da atualização do aplicativo atual usando o [método UpdateApplicationUpgradeAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), o [cmdlet Update-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt126030.aspx) ou a [operação Update Application Upgrade REST](https://msdn.microsoft.com/library/azure/dn707692.aspx).

9. Se necessário, o *operador* reverte a atualização do aplicativo atual usando o [método RollbackApplicationUpgradeAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), o [cmdlet Start-ServiceFabricApplicationRollback](https://msdn.microsoft.com/library/azure/mt125833.aspx) ou a [operação Rollback Application Upgrade REST](https://msdn.microsoft.com/library/azure/dn707692.aspx).

10. A Malha do Serviço atualiza o aplicativo de destino em execução no cluster sem perder a disponibilidade de qualquer um dos seus serviços membros.

Confira [Tutorial sobre a atualização de aplicativo](service-fabric-application-upgrade-tutorial.md) para obter exemplos.

## Manter
1. Para patches e atualizações do sistema operacional, a Malha do Serviço faz a interface com a infraestrutura do Azure para garantir a disponibilidade de todos os aplicativos executados no cluster.

2. Para atualizações e patches para a plataforma Malha do Serviço, a Malha do Serviço se atualiza automaticamente sem perder a disponibilidade de qualquer um dos aplicativos em execução no cluster.

3. Um *administrador do aplicativo* aprova a adição ou remoção de nós de um cluster depois de analisar os dados do histórico de utilização da capacidade e demanda futura projetada.

4. Um *operador* adiciona e remove nós especificados pelo *administrador do aplicativo*.

5. Quando novos nós são adicionados ou nós existentes são removidos do cluster, a Malha do Serviço balanceia a carga os aplicativos em execução em todos os nós do cluster para obter o desempenho ideal.

## Remove
1. Um *operador* pode excluir uma instância específica de um serviço em execução no cluster sem remover o aplicativo inteiro usando o [método DeleteServiceAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), o [cmdlet Remove-ServiceFabricService](https://msdn.microsoft.com/library/azure/mt126033.aspx) ou a [operação Delete Service REST](https://msdn.microsoft.com/library/azure/dn707692.aspx).  

2. Um *operador* também pode excluir uma instância de aplicativo e todos os seus serviços usando o [método DeleteApplicationAsync método](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), o [cmdlet Remove-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt125914.aspx) ou a [operação Delete Application REST](https://msdn.microsoft.com/library/azure/dn707692.aspx).

3. Depois que os aplicativos e serviços forem interrompidos, o *operador* pode desaprovisionar o tipo de aplicativo usando o [método UnprovisionApplicationAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), o [cmdlet Unregister-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125885.aspx) ou a [operação Desaprovisionar um aplicativo REST](https://msdn.microsoft.com/library/azure/dn707692.aspx). O desaprovisionamento do tipo de aplicativo não remove o pacote de aplicativo da ImageStore. Você deve remover o pacote de aplicativo manualmente.

4. Um *operador* remove o pacote de aplicativo usando da ImageStore usando o [método RemoveApplicationPackage ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) ou o [cmdlet Remove-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt163532.aspx).

Confira [implantar um aplicativo](service-fabric-deploy-remove-applications.md) para obter exemplos.

## Próximas etapas

Para saber mais sobre o desenvolvimento, o teste e o gerenciamento de aplicativos e serviços da Malha do Serviço, confira:

- [Desenvolver um serviço Malha do Serviço](service-fabric-develop-your-service-index.md)
- [Gerenciar um serviço Malha do Serviço](service-fabric-manage-your-service-index.md)
- [Testar um serviço Malha do Serviço](service-fabric-test-your-service-index.md)
- [Exemplo de ciclo de vida de um aplicativo baseado em REST](service-fabric-rest-based-application-lifecycle-sample.md)
 

<!---HONumber=July15_HO2-->