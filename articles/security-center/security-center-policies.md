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
ms.date: 06/16/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 2593e6846c897644017083b49ad4ba8219696c6c
ms.contentlocale: pt-br
ms.lasthandoff: 06/17/2017


---
# <a name="set-security-policies-in-azure-security-center"></a>Configurar políticas de segurança na Central de Segurança do Azure
Este documento ajuda a configurar as políticas de segurança na Central de Segurança, guiando você nas etapas necessárias para executar essa tarefa.

>[!NOTE] 
>A partir do início de junho de 2017, a Central de Segurança usará o Microsoft Monitoring Agent para coletar e armazenar dados. Veja [Migração da Plataforma Central de Segurança do Azure](security-center-platform-migration.md) para saber mais. As informações deste artigo representam a funcionalidade da Central de Segurança após a transição para o Microsoft Monitoring Agent.
>

## <a name="what-are-security-policies"></a>Quais são políticas de segurança?
Uma política de segurança define o conjunto de controles recomendados para os recursos na assinatura ou grupo de recursos especificado. Na Central de Segurança, você define as políticas para as assinaturas do Azure de acordo com as necessidades de segurança de sua empresa e os tipos de aplicativos ou a confidencialidade dos dados de cada assinatura.

Por exemplo, os recursos usados para o desenvolvimento ou teste podem ter requisitos de segurança diferentes daqueles usados para os aplicativos de produção. Da mesma forma, os aplicativos com dados regulamentados, como as informações de identificação pessoal, podem requerer um nível mais alto de segurança. As políticas de segurança habilitadas na Central de Segurança do Azure determinam as recomendações de segurança e o monitoramento para ajudar a identificar as potenciais vulnerabilidades e atenuar as ameaças. Leia o [Guia de Planejamento e Operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md) para obter mais informações sobre como determinar a opção adequada para você.

## <a name="set-security-policies"></a>Definir políticas de segurança
É possível configurar políticas de segurança para cada assinatura. Para modificar uma política de segurança, você deve ser um proprietário ou colaborador dessa assinatura. Acesse o portal do Azure e siga as etapas abaixo para configurar as políticas de segurança na Central de Segurança:

1. Clique no bloco **Política** no painel Central de Segurança.
2. Na folha Política de Segurança que abrir, selecione a assinatura na qual você deseja habilitar a política de segurança.

    ![Definir a política](./media/security-center-policies/security-center-policies-fig1-ga.png)
3. A folha **Política de segurança** da assinatura selecionada é aberta com um conjunto de opções. As opções disponíveis nesta folha são:

   * **Política prevenção**: use essa opção para configurar as políticas por assinatura ou grupo de recursos.  
   * **Notificação por email**: use essa opção para configurar uma notificação por email que é enviada na primeira ocorrência diária de um alerta e para os alertas de alta gravidade. As preferências do email podem ser configuradas apenas para as políticas da assinatura. Leia [Fornecer detalhes de contato da segurança na Central de Segurança do Azure](security-center-provide-security-contact-details.md) para obter mais informações sobre como configurar uma notificação por email.
   * **Camada de preços**: use essa opção para atualizar a seleção do tipo de preços. Confira os [Preços da Central de Segurança](security-center-pricing.md) para saber mais sobre as opções de preços.
4. Verifique se a opção **Coletar dados das máquinas virtuais** está **Ativada**. Essa opção habilita a coleta de log automática para os recursos novos e existentes usando o Microsoft Monitoring Agent – este é o mesmo agente usado pelo serviço Operations Management Suite e Log Analytics. Os dados coletados desse agente são armazenados em um espaço de trabalho do Log Analytics existente associado à sua assinatura do Azure ou a novos espaços de trabalho, levando em conta a localização geográfica da VM.

5. Na folha **Política de Segurança**, clique em **Política de prevenção** para ver as opções disponíveis. Clique em **Ativado** para habilitar as recomendações de segurança relevantes para esta assinatura.

    ![Selecionar as políticas de segurança](./media/security-center-policies/security-center-policies-fig4-newUI.png)

Use a tabela a seguir como referência para entender cada opção:

