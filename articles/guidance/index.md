---
title: "Documentação das Diretrizes do Azure - Tutoriais | Microsoft Docs"
description: "Práticas recomendadas e diretrizes do Azure"
services: guidance
author: carolz
manager: carolz
layout: LandingPage
ms.assetid: 
ms.service: guidance
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
translationtype: Human Translation
ms.sourcegitcommit: f5ced8f436f4d1426e4d2b307fcfb6b644e94aaf
ms.openlocfilehash: c05d311633b9c9aa5490e55baafe83376d0c8fbc

---
# <a name="azure-guidance"></a>Diretrizes do Azure
[!INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

A equipe de padrões e práticas da Microsoft faz parte da Equipe de Consulta do Cliente do Azure. Nosso objetivo é ajudar os desenvolvedores, arquitetos e profissionais de TI a terem êxito na plataforma Microsoft Azure. Desenvolvemos diretrizes que mostram as práticas recomendadas para compilar as soluções de nuvem no Azure.

## <a name="checklists"></a>Checklists
Essas listas são uma referência rápida para analisar os aspectos fundamentais de disponibilidade e escalabilidade. 

* [Lista de verificação de disponibilidade][AvailabilityChecklist] 
  
    Um resumo das práticas recomendadas para garantir a disponibilidade.
* [Lista de verificação de escalabilidade][ScalabilityChecklist]
  
    Um resumo das práticas recomendadas para projetar e implementar serviços escalonáveis e lidar com o gerenciamento de dados.

## <a name="best-practices-articles"></a>Artigos das práticas recomendadas
Esses artigos fornecem uma análise aprofundada dos conceitos importantes comumente associados à computação de nuvem. 

* [Design de API][APIDesign] 
  
    Uma análise dos problemas de design a considerar durante a criação de uma API da Web.
* [Implementação da API][APIImplementation] 
  
    Um conjunto de práticas recomendadas para implementar e publicar uma API da Web.
* [Diretrizes de segurança da API](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 
  
    Uma análise dos problemas de autenticação e autorização (por exemplo, tipos de token, protocolos de autorização, fluxos de autorização e atenuação de ameaças).
* [Diretrizes de dimensionamento automático][AutoscalingGuidance] 
  
    Um resumo das considerações sobre as soluções do dimensionamento sem uma intervenção manual.
* [Diretrizes de trabalhos em segundo plano][BackgroundJobsGuidance] 
  
    Uma descrição das opções disponíveis e práticas recomendadas para implementar tarefas que devem ser executadas em segundo plano, independentemente de qualquer operação interativa ou em primeiro plano.
* [Diretriz da Rede de Distribuição de Conteúdo (CDN)][CDNGuidance] 
  
    Diretrizes gerais e a práticas recomendadas para usar o CDN para reduzir a carga em seus aplicativos e maximizar a disponibilidade e o desempenho.
* [Diretrizes de cache][CachingGuidance] 
  
    Um resumo de como usar o cache para melhorar o desempenho e a escalabilidade de um sistema.
* [Diretrizes de particionamento de dados][DataPartitioningGuidance]
  
    Estratégias que podem ser usadas para particionar os dados para melhorar a escalabilidade, reduzir a contenção e otimizar o desempenho.
* [Diretrizes de monitoramento e diagnóstico][MonitoringandDiagnosticsGuidance] 
  
    Diretrizes sobre como os usuários utilizam o sistema, rastreiam a utilização de recursos e geralmente monitoram a integridade e o desempenho do sistema.
* [Convenções de nomenclatura recomendadas][naming-conventions] 
  
    Convenções de nomenclatura recomendadas para recursos do Azure.
* [Diretrizes gerais de repetição][RetryGeneralGuidance] 
  
    Uma análise sobre os conceitos gerais para lidar com falhas transitórias.
* [Diretriz específica do serviço de repetição][RetryServiceSpecificGuidance]
  
    Um resumo dos recursos de repetição para muitos serviços do Azure, incluindo informações para ajudar a usar, adaptar ou estender o mecanismo de repetição desse serviço.

## <a name="scenario-guides"></a>Guias do cenário
* [Execução do Elasticsearch no Azure][elasticsearch] 
  
    O Elasticsearch é um banco de dados e mecanismo de pesquisa de código-fonte aberto altamente escalonável. Ele é adequado para situações que exigem uma análise rápida e descoberta de informações mantidas em grandes conjuntos de dados. Essa diretriz aborda alguns aspectos importantes a considerar ao criar um cluster Elasticsearch.
* [Gerenciamento de identidades para aplicativos multilocatários][identity-multitenant] 
  
    A multilocação é uma arquitetura na qual vários locatários compartilham um aplicativo, mas são isolados uns dos outros. Essa diretriz mostra como gerenciar as identidades dos usuários em um aplicativo multilocatário, usando [Azure Active Directory][AzureAD] para lidar com a entrada e a autenticação.
* [Desenvolvendo soluções de Big Data](https://msdn.microsoft.com/library/dn749874.aspx)
  
    Este guia explora o uso do HDInsight para cenários como a exploração iterativo, por exemplo, um data warehouse, para processos de ETL e a integração com sistemas de BI existentes. Também inclui diretrizes sobre como entender os conceitos dos grandes volumes de dados, planejar e criar soluções de grandes volumes de dados, e implementar essas soluções.

## <a name="patterns"></a>Padrões
* [Padrões do Design de Nuvem: Diretrizes de Arquitetura Prescritiva para Aplicativos em Nuvem](https://msdn.microsoft.com/library/dn568099.aspx)
  
    Os Padrões do Design de Nuvem são uma biblioteca de padrões de design e tópicos de orientações relacionados. Explica o benefício de aplicar padrões, mostrando como cada parte pode encaixar-se nas arquiteturas dos aplicativos de nuvem.
* [Otimizando o Desempenho para Aplicativos em Nuvem](https://github.com/mspnp/performance-optimization)
  
    Essa diretriz é uma exploração de antipadrões comuns que impedem o dimensionamento sob carga dos aplicativos. Ele inclui exemplos que demonstram oito antipadrões, um [manual de análise do desempenho](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) e um guia para [avaliar o desempenho em relação às principais métricas](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Arquiteturas de referência
Nossas arquiteturas de referência são organizadas por cenário.
Cada arquitetura individual oferece práticas recomendadas, etapas prescritivas e um componente de executável que incorpora as recomendações.

A biblioteca atual das arquiteturas de referência está disponível em [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Diretrizes de resiliência
Estes tópicos descrevem como criar aplicativos que são resistentes a falhas em um ambiente de nuvem distribuído.   

* [Visão geral de resiliência][ResiliencyOvervew]
  
     Como compilar aplicativos na plataforma do Azure que podem recuperar-se de falhas e continuar a funcionar. Descreve uma abordagem estruturada para obter resiliência desde o design até a implementação, implantação e operações.
* [Lista de verificação de resiliência][resiliency-checklist]
  
    Uma lista de verificação de recomendações que ajudarão você a planejar vários modos de falha que podem ocorrer.
* [Análise do modo de falha][resiliency-fma] 
  
    A análise do modo de falha (FMA) é um processo de criação de resiliência em um sistema, identificando os possíveis pontos de falha. Como ponto de partida para seu processo FMA, este artigo contém um catálogo de modos de falha em potencial e suas atenuações. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md




<!--HONumber=Feb17_HO2-->


