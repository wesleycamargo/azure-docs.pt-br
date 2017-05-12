
# Visão geral
## [O que é o Service Fabric?](service-fabric-overview.md)
## [Entender os microsserviços](service-fabric-overview-microservices.md)
## [Cenários de aplicativos](service-fabric-application-scenarios.md)
## [Padrões e cenários](service-fabric-patterns-and-scenarios.md)
## [Arquitetura](service-fabric-architecture.md)
## [Terminologia](service-fabric-technical-overview.md)
## [Instruções elementares](service-fabric-content-roadmap.md)

# Introdução
## Configurar seu ambiente de desenvolvimento
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## Criar seu primeiro aplicativo
### [C# em Windows](service-fabric-create-your-first-application-in-visual-studio.md)
### [Java no Linux](service-fabric-create-your-first-linux-application-with-java.md)
### [C# em Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
## [Implantar aplicativos em um cluster local](service-fabric-get-started-with-a-local-cluster.md)
## [Criar seu primeiro cluster no Azure](service-fabric-get-started-azure-cluster.md)
## [Criar seu primeiro cluster autônomo](service-fabric-get-started-standalone-cluster.md)
## [Crie seu primeiro aplicativo de contêiner](service-fabric-get-started-containers.md)

# Como
## Compilar um aplicativo
  
### Conceitos
#### [Modelos de programação com suporte](service-fabric-choose-framework.md)
#### [Modelo de aplicativo](service-fabric-application-model.md)
#### [Recursos do manifesto do serviço](service-fabric-service-manifest-resources.md)
#### [Estado do serviço](service-fabric-concepts-state.md)
#### [Particionamento de serviço](service-fabric-concepts-partitioning.md)
#### [Disponibilidade de serviços](service-fabric-availability-services.md)
#### [Escalabilidade de aplicativos](service-fabric-concepts-scalability.md)
#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [Planejar a capacidade do aplicativo](service-fabric-capacity-planning.md)

### Compilar um serviço executável convidado
#### [Implantar um executável convidado](service-fabric-deploy-existing-app.md)
#### [Implantar vários executáveis de convidado](service-fabric-deploy-multiple-apps.md)

### Compilar um serviço de contêiner
#### [Visão geral](service-fabric-containers-overview.md)
#### [Implantar contêiner do Windows](service-fabric-deploy-container.md)
#### [Implantar contêiner do Linux](service-fabric-deploy-container-linux.md)

### Compilar um serviço de Reliable Service
#### [Visão geral](service-fabric-reliable-services-introduction.md)
#### Conceitos
##### [Ciclo de vida dos Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
##### [Ciclo de vida dos Reliable Services - Java](service-fabric-reliable-services-lifecycle-java.md)

#### Coleções Confiáveis
##### [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md)
##### [Recomendações e Diretrizes de coleções confiáveis](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [Trabalhando com Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [Transações e bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Gerenciador de estado confiável e Aspectos internos confiáveis da coleção](service-fabric-reliable-services-reliable-collections-internals.md)

#### Introdução
##### [C# em Windows](service-fabric-reliable-services-quick-start.md)
##### [Java no Linux](service-fabric-reliable-services-quick-start-java.md)

#### Ciclo de vida de Reliable Services
#### [Configurar](service-fabric-reliable-services-configuration.md)
#### [Enviar notificações](service-fabric-reliable-services-notifications.md)
#### [Backup e restauração](service-fabric-reliable-services-backup-restore.md)

#### Comunicar-se com os serviços
##### [Comunicar-se com o Reliable Services](service-fabric-reliable-services-communication.md)
##### [Comunicação remota do serviço - C#](service-fabric-reliable-services-communication-remoting.md)
##### [Comunicação remota do serviço - Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Comunicações seguras - C#](service-fabric-reliable-services-secure-communication.md)
##### [Comunicações seguras - Java](service-fabric-reliable-services-secure-communication-java.md)

#### [Uso avançado](service-fabric-reliable-services-advanced-usage.md)

### Compilar um serviço de Reliable Actor
#### [Visão geral](service-fabric-reliable-actors-introduction.md)
#### Conceitos
#### [Arquitetura](service-fabric-reliable-actors-platform.md)
#### [Ciclo de vida e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
#### [Gerenciamento de estado](service-fabric-reliable-actors-state-management.md)
#### [Polimorfismo](service-fabric-reliable-actors-polymorphism.md)
#### [Reentrada](service-fabric-reliable-actors-reentrancy.md)
#### [Serialização de tipo](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

