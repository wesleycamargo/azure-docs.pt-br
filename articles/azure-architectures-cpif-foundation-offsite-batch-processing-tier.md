<properties 
   pageTitle="Camada de processamento de lote externo (padrões de arquitetura do Azure)" 
   description="O padrão da Camada de Processamento em Lotes Externo faz parte da área de infraestrutura, que é descrita extensivamente no documento de arquitetura da CPIF." 
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

# Camada de processamento de lote externo (padrões de arquitetura do Azure)

A [Estrutura de integração de plataforma em nuvem (CPIF)](azure-architectures-cpif-overview.md) fornece diretrizes de integração de carga de trabalho para integração de aplicativos em uma solução de nuvem da Microsoft.  

A CPIF descreve como as organizações, os clientes e os parceiros devem projetar e implantar cargas de trabalho voltadas para a nuvem utilizando os recursos de plataforma e gerenciamento da nuvem híbrida do Azure, System Center e Windows Server. 

O padrão da **Camada de Processamento em Lotes Externo** faz parte da área de **infraestrutura**, que é descrita extensivamente no documento de arquitetura da CPIF. 

## Camada de processamento em lote externo

A camada de processamento em lote externo detalha os recursos e os serviços do Azure necessários para fornecer processamento de dados de back-end que seja tanto tolerante a falha quanto dimensionável.  Esses serviços são realizados como funções de trabalho nos serviços de nuvem no Azure, que atualmente podem ser implantados em qualquer data center do Azure.   

Cargas de trabalho de processamento em lotes são únicas no sentido de que normalmente fornecem pouca ou nenhuma interface do usuário.  Um exemplo desse tipo de carga de trabalho local seria um serviço Windows em execução no Windows Server.  Ao considerar esse tipo de carga de trabalho em um ambiente de nuvem, seria um desperdício implantar um servidor inteiro para executar uma carga de trabalho, quando o que é realmente necessário é computação, armazenamento e conectividade de rede.  A função de trabalho é a implementação disso no Azure. 

Por definição, um trabalho de processamento em lote executado no Azure é uma carga de trabalho que se conecta a um recurso, fornece alguma lógica de negócios (computação) e alguma saída.  Os recursos de entrada e saída são definidos pelo usuário e podem variar de arquivos simples, blobs no armazenamento de blob do Azure, um banco de dados NoSQL ou bancos de dados relacionais.   

A lógica de negócios é implementada em uma função de trabalho do Azure, normalmente definindo a lógica de negócios necessária em uma biblioteca .NET.  Embora a implantação de uma função de trabalho para o Azure seja uma operação simples, a implantação de uma função de trabalho tolerante a falhas e dimensionável requer um design que leve em consideração como o serviço é executado e mantido no Azure.  Esse padrão de design detalhará o design que considera esses requisitos e descreverá como eles podem ser implementados. 

## Visão geral do padrão de arquitetura 

Este documento descreve um padrão de processamento em lote externo usando instâncias de função de trabalho contidas em um serviço de nuvem no Azure.  Os componentes essenciais para esse design são mostrados abaixo.  Este diagrama ilustra as instâncias mínimas necessárias para obter tolerância a falhas.  Instâncias adicionais podem ser implantadas para aumentar o desempenho do serviço.  Além disso, a colocação em escala automática pode ser habilitada para ajudar na colocação em escala de instâncias por tempo ou métricas de servidor adicionais. 

## Recursos adicionais
[Camada de Processamento em Lote (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

## Consulte também
[Arquitetura da CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Camada da Web com Balanceamento de Carga Global](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[Camada de Dados com Balanceamento de Carga](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Rede Híbrida](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Camada de Pesquisa do Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 



<!--HONumber=52-->