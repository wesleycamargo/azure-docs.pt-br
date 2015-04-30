<properties 
   pageTitle="Rede híbrida (padrões de arquitetura do Azure)" 
   description="O padrão Rede Híbrida faz parte da área de Infraestrutura, que é descrita extensivamente no documento de Arquitetura da CPIF." 
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

# Rede híbrida (padrões de arquitetura do Azure)

A [Estrutura de integração de plataforma em nuvem (CPIF)](azure-architectures-cpif-overview.md) fornece diretrizes de integração de carga de trabalho para integração de aplicativos em uma solução de nuvem da Microsoft.  

A CPIF descreve como as organizações, os clientes e os parceiros devem projetar e implantar cargas de trabalho voltadas para a nuvem utilizando os recursos de plataforma e gerenciamento da nuvem híbrida do Azure, System Center e Windows Server. 

O padrão **Rede Híbrida** faz parte da área de **Infraestrutura**, que é descrita extensivamente no documento de Arquitetura da CPIF. 

## Rede Híbrida

O padrão de design da Rede Híbrida detalha os recursos e serviços do Azure necessários para fornecer funcionalidade de rede que possa fornecer alta disponibilidade e desempenho previsível entre fronteiras geográficas.  Uma lista completa das regiões do Microsoft Azure e dos serviços disponíveis em cada uma é fornecida no site de documentação do Microsoft Azure.  Este documento apresenta uma visão geral dos recursos de rede do Microsoft Azure para ambientes híbridos. A Rede Virtual do Microsoft Azure permite criar redes isoladas logicamente no Azure e conectá-las com segurança ao datacenter local pela Internet ou por meio de uma conexão de rede privada.  Além disso, computadores cliente individuais podem se conectar a uma rede isolada do Azure usando uma conexão VPN IPsec.  

O padrão de arquitetura de rede híbrida inclui as seguintes áreas de foco: 

- Conectando redes locais ao Azure 
- Estendendo redes virtuais do Azure entre regiões 
- Estendendo as redes virtuais do Azure entre assinaturas 
- Fornecendo aos desenvolvedores acesso remoto à rede 

## Visão geral do padrão de arquitetura 

O padrão de arquitetura de rede híbrida é complexo devido ao possível número de cenários que podem ser criados. Esse padrão de arquitetura focará nos quatro cenários a seguir: 

- Rede híbrida site a site com o roteamento de rede virtual com salto múltiplos dentro de uma única assinatura e região 
- Rede híbrida site a site com o roteamento de rede virtual com saltos múltiplos entre assinaturas e regiões de rede 
- Redes híbridas ExpressRoute usando conectividade MPLS 
- Redes híbridas ExpressRoute usando conectividade IXP 

## Recursos adicionais
[Rede Híbrida (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

## Consulte também
[Arquitetura da CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Camada da Web com Balanceamento de Carga Global](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[Camada de Dados com Balanceamento de Carga](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Camada de Pesquisa do Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

[Camada de Processamento em Lote](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!--HONumber=52-->