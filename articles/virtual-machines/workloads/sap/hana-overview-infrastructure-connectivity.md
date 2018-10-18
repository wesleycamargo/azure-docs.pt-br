---
title: Infraestrutura e conectividade para o SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Configure a infraestrutura de conectividade necessária para usar SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c61dffc6fd9d0c65f5e925daebdf2a02cfb5d6ba
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161349"
---
# <a name="sap-hana-large-instances-deployment"></a>Implantação do SAP HANA (instâncias grandes) 

Após a compra do SAP HANA no Azure (Instâncias Grandes) ser finalizada entre você e a equipe de conta empresarial da Microsoft, as seguintes informações são exigidas pela Microsoft para implantar Unidades de Instâncias Grandes HANA:

- Nome do cliente
- Informações de contatos comerciais (incluindo endereço de email e número de telefone)
- Informações de contatos técnicos (incluindo endereço de email e número de telefone)
- Informações de contatos da rede técnica (incluindo endereço de email e número de telefone)
- Região de implantação do Azure (Oeste dos EUA, Leste dos EUA, Leste da Austrália, Sudeste da Austrália, Europa Ocidental e Europa Setentrional a partir de julho de 2017)
- Confirmar SAP HANA no Azure (Instâncias Grandes) SKU (configuração)
- Como já detalhada no documento de visão geral e arquitetura do HANA instâncias grandes, para cada região do Azure que está sendo implantado:
    - Uma/29 intervalo de endereços IP para conexões de ER P2P que conectar redes virtuais do Azure para instâncias grandes HANA
    - Um bloco CIDR /24 usado para o Pool de IPs de Servidores de Instâncias Grandes HANA
- Os valores de intervalo de endereços IP usados no atributo do Espaço de Endereço da VNet de cada VNet do Azure que se conecta às Instâncias Grandes do HANA
- Dados para cada sistema de Instâncias Grandes HANA:
  - Nome de host desejado - o ideal é usar o nome de domínio totalmente qualificado.
  - Endereço IP desejado para a unidade de Instância Grande do HANA fora do intervalo de endereços do Pool IP do Servidor – tenha em mente que os 30 primeiros endereços IP no intervalo de endereços do Pool IP do Servidor são reservados para uso interno nas Instâncias Grandes do HANA
  - Nome do SID do SAP HANA da instância do SAP HANA (necessário para criar os volumes de disco relacionados ao SAP HANA necessários). O SID do HANA é necessário para criar as permissões para sidadm nos volumes NFS, que estão sendo anexados à unidade de Instância Grande do HANA. Ele também é usado como um dos componentes de nome dos volumes de disco que são montados. Se você desejar executar mais de uma instância do HANA na unidade, precisará listar vários SIDs do HANA. Cada uma obtém um conjunto separado de volumes atribuídos.
  - A groupid que o usuário sidadm tem no sistema operacional Linux é necessária para criar os volumes de disco relacionados ao SAP HANA. A instalação do SAP HANA normalmente cria o grupo de sapsys com uma id de grupo de 1001. O usuário sidadm faz parte do grupo
  - A userid que o usuário sidadm tem no sistema operacional Linux é necessária para criar os volumes de disco relacionados ao SAP HANA. Se você estiver executando várias instâncias do HANA na unidade, precisará listar todos os usuários <sid>adm 
- ID de assinatura do Azure da assinatura do Azure na qual as Instâncias Grandes do HANA do SAP HANA no Azure serão conectadas diretamente. Essa ID de assinatura referencia a assinatura do Azure, que será cobrada com as unidades da Instância Grande do HANA.

Depois de fornecer as informações, a Microsoft provisiona SAP HANA no Azure (instâncias grandes) e retornará as informações necessárias para vincular suas redes virtuais do Azure para instâncias grandes HANA e acessar as unidades de instância grande HANA.

Antes de ler este artigo, familiarize-se com os [termos comuns do HANA em Instâncias Grandes](hana-know-terms.md) e os [SKUs do HANA em Instâncias Grandes](hana-available-skus.md).

Você pode usar a sequência a seguir para se conectar ao HANA em Instâncias Grandes depois que ela for implantada pela Microsoft:

1. [Conectar VMs do Azure para Instâncias Grandes do HANA](hana-connect-azure-vm-large-instances.md)
2. [Conectar uma VNet ao ExpressRoute do HANA em Instâncias Grandes](hana-connect-vnet-express-route.md)
3. [Requisitos de rede adicionais (opcional)](hana-additional-network-requirements.md)

**Próximas etapas**

- Veja [Conectar VMs do Azure para HANA em Instâncias Grandes](hana-connect-azure-vm-large-instances.md).
- Veja [Conectar uma VNet ao ExpressRoute do HANA em Instância Grande](hana-connect-vnet-express-route.md).