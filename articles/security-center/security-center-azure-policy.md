---
title: "Integração de políticas de segurança da Central de Segurança do Azure com o Azure Policy | Microsoft Docs"
description: "Este documento ajuda você a configurar a integração de políticas de segurança da Central de Segurança do Azure com o Azure Policy."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 045cf83caa15cb2487b4781f3c1f42752880580c
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>Integrar políticas de segurança da Central de Segurança com o Azure Policy
Este artigo ajuda você a configurar as políticas de segurança da Central de Segurança do Azure, fornecidas pelo Azure Policy. 

## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança
A Central de Segurança cria automaticamente uma política de segurança padrão para cada uma de suas assinaturas do Azure. Você pode editar as políticas na Central de Segurança ou usar o [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) para fazer o seguinte:
* Criar definições de políticas.
* Atribuir as políticas entre grupos de gerenciamento, que podem representar uma organização inteira ou uma unidade de negócios dentro da organização.
* Monitorar a conformidade da política.

> [!NOTE]
> A Política do Azure está em versão prévia limitada. Para se inscrever, vá até [Criar uma conta no Azure Policy](https://aka.ms/getpolicy). Para obter mais informações sobre o Azure Policy, confira [Criar e gerenciar políticas para impor a conformidade](http://docs.microsoft.com/azure/azure-policy/create-manage-policy).

## <a name="edit-security-policies"></a>Editar políticas de segurança
Você pode editar a política de segurança padrão para cada uma de suas assinaturas do Azure na Central de Segurança. Para modificar uma política de segurança, você deve ser proprietário, colaborador ou administrador de segurança da assinatura ou do grupo de gerenciamento que a contém. Para exibir suas políticas de segurança na Central de Segurança, faça o seguinte:

1. Entre no Portal do Azure.

2. No painel **Central de Segurança**, em **Geral**, selecione **Política de segurança**.

    ![O painel Gerenciamento de Política](./media/security-center-azure-policy/security-center-policies-fig10.png)

3. Selecione a assinatura para a qual deseja habilitar uma política de segurança.  

4. Na seção **Componentes da Política**, selecione **Política de segurança**.  
    A janela **Noções básicas** é aberta.

    ![Componentes da política](./media/security-center-azure-policy/security-center-policies-fig12.png)

5. Para excluir uma definição de política, em **Políticas e parâmetros**, ao lado da definição que deseja excluir, selecione **Excluir**.

6. Clique em **Salvar**.  
    A janela **Definições Disponíveis** é aberta, exibindo a política padrão que é atribuída à Central de Segurança via Azure Policy. 

7. (Opcional) Na janela **Definições Disponíveis**, realize uma das seguintes ações:

    * Para adicionar uma definição de política, selecione o sinal de adição (+) ao lado da definição.

    ![Definições de políticas disponíveis](./media/security-center-azure-policy/security-center-policies-fig11.png)

    * Para obter uma explicação detalhada de uma política, selecione-a.  
    Uma janela com a **Versão prévia** da definição é aberta. Ela exibe uma descrição da definição e um link para o código JSON que fornece a estrutura da [definição de política](../azure-policy/policy-definition.md).

    ![A janela da Versão prévia da definição](./media/security-center-azure-policy/security-center-policies-fig14.png)

7. Ao concluir a edição, selecione **Salvar**.

## <a name="available-security-policy-definitions"></a>Definições de política de segurança disponíveis

Para entender as definições de política disponíveis na política de segurança padrão, confira a seguinte tabela: 

| Política | O que a política habilitada faz |
| --- | --- |
| Atualizações do sistema |Recupera uma lista diária das atualizações de segurança e críticas do Windows Update ou dos Serviços de Atualização do Windows Server. A lista recuperada depende do serviço configurado para suas máquinas virtuais e recomenda que as atualizações que faltam sejam aplicadas. Para os sistemas Linux, a política usa o sistema de gerenciamento de pacotes fornecido pela distribuição para determinar os pacotes com atualizações disponíveis. Também verifica as atualizações de segurança e críticas das máquinas virtuais dos [Serviços de Nuvem do Azure](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Vulnerabilidades do SO |Analisa as configurações do sistema operacional diariamente para determinar os problemas que podem tornar a máquina virtual vulnerável a ataques. A política também recomenda alterações de configuração para tratar essas vulnerabilidades. Para obter mais informações sobre as configurações específicas que estão sendo monitoradas, confira a [lista de linhas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (No momento, não há suporte completo para o Windows Server 2016.) |
| Proteção do ponto de extremidade |Recomenda que seja configurada uma proteção do ponto de extremidade para todas as máquinas virtuais (VMs) do Windows para ajudar a identificar e remover vírus, spyware e outros software mal-intencionados. |
| Criptografia do disco |Recomenda-se habilitar a criptografia de disco em todas as máquinas virtuais para aprimorar a proteção de dados em repouso. |
| Grupos de segurança de rede |Recomenda que os [grupos de segurança da rede](../virtual-network/virtual-networks-nsg.md) sejam configurados para controlar os tráfegos de entrada e saída para as VMs com pontos de extremidade públicos. Os grupos de segurança da rede configurados para uma sub-rede são herdados por todas as interfaces de rede da máquina virtual, a menos que seja especificado o contrário. Além de verificar se um grupo de segurança de rede foi configurado, essa política avalia as regras de segurança de entrada para identificar as regras que permitem o tráfego de entrada. |
| Firewall do aplicativo Web |Recomenda que um firewall do aplicativo Web seja configurado nas máquinas virtuais quando uma das seguintes opções é verdadeira: <ul><li>Um [IP público em nível de instância](../virtual-network/virtual-networks-instance-level-public-ip.md) é usado, e as regras de segurança de entrada para o grupo de segurança de rede associado são configuradas para permitir o acesso à porta 80/443.</li><li>Um IP de balanceamento de carga é usado, e o balanceamento de carga associado e as regras NAT (conversão do endereço de rede de entrada) são configurados para permitir o acesso à porta 80/443. Para saber mais, confira [Suporte do Azure Resource Manager para Balanceador de Carga](../load-balancer/load-balancer-arm.md).</li> |
| Firewall da próxima geração |Estende as proteções da rede para além dos grupos de segurança da rede, que são internos no Azure. A Central de Segurança descobre as implantações para as quais um firewall da próxima geração é recomendado, então você pode configurar uma solução de virtualização. |
| Auditoria do SQL e detecção de ameaças |Recomenda que a auditoria de acesso ao Banco de Dados do Azure esteja habilitada para conformidade e para detecção de ameaça avançada para fins de investigação. |
| Criptografia do SQL |Recomenda que a criptografia em repouso seja habilitada para o Banco de dados do SQL do Azure, backups associados e arquivos do log de transações. Mesmo que seus dados sejam violados, eles não podem ser lidos. |
| Avaliação de vulnerabilidade |Recomenda que você instale uma solução de avaliação de vulnerabilidade na VM. |
| Criptografia do armazenamento |Atualmente, esse recurso está disponível para o armazenamento do Azure Blobs e para Arquivos do Azure. Depois de habilitar a Criptografia do Serviço de Armazenamento, apenas dados novos são criptografados, e quaisquer arquivos existentes nessa conta de armazenamento permanecem não criptografados. |
| Acesso à rede JIT |Quando a rede Just-In-Time está habilitada, a Central de Segurança bloqueia o tráfego de entrada às suas VMs do Azure criando uma regra de grupo de segurança de rede. Selecione as portas na VM para as quais o tráfego de entrada deverá ser bloqueado. Para saber mais, confira [Gerenciar o acesso à máquina virtual usando o just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como configurar políticas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Guia de operações e planejamento da Central de Segurança do Azure](security-center-planning-and-operations-guide.md): saiba como planejar e entender as considerações de design sobre a Central de Segurança do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciar e responder aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerenciar e responder aos alertas de segurança.
* [Monitorar as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as respostas para as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): encontre postagens no blog sobre conformidade e segurança do Azure.
