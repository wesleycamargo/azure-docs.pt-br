---
title: "Preços da Central de Segurança | Microsoft Docs"
description: "Este artigo fornece informações sobre os preços para a Central de Segurança do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: 9c20ace9112c62e733b7b679e3f0be717144f3c2
ms.openlocfilehash: 933a485e36b3cf0f514f28fc069d49161605af3a
ms.contentlocale: pt-br
ms.lasthandoff: 02/23/2017


---
# <a name="azure-security-center-pricing"></a>Preços da Central de Segurança do Azure
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

## <a name="pricing-tiers"></a>Tipos de preço
A Central de Segurança é oferecida em duas camadas:

* A **camada Gratuita** é habilitada automaticamente em todas as assinaturas do Azure. A camada Gratuita fornece visibilidade do estado de segurança de seus recursos do Azure, a política de segurança básica, recomendações de segurança e integração a produtos de segurança e serviços de parceiros.
* A **camada Padrão** adiciona recursos de detecção avançada de ameaças, inclusive inteligência de ameaças, análise comportamental, detecção de anomalias, incidentes de segurança e relatórios de avaliação de risco. A camada Standard é oferecida gratuitamente nos 60 primeiros dias.

Para obter mais informações, confira a [página de preços](https://azure.microsoft.com/pricing/details/security-center/) da Central de Segurança.

> [!NOTE]
> A Central de Segurança usa o armazenamento do Azure para salvar dados de segurança gerados pelos nós protegidos. Os custos associados a esse armazenamento não estão incluídos no preço do serviço e são cobrados separadamente de acordo com as [taxas normais de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Encargos de armazenamento são aplicáveis mesmo durante a avaliação gratuita de 60 dias.
>
>

## <a name="try-standard-free-for-60-days"></a>Experimentar a versão Standard gratuitamente por 60 dias
A camada Standard é oferecida gratuitamente nos 60 primeiros dias. Ao fim dos 60 dias, se você optar por continuar usando o serviço, começaremos a cobrar automaticamente pelo uso.

Para obter a camada Standard:

1. Selecione o bloco **Política** na folha **Central de Segurança**.
2. Selecione a assinatura que você deseja atualizar para o Padrão.
3. Na folha **Política de segurança**, selecione **Camada de preços**.
4. Na folha **Escolha seu tipo de preços**, selecione **Padrão**.
5. Clique em **Selecionar**.

![Camada padrão][1]

## <a name="why-upgrade-to-standard"></a>Por que atualizar para Padrão?
A camada Padrão da Central de Segurança fornece todos os recursos da camada Gratuita, mais a detecção avançada de ameaças. A detecção avançada de ameaças ajuda a identificar ameaças ativas que direcionam os recursos do Azure e fornece as percepções necessárias para que você reaja rapidamente.

A Central de Segurança emprega análise de segurança avançada, que vai além das abordagens baseadas em assinatura. As inovações em tecnologias de big data e aprendizado de máquina são usadas para avaliar eventos em toda a malha de nuvem, detectando ameaças que seriam impossíveis de identificar usando abordagens manuais e prevendo a evolução de ataques.

A análise de segurança fornecida com a camada padrão é:

* **Inteligência contra ameaças** - procura atores ruins usando a inteligência contra ameaça e produtos e serviços Microsoft, da unidade de Crimes digitais da Microsoft, do Microsoft Security Response Center e de feeds externos
* **Análise comportamental** - aplica padrões conhecidos para descobrir os comportamentos mal-intencionados
* **Detecção de anomalias** - usa estatísticas de criação de perfil para criar uma linha de base histórica. Ela o alertará sobre desvios das linhas de base estabelecidas em conformidade com um vetor de possível ataque

Na folha **Alertas de segurança** abaixo, a Central de Segurança detectou um **incidente** de segurança. Um incidente de segurança é uma agregação de todos os alertas de um recurso que se alinham com os padrões da cadeia de desativações. Selecionar o incidente de segurança revela mais detalhes sobre o incidente e lista os alertas relacionados. A seleção de um alerta fornece mais informações sobre essa ocorrência.

![Incidente de segurança][2]

O alerta de **Comunicação de rede** abaixo fornece detalhes sobre o alerta. Os detalhes incluem a descrição completa, a gravidade, o estado atual (que, nesse caso, é ignorado, o que significa que o usuário tomou medidas para ignorá-lo), o recurso atacado e as etapas de correção. Também há uma lista de links para os relatórios do Microsoft Threat Intelligence. Esses relatórios podem ser usados para fins de defesa e correções de segurança.

![Detalhes do alerta de segurança][3]

## <a name="enable-data-collection"></a>Habilitar coleta de dados
Para habilitar a análise de comportamento de máquina virtual, a coleta de dados deve ser ativada.

Para validar se a coleta de dados está habilitada:

1. Selecione o bloco **Política**. A folha **Política de segurança** será aberta, listando as assinaturas do Azure.
2. Selecione uma assinatura.
3. Se a **Coleta de Dados** for desativada, altere-a para ativada e salve a alteração.

Confira [Habilitar coleta de dados na Central de Segurança do Azure](security-center-enable-data-collection.md) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
* Neste documento, foram apresentados os preços da Central de Segurança. Para obter mais informações sobre preços, confira a [página de preços](https://azure.microsoft.com/pricing/details/security-center/) da Central de Segurança.
* Para saber mais sobre os recursos de detecção avançada da Central de Segurança, confira [Recursos de detecção da Central de Segurança do Azure](security-center-detection-capabilities.md).
* Se você tiver dúvidas sobre como usar a Central de Segurança, confira as [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md).
* Se você ainda tiver dúvidas sobre como usar a Central de Segurança ou o Azure, acesse os [fóruns do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc).

<!--Image references-->
[1]: ./media/security-center-pricing/standard.png
[2]: ./media/security-center-pricing/incident.png
[3]: ./media/security-center-pricing/network-alert.png

