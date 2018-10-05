---
title: Arquitetura de Conectividade de Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo fornece a visão geral da comunicação de Instância Gerenciada do Banco de Dados SQL do Azure e explica a arquitetura de conectividade, além de como os diferentes componentes funcionam para direcionar o tráfego para a Instância Gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 08/16/2018
ms.openlocfilehash: 312425d3ea02d15a992b9a694f09cb2be73b6221
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161583"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Arquitetura de Conectividade de Instância Gerenciada do Banco de Dados SQL do Azure 

Este artigo fornece a visão geral da comunicação de Instância Gerenciada do Banco de Dados SQL do Azure e explica a arquitetura de conectividade, além de como os diferentes componentes funcionam para direcionar o tráfego para a Instância Gerenciada.  

## <a name="communication-overview"></a>Visão geral da comunicação 

O diagrama a seguir mostra entidades que se conectam à Instância Gerenciada, bem como recursos que a Instância Gerenciada precisa atingir para funcionar adequadamente. 

![entidades de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

A comunicação descrita na parte inferior do diagrama representa os aplicativos e as ferramentas do cliente que se conectam à Instância Gerenciada como fonte de dados.  

Como a Instância Gerenciada é uma oferta de plataforma como um serviço (PaaS), a Microsoft gerencia esse serviço usando agentes automatizados (gerenciamento, implementação e manutenção) com base em fluxos de dados de telemetria. Como o gerenciamento de instâncias gerenciadas é de responsabilidade exclusiva da Microsoft, os clientes não podem acessar as máquinas de cluster virtual da instância gerenciada por meio do RDP. 

Algumas operações do SQL Server iniciadas pelos usuários finais ou aplicativos podem exigir que a Instância Gerenciada interaja com a plataforma. Um caso é a criação de um banco de dados de Instância Gerenciada - um recurso que é exposto por meio do portal, PowerShell e CLI do Azure. 

A Instância Gerenciada depende de outros Serviços do Azure para seu funcionamento adequado (como o Armazenamento do Azure para backups, o Barramento de Serviços do Azure para telemetria, o Azure AD para autenticação, o Cofre de Chaves do Azure para TDE e assim por diante) e inicia as conexões de acordo. 

Todas as comunicações, mencionadas acima, são criptografadas e assinadas usando certificados. Para garantir que as partes em comunicação sejam confiáveis, a Instância Gerenciada verifica constantemente esses certificados entrando em contato com a Autoridade de Certificação. Se os certificados forem revogados ou a Instância Gerenciada não puder verificá-los, ele fechará as conexões para proteger os dados. 

## <a name="high-level-connectivity-architecture"></a>Arquitetura de alto nível de conectividade 

Em um nível alto, a Instância Gerenciada é um conjunto de componentes de serviço, hospedado em um conjunto dedicado de máquinas virtuais isoladas que são executadas dentro da sub-rede da rede virtual do cliente e formam um cluster virtual. 

Várias instâncias gerenciadas podem ser hospedadas em um único cluster virtual. O cluster é expandido ou contratado automaticamente, se necessário, quando o cliente altera o número de instâncias aprovisionadas na sub-rede. 

Os aplicativos do cliente podem se conectar à Instância Gerenciada, consultar e atualizar bancos de dados somente se forem executados dentro da rede virtual ou em rede virtual emparelhada ou rede conectada por VPN / Express Route usando endpoint com endereço IP privado.  

![diagrama de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Os serviços de gerenciamento e implantação da Microsoft são executados fora da rede virtual, para que a conexão entre a Instância Gerenciada e os serviços da Microsoft ultrapasse os terminais com endereços IP públicos. Quando a Instância Gerenciada cria uma conexão de saída, ao receber o destino, parece que ela está vindo desse IP público devido à NAT (Tradução de Endereço de Rede). 

O tráfego de gerenciamento flui através da rede virtual do cliente. Isso significa que os elementos da infraestrutura de rede virtual afetam e podem prejudicar o tráfego de gerenciamento, fazendo com que a instância entre no estado defeituoso e se torne indisponível. 

> [!IMPORTANT]
> Para melhorar a experiência do cliente e a disponibilidade do serviço, a Microsoft aplica a Diretiva de Intenção de Rede nos elementos da infraestrutura de rede virtual do Azure que podem afetar o funcionamento da Instância Gerenciada. Este é um mecanismo de plataforma para comunicar de forma transparente os requisitos de rede aos usuários finais, com o objetivo principal de impedir a configuração incorreta da rede e garantir as operações normais da Instância Gerenciada. Na exclusão da instância gerenciada, a política de intenção de rede também é removida. 

## <a name="virtual-cluster-connectivity-architecture"></a>Arquitetura de conectividade do cluster virtual 

Vamos nos aprofundar na arquitetura de conectividade de instâncias gerenciadas. O diagrama a seguir mostra o layout conceitual do cluster virtual. 

![cluster virtual do diagrama de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Os clientes se conectam à Instância Gerenciada usando o nome do host que possui um formulário <mi_name>. <clusterid>.database.windows.net. Esse nome de host é resolvido para o endereço IP privado, embora esteja registrado na zona DNS pública e seja publicamente solucionável. 

Esse endereço IP privado pertence ao ILB (Managed Internal Load Balancer) da instância gerenciada que direciona o tráfego para o GW (Gateway de Instância Gerenciada). Como várias instâncias gerenciadas podem ser executadas no mesmo cluster, o GW usa o nome do host da instância gerenciada para redirecionar o tráfego para o serviço do SQL Engine correto. 

Os serviços de gerenciamento e implantação se conectam à Instância Gerenciada usando o endpoint público que mapeia para o balanceador de carga externo. O tráfego é roteado para os nós apenas se recebido em um conjunto predefinido de portas usadas exclusivamente pelos componentes de gerenciamento da instância gerenciada. Toda a comunicação entre componentes de gerenciamento e plano de gerenciamento é mutuamente autenticado do certificado. 

## <a name="next-steps"></a>Próximas etapas 

- Para uma visão geral, consulte  [O que é uma instância gerenciada](sql-database-managed-instance.md) 
- Para obter mais informações sobre a configuração de rede virtual, consulte [configuração de rede virtual de instância gerenciada](sql-database-managed-instance-vnet-configuration.md). 
- Para um início rápido, consulte como criar a instância gerenciada: 
  - No [portal do Azure](sql-database-managed-instance-get-started.md) 
  - Usando o [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) 
  - usando [modelo do Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/) 
  - usando [modelo do Resource Manager (jumpbox com o SSMS incluído)](https://portal.azure.com/) 

