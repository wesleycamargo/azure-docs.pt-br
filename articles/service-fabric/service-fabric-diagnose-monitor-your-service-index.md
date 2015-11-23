<properties
   pageTitle="Diagnosticar e solucionar problemas de um serviço da Malha do Serviço"
   description="Informações conceituais e tutoriais que ajudarão você com o diagnóstico, o monitoramento e a solução de problemas de um serviço da Malha do Serviço."
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
   ms.date="09/25/2015"
   ms.author="ryanwi"/>

# Diagnosticar e monitorar um serviço da Malha do Serviço
Monitoramento, detecção, diagnóstico e solução de problemas permitem dar continuidade aos serviços com mínima interrupção da experiência do usuário. Para saber mais, leia:

- [Como monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Configurando o Application Insights para seu aplicativo da Malha do Serviço](../app-insights-windows-desktop.md)
- [Solucionando problemas de falhas de atualização de aplicativo ](service-fabric-application-upgrade-troubleshooting.md)
- [Diagnóstico e monitoramento de desempenho para Atores Confiáveis](service-fabric-reliable-actors-diagnostics.md)
- [Diagnóstico e monitoramento de desempenho para Serviços Confiáveis](service-fabric-reliable-services-diagnostics.md)

## Solucionar problemas de um cluster
As informações a seguir ajudarão você a solucionar problemas do cluster de desenvolvimento local:

- [Solucionar problemas de configuração do cluster de desenvolvimento local](service-fabric-troubleshoot-local-cluster-setup.md)

## Modelo de integridade
A Malha do Serviço apresenta um modelo de integridade que fornece uma funcionalidade de avaliação e relatório avançada, flexível e extensível para entidades da Malha do Serviço. Os componentes da Malha do Serviço apresentam relatórios de integridade prontos para uso sobre todas as entidades. Os serviços do usuário podem enriquecer os dados de integridade com informações específicas à respectiva lógica, relatórios sobre si mesmos ou outras entidades no cluster. Para saber mais, leia:

- [Introdução ao Monitoramento de Integridade da Malha do Serviço](service-fabric-health-introduction.md)
- [Como exibir relatórios de integridade da Malha do Serviço](service-fabric-view-entities-aggregated-health.md)
- [Usando relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Adicionando relatórios de integridade personalizados da Malha do Serviço](service-fabric-report-health.md)

<!---HONumber=Nov15_HO3-->