---
title: "Definir políticas de segurança na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda você a configurar políticas de segurança na Central de Segurança do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: 4d1a30b046c0c398d934291a907af891e9ac7fdf
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2018
---
# <a name="set-security-policies-in-azure-security-center"></a>Configurar políticas de segurança na Central de Segurança do Azure
Este artigo ajuda você a configurar políticas de segurança na Central de Segurança.

## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança
A Central de Segurança cria automaticamente uma política de segurança padrão para cada uma de suas assinaturas do Azure. Você pode editar a política e monitorar a conformidade de política na Central de Segurança.

> [!NOTE]
> Agora você pode estender as políticas da Central de Segurança usando o Azure Policy, que está em versão prévia limitada. Para se inscrever na versão prévia, vá até [Criar uma conta no Azure Policy](https://aka.ms/getpolicy). Para obter mais informações, confira [Integrar políticas de segurança da Central de Segurança com o Azure Policy](security-center-azure-policy.md).

Os requisitos de segurança para os recursos usados para desenvolvimento ou teste podem ser diferentes daqueles usados para os aplicativos de produção. Os aplicativos que usam dados regulamentados, como as informações de identificação pessoal, podem requerer um nível mais alto de segurança. As políticas de segurança habilitadas na Central de Segurança do Azure determinam as recomendações de segurança e o monitoramento para ajudar a identificar as potenciais vulnerabilidades e atenuar as ameaças. Para obter mais informações sobre como determinar a opção adequada para você, consulte [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md).

## <a name="edit-security-policies"></a>Editar políticas de segurança
Você pode editar a política de segurança padrão para cada uma de suas assinaturas do Azure na Central de Segurança. Para modificar uma política de segurança, você deve ser um proprietário, colaborador ou administrador de segurança da assinatura. Para configurar suas políticas de segurança na Central de Segurança, faça o seguinte:

1. Entre no Portal do Azure.

2. No painel **Central de Segurança**, em **Geral**, selecione **Política de segurança**.

3. Selecione a assinatura para a qual deseja habilitar uma política de segurança.

4. Na seção **Componentes da Política**, selecione **Política de segurança**.  
    Esta é a política padrão atribuída pela Central de Segurança. Você pode ativar ou desativar as recomendações de segurança disponíveis.

5. Ao concluir a edição, selecione **Salvar**.

## <a name="available-security-policy-definitions"></a>Definições de política de segurança disponíveis

Para entender as definições de política disponíveis na política de segurança padrão, confira a seguinte tabela:

| Política | O que faz a política |
| --- | --- |
| Atualizações do sistema |Recupera uma lista diária das atualizações de segurança e críticas do Windows Update ou dos Serviços de Atualização do Windows Server. A lista recuperada depende do serviço configurado para suas máquinas virtuais e recomenda que as atualizações que faltam sejam aplicadas. Para os sistemas Linux, a política usa o sistema de gerenciamento de pacotes fornecido pela distribuição para determinar os pacotes com atualizações disponíveis. Também verifica as atualizações de segurança e críticas das máquinas virtuais dos [Serviços de Nuvem do Azure](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Configurações de segurança |Analisa as configurações do sistema operacional diariamente para determinar os problemas que podem tornar a máquina virtual vulnerável a ataques. A política também recomenda alterações de configuração para tratar essas vulnerabilidades. Para obter mais informações sobre as configurações específicas que estão sendo monitoradas, confira a [lista de linhas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (No momento, não há suporte completo para o Windows Server 2016.) |
| Proteção do ponto de extremidade |Recomenda que seja configurada uma proteção do ponto de extremidade para todas as máquinas virtuais (VMs) do Windows para ajudar a identificar e remover vírus, spyware e outros software mal-intencionados. |
| Criptografia do disco |Recomenda-se habilitar a criptografia de disco em todas as máquinas virtuais para aprimorar a proteção de dados em repouso. |
| Grupos de segurança de rede |Recomenda que os [grupos de segurança da rede](../virtual-network/virtual-networks-nsg.md) sejam configurados para controlar os tráfegos de entrada e saída para as VMs com pontos de extremidade públicos. Os grupos de segurança da rede configurados para uma sub-rede são herdados por todas as interfaces de rede da máquina virtual, a menos que seja especificado o contrário. Além de verificar se um grupo de segurança de rede foi configurado, essa política avalia as regras de segurança de entrada para identificar as regras que permitem o tráfego de entrada. |
| Firewall do aplicativo Web |Recomenda que um firewall do aplicativo Web seja configurado nas máquinas virtuais quando uma das seguintes opções é verdadeira: <ul><li>Um [IP público em nível de instância](../virtual-network/virtual-networks-instance-level-public-ip.md) é usado, e as regras de segurança de entrada para o grupo de segurança de rede associado são configuradas para permitir o acesso à porta 80/443.</li><li>Um IP de balanceamento de carga é usado, e o balanceamento de carga associado e as regras NAT (conversão do endereço de rede de entrada) são configurados para permitir o acesso à porta 80/443. Para saber mais, confira [Suporte do Azure Resource Manager para Balanceador de Carga](../load-balancer/load-balancer-arm.md).</li> |
| Firewall da próxima geração |Estende as proteções da rede para além dos grupos de segurança da rede, que são internos no Azure. A Central de Segurança descobre as implantações para as quais um firewall da próxima geração é recomendado, então você pode configurar uma solução de virtualização. |
| Auditoria do SQL e detecção de ameaças |Recomenda que a auditoria de acesso ao banco de dados SQL esteja habilitada para conformidade e para detecção de ameaça avançada para fins de investigação. |
| Criptografia do SQL |Recomenda que a criptografia em repouso seja habilitada para o seu banco de dados SQL, backups associados e arquivos do log de transações. Mesmo que seus dados sejam violados, eles não podem ser lidos. |
| Avaliação de vulnerabilidade |Recomenda que você instale uma solução de avaliação de vulnerabilidade na VM. |
| Criptografia do armazenamento |Atualmente, este recurso está disponível para blobs e Arquivos do Azure. Depois de habilitar a Criptografia do Serviço de Armazenamento, apenas dados novos são criptografados, e quaisquer arquivos existentes nessa conta de armazenamento permanecem não criptografados. |
| Acesso à rede JIT |Quando a rede Just-In-Time está habilitada, a Central de Segurança bloqueia o tráfego de entrada às suas VMs do Azure criando uma regra de grupo de segurança de rede. Selecione as portas na VM para as quais o tráfego de entrada deverá ser bloqueado. Para saber mais, confira [Gerenciar o acesso à máquina virtual usando o just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como configurar políticas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Guia de operações e planejamento da Central de Segurança do Azure](security-center-planning-and-operations-guide.md): saiba como planejar e entender as considerações de design sobre a Central de Segurança do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciar e responder aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerenciar e responder aos alertas de segurança.
* [Monitorar as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as respostas para as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): encontre postagens no blog sobre conformidade e segurança do Azure.