#### Introdução
##### [C# em Windows](service-fabric-reliable-actors-get-started.md)
##### [Java no Linux](service-fabric-reliable-actors-get-started-java.md)

#### [Enviar notificações](service-fabric-reliable-actors-events.md) 
#### [Definir lembretes e temporizadores](service-fabric-reliable-actors-timers-reminders.md)
#### [Configurar KVSActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Definir as configurações da comunicação](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [Configurar ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### Comunicar-se com os serviços
#### [Comunicação de serviço](service-fabric-connect-and-communicate-with-services.md)
#### [Proxy reverso](service-fabric-reverseproxy.md)

### [Adicionar um front-end da web](service-fabric-add-a-web-frontend.md)

### Trabalhar em um IDE
#### [Introdução ao plug-in Eclipse para desenvolvimento de Java](service-fabric-get-started-eclipse.md)
#### [Gerenciar aplicativos no Visual Studio](service-fabric-manage-application-in-visual-studio.md)
#### [Configurar conexões seguras no Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
#### [Configurar seu aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)

### Configurar a segurança
#### [Gerenciar os segredos do aplicativo](service-fabric-application-secret-management.md)  
#### [Configurar políticas de segurança para seu aplicativo](service-fabric-application-runas-security.md)

### Depurar
#### [Depurar um serviço C# no VS](service-fabric-debugging-your-application.md)
#### [Depurar um serviço Java no Eclipse](service-fabric-debugging-your-application-java.md)
#### [Erros e exceções comuns](service-fabric-errors-and-exceptions.md)

### Monitorar e diagnosticar localmente
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

