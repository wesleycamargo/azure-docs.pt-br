---
title: Gerenciamento e operações de produção do Azure
description: Este artigo fornece uma descrição geral do gerenciamento e a operação de rede de produção do Azure.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: dc389f5f5c155555deb860f041b15b0ea49ee416
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101238"
---
# <a name="azure-production-operations-and-management"></a>Gerenciamento e operações de produção do Azure    
Gerenciamento e operação da rede de produção do Azure são um esforço coordenado entre as equipes de operações do Azure e banco de dados SQL. Várias ferramentas de monitoramento de desempenho de sistemas e aplicativos são usadas no ambiente. Dispositivos de rede, servidores, serviços e processos de aplicativos são monitorados com ferramentas apropriadas.

Vários níveis de monitoramento, registro e geração de relatórios são implementados para garantir a execução segura dos serviços em execução no ambiente do Microsoft Azure, incluindo as seguintes ações:

- Primeiramente, o Agente de Monitoramento do Microsoft Azure (MA) reúne informações de log de monitoramento e diagnóstico de vários locais, incluindo o FC e o SO raiz, e os grava em arquivos de log. Eventualmente, ele envia um subconjunto digitado das informações para uma Conta de Armazenamento do Azure pré-configurada. Além disso, o Serviço de Monitoramento e Diagnóstico (MDS) é um serviço independente que lê vários dados de registro de monitoramento e diagnóstico e resume as informações. MDS grava as informações em um log integrado. O Azure usa o Monitoramento de Segurança do Azure (ASM) personalizado, que é uma extensão do sistema de monitoramento do Azure. Ele possui componentes que observam, analisam e relatam eventos pertinentes à segurança de vários pontos da plataforma.
- Banco de dados SQL do Microsoft Azure A plataforma WinFabric fornece serviços de gerenciamento, implantação, desenvolvimento e supervisão operacional para o Banco de Dados SQL do Microsoft Azure. Ele oferece serviços distribuídos de implantação em várias etapas, monitoramento de integridade, reparos automáticos e conformidade de versão de serviço. Ela fornece os seguintes recursos:

   - Serviço de recursos com o ambiente de desenvolvimento de alta fidelidade de modelagem (clusters do datacenter são escassos e caro).
   - Implantação de um clique e fluxos de trabalho de atualização para a inicialização de serviço e manutenção.
   - Relatório com fluxos de trabalho de reparo automatizado para habilitar a autorrecuperação de integridade.
   - Monitoramento, alertas e recursos de depuração em todos os nós de um sistema distribuído em tempo real.
   - Coleção centralizada de dados operacionais e métricas para raiz distribuído causar insight de análise e o serviço.
   - Ferramentas operacionais para implantação, gerenciamento de mudanças e monitoramento.
   - Banco de dados SQL do Microsoft Azure A plataforma WinFabric e os scripts de monitoramento do WinFabric são executados continuamente e monitorados em tempo real.

Se ocorrer alguma anomalia, o processo de Resposta a Incidentes seguido pela equipe de Triagem de Incidente do Azure será ativado. A equipe de suporte do Azure apropriada será notificado para responder ao incidente. Acompanhamento de questões e resolução são documentados e gerenciados em um sistema centralizado de emissão de tíquetes. As métricas de tempo de atividade do sistema estão disponíveis sob o contrato de confidencialidade (NDA) e mediante solicitação.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Rede corporativa e o acesso de vários fatores para a produção
A base de usuários da rede corporativa inclui pessoal de suporte do Microsoft Azure. A rede corporativa oferece suporte a funções corporativas internas e inclui acesso a aplicativos internos usados para suporte ao cliente do Azure. A rede corporativa é logicamente e fisicamente separada da rede de produção do Azure. O pessoal do Microsoft Azure acessa a rede corporativa usando estações de trabalho e laptops do Microsoft Azure. Todos os usuários devem ter uma conta do Active Directory (AD), incluindo um nome de usuário e senha para acessar recursos da rede corporativa. Acesso de rede corporativa usa contas do AD, que são emitidas para todos os funcionários da Microsoft, prestadores de serviços, fornecedores e gerenciadas pelo MSIT. Os identificadores exclusivos de usuários distinguem o pessoal com base em seu status de emprego na Microsoft.

O acesso a aplicativos internos do Azure é controlado por meio da autenticação com o ADFS (Serviços de Federação do Active Directory). O ADFS é um serviço hospedado pelo MSIT que fornece autenticação de usuários do CorpNet por meio da aplicação de um token seguro e de declarações do usuário. O ADFS permite que aplicativos internos do Microsoft Azure autenticar usuários em um domínio de AD corporativo da Microsoft. Para acessar a rede de produção no ambiente de rede corporativa, o usuário deve autenticar usando a autenticação multifator.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Microsoft Azure](azure-infrastructure-sql.md)
- [Monitoramento da infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Monitoramento da integridade do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente no Azure](azure-protection-of-customer-data.md)
