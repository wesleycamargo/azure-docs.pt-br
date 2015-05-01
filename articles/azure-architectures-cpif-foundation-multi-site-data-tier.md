<properties 
   pageTitle="Camada de dados de vários locais (padrões de arquitetura do Azure)" 
   description="O padrão de camada de dados de vários locais faz parte da área Foundation, que é descrita extensivamente no documento de arquitetura da CPIF." 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# Camada de dados de vários locais (padrões de arquitetura do Azure)

A [Estrutura de integração de plataforma em nuvem (CPIF)](azure-architectures-cpif-overview.md) fornece diretrizes de integração de carga de trabalho para integração de aplicativos em uma solução de nuvem da Microsoft. 

A CPIF descreve como as organizações, os clientes e os parceiros devem projetar e implantar cargas de trabalho voltadas para a nuvem utilizando os recursos de plataforma e gerenciamento da nuvem híbrida do Azure, System Center e Windows Server. 

O padrão de **camada de dados de vários locais** faz parte da área **Foundation**, que é descrita extensivamente no documento de arquitetura da CPIF. 

## Camada de dados de vários locais

O design da camada de dados de vários locais detalha recursos e serviços do Azure necessários para fornecer serviços de camada de dados que podem fornecer alta disponibilidade e desempenho previsível entre fronteiras geográficas. Para os fins desse padrão de design, uma camada de dados é definida como uma camada de serviço que fornece serviços de plataforma de dados tradicional de uma maneira isolada ou como parte de um aplicativo de várias camadas.  Dentro desse padrão, o balanceamento de carga da camada de dados é fornecido tanto localmente dentro da região quanto entre regiões.   

Os Grupos de Disponibilidade AlwaysOn introduzidos com o SQL Server 2012 são um recurso de alta disponibilidade e recuperação de desastres com suporte total nos serviços de infraestrutura do Azure.  Informações detalhadas e o comunicado oficial de suporte para os Grupos de Disponibilidade AlwaysOn na infraestrutura de serviço do Windows Azure podem ser encontrados no artigo Grupos de Disponibilidade AlwaysOn.   

Este documento apresenta uma visão geral da arquitetura de uma camada de dados de vários locais no Azure usando os Grupos de Disponibilidade AlwaysOn SQL. Com um nó secundário somente leitura opcional em um datacenter do Azure para funcionalidade recuperação de desastres adicionais. Usar o AlwaysOn SQL no Azure fornece uma camada de dados de alta disponibilidade que pode ser consumida pelas camadas da web ou do aplicativo.  

Embora este documento se concentre em práticas e padrões de arquitetura, diretrizes de implantação completas podem ser encontradas nos tutoriais oficiais, que descrevem a configuração de Grupos de Disponibilidade AlwaysOn no Azure e a configuração do ouvinte do Grupos de Disponibilidade AlwaysOn. 

## Visão geral do padrão de arquitetura 

Este documento descreve um padrão para fornecer acesso ao conteúdo do Microsoft SQL Server em várias regiões para fins de redundância e disponibilidade.  Serviços essenciais são ilustrados a seguir sem atenção para o aplicativo ou a camada da web que acessará os dados em si.  O diagrama a seguir é uma ilustração simples dos serviços relevantes e como eles são usados como parte desse padrão.   

Cada uma das áreas principais de serviço são descritas em mais detalhes no diagrama a seguir. 
 
![Marca parte nas folhas de recurso e grupo de recursos](./media/azure-architectures-cpif-foundation-multi-site-data-tier/overview.png)

## Recursos adicionais
[Camada de Dados com Balanceamento de Carga (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

## Consulte também
[Arquitetura da CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Camada da Web com Balanceamento de Carga Global](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[Rede Híbrida](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Camada de Pesquisa do Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

[Camada de Processamento em Lote](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)


<!--HONumber=52-->