### Migrar dos Serviços de Nuvem
#### [Comparar os Serviços de Nuvem com o Service Fabric](service-fabric-cloud-services-migration-differences.md)
#### [Migrar para o Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
#### [Práticas recomendadas](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Gerenciar ciclo de vida do aplicativo
### [Visão geral](service-fabric-application-lifecycle.md)
### [Preparar um aplicativo](service-fabric-package-apps.md)
### [Entender a configuração ImageStoreConnectionString](service-fabric-image-store-connection-string.md)
### Implantar ou remover aplicativos
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [APIs de FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
### Atualizar um aplicativo
#### [Visão geral](service-fabric-application-upgrade.md)
#### [Configurar a atualização de aplicativo](service-fabric-visualstudio-configure-upgrade.md)
#### [Parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md)
#### Atualizar
##### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
##### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [Solucionar problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md)
#### [Serialização de dados nas atualizações de aplicativo](service-fabric-application-upgrade-data-serialization.md)
#### [Tópicos avançados de atualização de aplicativo](service-fabric-application-upgrade-advanced.md)

### Testar aplicativos e serviços
#### [Visão geral de análise de falhas](service-fabric-testability-overview.md)
#### [Testar comunicação serviço a serviço](service-fabric-testability-scenarios-service-communication.md)
#### Simular falhas
##### [Como usar o caos controlado](service-fabric-controlled-chaos.md)
##### [Como usar as ações de teste](service-fabric-testability-actions.md)
##### [Durante cargas de trabalho](service-fabric-testability-workload-tests.md)
##### [Como usar os cenários de teste](service-fabric-testability-scenarios.md)
##### [Usando as APIs de transição do nó](service-fabric-node-transition-apis.md)
#### [Fazer teste de carga em seu aplicativo](service-fabric-vso-load-test.md)

### Configurar a integração contínua
#### [Configurar a integração contínua com o VSTS](service-fabric-set-up-continuous-integration.md)
#### [Implantar seu aplicativo Java para Linux usando o Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

## Criar e gerenciar clusters

### [Visão geral](service-fabric-deploy-anywhere.md)
### Conceitos
#### [Descrever um cluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Segurança de cluster](service-fabric-cluster-security.md)
#### [Diferenças de recurso entre o Linux e o Windows](service-fabric-linux-windows-differences.md)

### Planejar e preparar
#### [Planejamento da capacidade](service-fabric-cluster-capacity.md)
#### [Recuperação de desastre](service-fabric-disaster-recovery.md)

### Clusters no Azure
#### Conceitos
##### [Tipos de nós e Conjuntos de Dimensionamento de VMs](service-fabric-cluster-nodetypes.md)
##### [Padrões de rede do cluster](service-fabric-patterns-networking.md)
#### Criação 
##### [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
##### [Gerenciador de Recursos do Azure](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio e Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### Escala 
##### [Manualmente](service-fabric-cluster-scale-up-down.md)
##### [Programaticamente](service-fabric-cluster-programmatic-scaling.md)
#### [Atualizar](service-fabric-cluster-upgrade.md)
#### [Definir controle de acesso](service-fabric-cluster-security-roles.md)
#### [Configurar](service-fabric-cluster-fabric-settings.md)
#### [Gerenciar certificados de cluster](service-fabric-cluster-security-update-certs-azure.md) 
#### [Excluir](service-fabric-cluster-delete.md)

### Clusters independentes
#### [Conteúdos do pacote autônomo](service-fabric-cluster-standalone-package-contents.md)
#### [Planejar e preparar para a implantação do cluster](service-fabric-cluster-standalone-deployment-preparation.md)
#### Criação
##### [Criar localmente](service-fabric-cluster-creation-for-windows-server.md)
##### [Criar em máquinas virtuais do Azure](service-fabric-cluster-creation-with-windows-azure-vms.md)
##### [Proteger usando certificados](service-fabric-windows-cluster-x509-security.md)  
##### [Proteger usando a segurança do Windows](service-fabric-windows-cluster-windows-security.md)
#### [Escala](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Definir controle de acesso](service-fabric-cluster-security-roles.md)
#### [Configurar](service-fabric-cluster-manifest.md)
#### [Atualizar](service-fabric-cluster-upgrade-windows-server.md) 

### [Visualizar um cluster](service-fabric-visualizing-your-cluster.md)
### [Conectar-se a um cluster seguro](service-fabric-connect-to-secure-cluster.md)

### [Gerenciar um cluster usando a CLI do Azure](service-fabric-azure-cli.md)

### Gerenciar e orquestrar recursos do cluster
#### [Visão geral do Resource Manager de cluster](service-fabric-cluster-resource-manager-introduction.md)
#### [Arquitetura do Gerenciador de Recursos do Cluster](service-fabric-cluster-resource-manager-architecture.md)
#### [Descrever um cluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Visão geral de grupos de aplicativos](service-fabric-cluster-resource-manager-application-groups.md)
#### [Definir configurações do Resource Manager de Cluster](service-fabric-cluster-resource-manager-configure-services.md)
#### [Métricas de consumo de recursos](service-fabric-cluster-resource-manager-metrics.md)
#### [Usar afinidade de serviço](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [Políticas de posicionamento de serviço](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [Gerenciar um cluster](service-fabric-cluster-resource-manager-management-integration.md)
#### [Desfragmentação do cluster](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [alancear um cluster](service-fabric-cluster-resource-manager-balancing.md)
#### [Limitação](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [Movimento de serviço](service-fabric-cluster-resource-manager-movement-cost.md)

### [Conectar-se a um cluster seguro](service-fabric-connect-to-secure-cluster.md)

## Inspecionar a integridade do aplicativo e do cluster
### [Monitorar a integridade do Service Fabric](service-fabric-health-introduction.md)
### [Relatar e verificar a integridade de serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Adicionar relatórios de integridade personalizados](service-fabric-report-health.md)
### [Solucionar problemas com relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Exibir relatórios de integridade](service-fabric-view-entities-aggregated-health.md)

## Monitorar e diagnosticar
### [Monitorar e diagnosticar aplicativos](service-fabric-diagnostics-overview.md)
### Monitorar e diagnosticar serviços localmente
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Logs de Diagnóstico do Azure
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Coletar logs de um processo de serviço](service-fabric-diagnostic-collect-logs-without-an-agent.md)
### [Diagnóstico em Reliable Services com estado](service-fabric-reliable-services-diagnostics.md)
### [Diagnósticos em Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
### [Solucionar problemas do seu cluster local](service-fabric-troubleshoot-local-cluster-setup.md)

# Referência
## [PowerShell](/powershell/azure/overview?view=azureservicefabricps)
## [API Java](/java/api/)
## [.NET](/dotnet/api/)
## [REST](/rest/api/servicefabric)

# Recursos
## [Perguntas comuns sobre o Service Fabric](service-fabric-common-questions.md)
## [Opções de suporte do Service Fabric](service-fabric-support.md)
## [Exemplo de código](http://aka.ms/servicefabricsamples)
## [Roteiro de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Preços](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=service-fabric)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
