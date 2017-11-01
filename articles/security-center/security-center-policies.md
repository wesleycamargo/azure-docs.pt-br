---
title: "Definir políticas de segurança na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda você a configurar políticas de segurança na Central de segurança do Azure."
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
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 1cebb6edecd13c6ab32c6854bfd6fe908c1f71f4
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2017
---
# <a name="set-security-policies-in-security-center"></a>Definir políticas de segurança na Central de Segurança
Este documento ajuda a configurar as políticas de segurança na Central de Segurança, guiando você nas etapas necessárias para executar essa tarefa. 


## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança
A Central de Segurança cria automaticamente uma política de segurança padrão para cada uma de suas assinaturas do Azure. Você pode editar a política na Central de Segurança e monitorar a conformidade de política. 

> [!NOTE]
> Agora você pode estender as políticas da Central de Segurança usando o Azure Policy, que está em versão prévia limitada. Clique [aqui](http://aka.ms/getpolicy) para ingressar na versão prévia ou confira a documentação [aqui](security-center-azure-policy.md).

Por exemplo, os recursos usados para o desenvolvimento ou teste podem ter requisitos de segurança diferentes daqueles usados para os aplicativos de produção. Da mesma forma, os aplicativos com dados regulamentados, como as informações de identificação pessoal, podem requerer um nível mais alto de segurança. As políticas de segurança habilitadas na Central de Segurança do Azure determinam as recomendações de segurança e o monitoramento para ajudar a identificar as potenciais vulnerabilidades e atenuar as ameaças. Leia o [Guia de Planejamento e Operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md) para obter mais informações sobre como determinar a opção adequada para você.

## <a name="edit-security-policies"></a>Editar políticas de segurança
Você pode editar a política de segurança padrão para cada uma de suas assinaturas do Azure na Central de Segurança. Para modificar uma política de segurança, você deve ser um proprietário, colaborador ou Administrador de Segurança dessa assinatura. Acesse o portal do Azure e siga as etapas abaixo para configurar as políticas de segurança na Central de Segurança: 

1.  No painel **Central de Segurança**, em **Geral**, clique em **Política de Segurança**.
2.  Selecione a assinatura na qual você deseja habilitar a política de segurança.
3.  Na seção **COMPONENTES DA POLÍTICA**, clique em **Política de segurança**.
4.  Esta é a política padrão atribuída pela Central de Segurança. Você pode ativar/desativar as recomendações de segurança disponíveis.
5.  Ao concluir a edição, clique em **Salvar**.

## <a name="available-security-policy-options"></a>Opções de política de segurança disponíveis

Use a tabela a seguir como referência para entender cada opção:

| Política | Quando o estado está ativado |
| --- | --- |
| Atualizações do sistema |Recupera uma lista diária das atualizações de segurança e críticas do Windows Update ou dos Serviços de Atualização do Windows Server. A lista recuperada depende do serviço configurado para a máquina virtual e recomenda que as atualizações que faltam sejam aplicadas. Para os sistemas Linux, a política usa o sistema de gerenciamento de pacotes fornecido pela distribuição para determinar os pacotes com atualizações disponíveis. Também verifica as atualizações de segurança e críticas das máquinas virtuais dos [Serviços de Nuvem do Azure](../cloud-services/cloud-services-how-to-configure.md). |
| Vulnerabilidades do SO |Analisa as configurações do sistema operacional diariamente para determinar os problemas que podem tornar a máquina virtual vulnerável a ataques. A política também recomenda alterações de configuração para tratar essas vulnerabilidades. Consulte a [lista de linhas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para obter mais informações sobre as configurações específicas que estão sendo monitoradas. (No momento, não há suporte completo para o Windows Server 2016.) |
| Proteção do ponto de extremidade |Recomenda que seja fornecida uma proteção do ponto de extremidade para todas as máquinas virtuais do Windows para ajudar a identificar e remover vírus, spyware e outros softwares mal-intencionados. |
| Criptografia do disco |Recomenda-se habilitar a criptografia de disco em todas as máquinas virtuais para aprimorar a proteção de dados em repouso. |
| Grupos de segurança de rede |Recomenda que os [grupos de segurança da rede](../virtual-network/virtual-networks-nsg.md) sejam configurados para controlar os tráfegos de entrada e saída para as VMs com pontos de extremidade públicos. Os grupos de segurança da rede configurados para uma sub-rede são herdados por todas as interfaces de rede da máquina virtual, a menos que o contrário seja especificado. Além de verificar se um grupo de segurança da rede foi configurado, essa política avalia as regras de segurança de entrada para identificar as regras que permitem o tráfego de entrada. |
| Firewall do aplicativo Web |Recomenda que um firewall do aplicativo Web seja provisionado nas máquinas virtuais quando uma das seguintes opções é verdadeira: </br></br>[IP público em nível de instância](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) é usado e as regras de segurança de entrada para o grupo de segurança de rede associado são configuradas para permitir o acesso à porta 80/443.</br></br>O IP de balanceamento de carga é usado e o balanceamento de carga associado e as regras NAT (conversão do endereço de rede de entrada) são configurados para permitir o acesso à porta 80/443. (Para obter mais informações, consulte [Suporte do Azure Resource Manager para o Balanceador de Carga](../load-balancer/load-balancer-arm.md). |
| Firewall da próxima geração |Estende as proteções da rede para além dos grupos de segurança da rede, que são internos no Azure. A Central de Segurança descobrirá as implantações para as quais um firewall da próxima geração é recomendado e permitirá que você forneça um dispositivo virtual. |
| Auditoria e detecção de ameaças do SQL |Recomenda que a auditoria de acesso ao Banco de Dados do Azure esteja habilitada para conformidade, e também para detecção de ameaça avançada, para fins de investigação. |
| Criptografia do SQL |Recomenda que a criptografia em repouso seja habilitada para o Banco de Dados SQL, backups associados e arquivos do log de transação. Mesmo se seus dados sejam violados, eles não poderão ser lidos. |
| Avaliação de vulnerabilidade |Recomenda que você instale uma solução de avaliação de vulnerabilidade na VM. |
| Criptografia do Armazenamento |Atualmente, este recurso está disponível para arquivos e blobs do Azure. Depois de habilitar a Criptografia do Serviço de Armazenamento, apenas os novos dados serão criptografados e quaisquer arquivos existentes nesta conta de armazenamento permanecerão não criptografados. |
| Acesso à Rede JIT |Quando o just in time está habilitado, a Central de Segurança bloqueia o tráfego de entrada às suas VMs do Azure, criando uma regra de NSG. Selecione as portas na VM para as quais o tráfego de entrada deverá ser bloqueado. Para saber mais, confira [Gerenciar o acesso à máquina virtual usando o just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |


## <a name="next-step"></a>Próxima etapa
Neste documento, você aprendeu a configurar políticas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md). Saiba como planejar e entender as considerações de design para adotar a Central de Segurança do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md). Saiba como gerenciar e responder aos alertas de segurança.
* [Monitoramento das soluções de parceiros na Central de Segurança do Azure](security-center-partner-solutions.md). Saiba como monitorar o status da integridade das soluções dos parceiros.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
