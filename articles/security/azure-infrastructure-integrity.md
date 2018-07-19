---
title: Integridade da infraestrutura do Azure
description: Este artigo aborda a integridade da infraestrutura do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 867bc66a68bec662153d8336e649cf46df02f101
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901309"
---
# <a name="azure-infrastructure-integrity"></a>Integridade da infraestrutura do Azure

## <a name="software-installation"></a>Instalação de software
Todos os componentes na pilha de software que estão instalados no ambiente do Azure são criados de forma personalizada seguindo o processo de SDL (Security Development Lifecycle) da Microsoft. Todos os componentes de software (incluindo as imagens do sistema operacional e o Banco de Dados SQL) são implantados como parte do processo de gerenciamento de alteração e de versão. O sistema operacional que é executado em todos os nós é uma versão personalizada do Windows Server 2008 ou do Windows Server 2012. A versão exata é escolhida pelo FC de acordo com a função que ele pretende que o sistema operacional desempenhe. Além disso, o sistema operacional host não permite a instalação de nenhum componente de software não autorizado.

Alguns componentes do Microsoft Azure (por exemplo, o RDFE, o Portal do Desenvolvedor, etc.) são implantados como clientes do Azure na VM convidada em execução no sistema operacional convidado.

## <a name="virus-scans-on-builds"></a>Verificações de vírus em builds
Os builds do componente de software do Azure (incluindo o sistema operacional) precisam passar por uma verificação de vírus usando a ferramenta antivírus MEP (Microsoft Endpoint Protection). Cada verificação de vírus cria um log no diretório de build associado, detalhando o que foi examinado e os resultados da varredura. A verificação de vírus faz parte do código-fonte do build para todos os componentes do Azure. O código não será movido para a produção sem passar por uma verificação de vírus limpa e bem-sucedida. Se houver algum problema observado, o build será congelado e, em seguida, passará para as equipes de segurança da Microsoft Security para identificar onde o código "invasor" entrou no build.

## <a name="closedlocked-environment"></a>Ambiente bloqueado/fechado
Por padrão, os nós de infraestrutura do Azure e as VMs convidadas não têm contas de usuário criadas neles. Além disso, as contas de administrador do Windows padrão também são desabilitadas. Os administradores do WALS (Microsoft Azure Live Support) podem, com a autenticação adequada, fazer logon nesses computadores e administrar a rede de produção do Azure para reparos de emergências.

## <a name="microsoft-azure-sql-database-authentication"></a>Autenticação do Banco de Dados SQL do Microsoft Azure
Assim como acontece com qualquer implementação do SQL Server, o gerenciamento de conta de usuário precisa ser controlado rigorosamente. O Banco de Dados SQL do Microsoft Azure dá suporte apenas para autenticação do SQL Server. Contas de usuário com senhas fortes e configuradas com direitos específicos também devem ser usadas para complementar o modelo de segurança de dados do cliente.

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>Firewalls/ACLs entre o MSFT CorpNet e o Cluster do Microsoft Azure
ACLs e firewalls entre a plataforma de serviço e a rede corporativa da MS protegem o Banco de Dados SQL do Microsoft Azure contra acesso de invasores não autorizado. Além disso, apenas os usuários de intervalos de endereços IP do Microsoft CorpNet podem acessar o ponto de extremidade de gerenciamento da plataforma WinFabric.

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Firewalls/ACLs entre nós em um cluster de BD SQL do Azure
Como uma proteção adicional, como parte da estratégia de defesa profunda, ACLs e firewalls foram implementados entre os nós em um cluster de BD SQL do Microsoft Azure. Toda a comunicação dentro do cluster da plataforma WinFabric, bem como todo o código em execução é confiável.

## <a name="custom-mas-watchdogs"></a>MAs personalizados (watchdogs)
O Banco de Dados SQL do Microsoft Azure emprega MAs personalizados chamados watchdogs para monitorar a integridade do cluster do BD SQL do Microsoft Azure.

## <a name="web-protocols"></a>Protocolos da Web

### <a name="role-instance-monitoring-and-restart"></a>Monitoramento e reinicialização de instância de função
O Azure garante que todas as funções em execução (funções Web da Internet ou de trabalho de processamento de back-end) implantadas estejam sujeitas ao monitoramento de integridade contínuo para assegurar que eles entreguem de forma efetiva e eficiente os serviços em que foram provisionados. Caso uma função se torne não íntegra, por uma falha crítica no aplicativo que está sendo hospedado ou por um problema na configuração subjacente na própria instância de função, o Microsoft Azure FC detectará o problema dentro da instância de função e iniciará um estado corretivo.

### <a name="compute-connectivity"></a>Conectividade de computação
O Azure garante que o aplicativo/serviço implantado esteja acessível por meio de protocolos padrão baseados na Web. As instâncias virtual da função web da Internet terão conectividade externa com a Internet e estarão acessíveis diretamente por usuários da Web. As instâncias virtuais de função de trabalho de processamento de back-end têm conectividade com a Internet externa, mas não podem ser acessadas diretamente por um usuário da Web externo, para proteger a confidencialidade e a integridade das operações que as funções de trabalho executam em nome das instâncias virtuais de função web acessíveis publicamente.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Limites e componentes do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Microsoft Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento da infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Proteção de dados do cliente no Azure](azure-protection-of-customer-data.md)
