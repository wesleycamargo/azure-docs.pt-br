<properties 
   pageTitle="Estrutura de integração de plataforma de nuvem | Microsoft Azure" 
   description="A Estrutura de integração da plataforma de nuvem fornece diretrizes de integração de carga de trabalho para integração de aplicativos em uma solução de nuvem da Microsoft que consiste em padrões de arquitetura para o Microsoft Azure" 
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


# Estrutura de integração de plataforma de nuvem (padrões de arquitetura do Azure)

A Estrutura de integração de plataforma de nuvem (CPIF) fornece diretrizes de integração de carga de trabalho para integração de aplicativos em uma solução de nuvem da Microsoft.

A CPIF descreve como as organizações, os clientes e os parceiros devem projetar e implantar cargas de trabalho voltadas para a nuvem utilizando os recursos de plataforma e gerenciamento da nuvem híbrida do Azure, System Center e Windows Server. Os domínios CPIF foram divididos nas seguintes funções:

![Parte de marcas nas folhas de recurso e grupo de recursos](./media/azure-architecture-cpif-overview/overview.png)

##  Arquitetura da CPIF

Os ambientes de nuvem pública e privada fornecem elementos comuns para dar suporte à execução de cargas de trabalho complexas. Embora essas arquiteturas sejam relativamente bem compreendidas em ambientes físicos e virtualizados locais tradicionais, as construções encontradas dentro do Microsoft Azure requerem planejamento adicional para racionalizar os recursos de plataforma e infraestrutura encontrados em ambientes de nuvem pública. Para dar suporte ao desenvolvimento de um aplicativo hospedado ou serviço no Azure, é necessária uma série de padrões que destaque os diversos componentes necessários para compor uma determinada solução de carga de trabalho.

Esses padrões de arquitetura se enquadram nas seguintes categorias:

- **Infraestrutura**– o Microsoft Azure é uma solução de infraestrutura e de plataforma como serviço composta por vários serviços e recursos subjacentes. Esses serviços podem, em grande parte, ser divididos em serviços de computação, de armazenamento e de rede, no entanto, há vários recursos que podem ficar fora dessas definições. Padrões de infraestrutura detalham uma determinada área funcional do Microsoft Azure que é necessária para fornecer um serviço específico para uma ou mais soluções hospedadas dentro de uma determinada assinatura do Azure. 
- **Foundation** – ao criar um aplicativo ou serviço de várias camadas no Azure, vários componentes devem ser usados em conjunto para fornecer um ambiente de hospedagem adequado. Os padrões de base são compostos por um ou mais serviços do Microsoft Azure para dar suporte a uma determinada camada de funcionalidade dentro de um aplicativo. Isso pode exigir o uso de um ou mais componentes descritos nos padrões de infraestrutura destacados acima. Por exemplo, a camada de apresentação de um aplicativo de várias camada requer recursos de computação, rede e armazenamento no Azure para se tornar funcional. Padrões de base devem ser compostos com outros padrões como parte de uma determinada solução.
- **Solução** – padrões de solução são compostos por padrões de infraestrutura e/ou base para representar um aplicativo final ou serviço que está sendo desenvolvido. Prevê-se que soluções complexas não deve ser desenvolvidas independentemente de outros padrões. Em vez disso, eles devem utilizar os componentes e as interfaces definidos em cada uma das categorias de padrão descritas acima.    

## Conceitos de padrão de arquitetura do Azure

Para dar suporte ao desenvolvimento de arquiteturas de solução no Azure, uma série de guias padrão são fornecidos para cenários comuns. Apesar de que esses guias de cenário serão construídos ao longo do tempo, os padrões previstos incluem:

- Camada da Web com Balanceamento de Carga Global 
- Camada de Dados com Balanceamento de Carga
- Camada de Processamento em Lote
- Rede Híbrida
- Pesquisa do Azure 

##  Recursos adicionais
[Arquitetura de CPIF (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

## Consulte também
[Camada da Web com Balanceamento de Carga Global](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Camada de Dados com Balanceamento de Carga](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Camada de Processamento em Lote](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

[Rede do Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Pesquisa do Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=Oct15_HO4-->