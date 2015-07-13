<properties 
   pageTitle="Camada de Pesquisa do Azure (padrões de arquitetura do Azure)" 
   description="O padrão da Camada de Pesquisa do Azure faz parte da área Foundation, que é descrita extensivamente no documento de arquitetura de CPIF" 
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

# Camada de Pesquisa do Azure (padrões de arquitetura do Azure)

A [Estrutura de integração de plataforma de nuvem (CPIF)](azure-architectures-cpif-overview.md) fornece diretrizes de integração de carga de trabalho para integração de aplicativos em uma solução de nuvem da Microsoft.

A CPIF descreve como as organizações, os clientes e os parceiros devem projetar e implantar cargas de trabalho voltadas para a nuvem utilizando os recursos de plataforma e gerenciamento da nuvem híbrida do Azure, System Center e Windows Server.

O padrão de **Camada de Pesquisa do Azure** faz parte da área **Foundation**, que é descrita extensivamente no documento de arquitetura de CPIF.

##  Camada de Pesquisa do Azure

O padrão de design da Camada de Pesquisa do Azure detalha os recursos e serviços do Azure necessários para fornecer serviços de pesquisa que podem oferecer alta disponibilidade e desempenho previsível entre fronteiras geográficas e fornece um padrão de arquitetura para usar a Pesquisa do Azure no fornecimento de uma solução de pesquisa. A Pesquisa do Azure é uma "pesquisa como um serviço" criada com o Microsoft Azure que permite aos desenvolvedores incorporar capacidades de pesquisa em aplicativos sem necessidade de implantar, gerenciar ou manter os serviços de infraestrutura para fornecer essa capacidade aos aplicativos. A finalidade desse padrão é fornecer uma solução repetível para uso em diferentes situações e design.

## Visão geral do padrão de arquitetura 

Este documento descreve um padrão central para usar a Pesquisa do Azure com duas variações do núcleo para demonstrar a variedade de arquiteturas do serviço. O padrão central consiste na Pesquisa do Azure e serviços do Azure relacionados e tem como objetivo fornecer diretrizes para a criação de designs de ponta a ponta. Variações do padrão, especificamente o serviço compartilhado e padrões de simultaneidade, também estão incluídas nesta seção para fornecer diretrizes com base em diferentes requisitos, contratos de nível de serviço (SLA) e outras condições específicas.

##  Recursos adicionais
[Camada de Pesquisa do Azure (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

## Consulte também
[Arquitetura CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Camada da Web com Balanceamento de Carga Global](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Camada de Dados com Balanceamento de Carga](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Rede Híbrida](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Camada de Processamento em Lote](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=July15_HO1-->