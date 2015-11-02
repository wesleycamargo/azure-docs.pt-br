<properties 
   pageTitle="Camada da Web de balanceamento de carga global (padrões de arquitetura do Azure)" 
   description="O padrão Camada da Web com Balanceamento de Carga Global faz parte da área Foundation, que é descrita extensivamente no documento de arquitetura da CPIF." 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# Camada da Web de balanceamento de carga global (padrões de arquitetura do Azure)

A [Estrutura de integração de plataforma de nuvem (CPIF)](azure-architectures-cpif-overview.md) fornece diretrizes de integração de carga de trabalho para integração de aplicativos em uma solução de nuvem da Microsoft.

A CPIF descreve como as organizações, os clientes e os parceiros devem projetar e implantar cargas de trabalho voltadas para a nuvem utilizando os recursos de plataforma e gerenciamento da nuvem híbrida do Azure, System Center e Windows Server.

O **padrão Camada da Web com Balanceamento de Carga Global** faz parte da área **Foundation**, que é descrita extensivamente no documento de arquitetura da CPIF.

##  Camada da Web com Balanceamento de Carga Global

O padrão de design da Camada da Web com Balanceamento de Carga Global detalha os recursos e serviços do Azure necessários para oferecer serviços que possam fornecer alta disponibilidade e desempenho previsível entre fronteiras geográficas.

Para os fins desse padrão de design, uma camada da web é definida como uma camada de serviços que fornece conteúdo HTTP/HTTPS tradicional ou serviços de aplicativo de maneira isolada ou como parte de um aplicativo Web de várias camadas. Dentro desse padrão, a camada da web com balanceamento de carga é fornecida tanto localmente dentro da região quanto entre regiões. De uma perspectiva de computação, esses serviços podem ser fornecidos por meio das Máquinas Virtuais do Microsoft Azure, sites da Web ou uma combinação de ambos. Máquinas virtuais fornecendo serviços Web pode hospedar conteúdo usando qualquer sistema operacional convidado de distribuição Microsoft Windows ou Linux com suporte no Microsoft Azure.


## Visão geral do padrão de arquitetura 

Este documento descreve um padrão para fornecer acesso a serviços Web ou conteúdo do servidor Web por várias regiões geográficas para fins de redundância e disponibilidade. Serviços essenciais são ilustrados abaixo sem atenção às limitações da plataforma web ou metodologia de desenvolvimento dentro do próprio serviço web. Há duas variações para esse padrão – um que hospeda o conteúdo ou serviços da web em máquinas virtuais (usando os sistemas operacionais e famílias com suporte no Azure) e outra que usa sites do Azure. O diagrama a seguir é uma ilustração simples dos serviços relevantes e como eles são usados como parte desse padrão usando o exemplo de máquinas virtuais.

##  Recursos adicionais
[Camada da Web com Balanceamento de Carga Global (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

## Consulte também
[Arquitetura CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Camada de Dados com Balanceamento de Carga](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Rede Híbrida](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Camada de Pesquisa do Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[Camada de Processamento em Lote](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=Oct15_HO4-->