| Política | Quando o estado está ativado |
| --- | --- |
| Atualizações do sistema |Recupera uma lista diária das atualizações de segurança e críticas do Windows Update ou dos Serviços de Atualização do Windows Server. A lista recuperada depende do serviço configurado para a máquina virtual e recomenda que as atualizações que faltam sejam aplicadas. Para os sistemas Linux, a política usa o sistema de gerenciamento de pacotes fornecido pela distribuição para determinar os pacotes com atualizações disponíveis. Também verifica as atualizações de segurança e críticas das máquinas virtuais dos [Serviços de Nuvem do Azure](../cloud-services/cloud-services-how-to-configure.md). |
| Vulnerabilidades do SO |Analisa as configurações do sistema operacional diariamente para determinar os problemas que podem tornar a máquina virtual vulnerável a ataques. A política também recomenda alterações de configuração para tratar essas vulnerabilidades. Consulte a [lista de linhas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para obter mais informações sobre as configurações específicas que estão sendo monitoradas. (No momento, não há suporte completo para o Windows Server 2016.) |
| Proteção do ponto de extremidade |Recomenda que seja fornecida uma proteção do ponto de extremidade para todas as máquinas virtuais do Windows para ajudar a identificar e remover vírus, spyware e outros softwares mal-intencionados. |
| Criptografia do disco |Recomenda-se habilitar a criptografia de disco em todas as máquinas virtuais para aprimorar a proteção de dados em repouso. |
| Grupos de segurança de rede |Recomenda que os [grupos de segurança da rede](../virtual-network/virtual-networks-nsg.md) sejam configurados para controlar os tráfegos de entrada e saída para as VMs com pontos de extremidade públicos. Os grupos de segurança da rede configurados para uma sub-rede serão herdados por todas as interfaces de rede da máquina virtual, a menos que o contrário seja especificado. Além de verificar se um grupo de segurança da rede foi configurado, essa política avalia as regras de segurança de entrada para identificar as regras que permitem o tráfego de entrada. |
| Firewall do aplicativo Web |Recomenda que um firewall do aplicativo Web seja provisionado nas máquinas virtuais quando uma das seguintes opções é verdadeira: </br></br>[IP público em nível de instância](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) é usado e as regras de segurança de entrada para o grupo de segurança de rede associado são configuradas para permitir o acesso à porta 80/443.</br></br>O IP de balanceamento de carga é usado e o balanceamento de carga associado e as regras NAT (conversão do endereço de rede de entrada) são configurados para permitir o acesso à porta 80/443. (Para obter mais informações, consulte [Suporte do Azure Resource Manager para o Balanceador de Carga](../load-balancer/load-balancer-arm.md). |
| Firewall da próxima geração |Estende as proteções da rede para além dos grupos de segurança da rede, que são internos no Azure. A Central de Segurança descobrirá as implantações para as quais um firewall da próxima geração é recomendado e permitirá que você forneça um dispositivo virtual. |
| Auditoria e detecção de ameaças do SQL |Recomenda que a auditoria de acesso ao Banco de Dados do Azure esteja habilitada para conformidade, e também para detecção de ameaça avançada, para fins de investigação. |
| Criptografia do SQL |Recomenda que a criptografia em repouso seja habilitada para o Banco de Dados SQL, backups associados e arquivos do log de transação. Mesmo se seus dados sejam violados, eles não poderão ser lidos. |
| Avaliação de vulnerabilidade |Recomenda que você instale uma solução de avaliação de vulnerabilidade na VM. |
| Criptografia do Armazenamento |Atualmente, este recurso está disponível para arquivos e blobs do Azure. Observe que, depois de habilitar a Criptografia do Serviço de Armazenamento, apenas os novos dados serão criptografados e quaisquer arquivos existentes nesta conta de armazenamento permanecerão não criptografados. |

Depois de configurar todas as opções, clique em **OK** na folha **Política de Segurança** com as recomendações e clique em **Salvar** na folha **Política de Segurança** com as configurações iniciais.

> [!NOTE]
> O tipo de preço é aplicável para o nível de grupo de recursos. Para saber mais, visite nossa página [Preços](https://azure.microsoft.com/pricing/details/security-center/).
>
>

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu como configurar políticas de segurança na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md). Saiba como planejar e entender as considerações de design para adotar a Central de Segurança do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md). Saiba como gerenciar e responder aos alertas de segurança.
* [Monitoramento das soluções de parceiros na Central de Segurança do Azure](security-center-partner-solutions.md). Saiba como monitorar o status da integridade das soluções dos parceiros.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.

