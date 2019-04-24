---
title: Corrigir configurações de segurança na Central de Segurança do Azure | Microsoft Docs
description: Este documento mostra como implementar a recomendação da Central de Segurança do Azure, “Corrigir configurações de segurança”.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: f4558c6fdb1e5e4f0ffb7a4b4fdb1ab62eb4cfa9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332787"
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Corrigir as configurações de segurança na Central de Segurança do Azure
A Central de Segurança do Azure analisa diariamente o SO (sistema operacional) de suas VMs (máquinas virtuais) e computadores para verificar a existência de configuração que possa tornar as VMs e computadores mais vulneráveis a ataques. A Central de Segurança recomenda que você resolva as vulnerabilidades quando sua configuração do sistema operacional não corresponder às regras de configuração de segurança e recomenda alterações de configuração para resolver essas vulnerabilidades.

Para obter mais informações sobre as configurações específicas que estão sendo monitoradas, veja a [lista de regras de configuração recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Para saber como personalizar avaliações de configuração de segurança, consulte [Personalizar configurações de segurança do sistema operacional na Central de Segurança do Azure (versão prévia)](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>Implementar a recomendação
A opção “Corrigir configurações de segurança” é apresentada como uma recomendação na Central de Segurança. A recomendação é exibida em **Recomendações** > **Computação e aplicativos**.

Este exemplo abrange a recomendação “Corrigir configurações de segurança” em **Computação e aplicativos**.
1. Na Central de Segurança, no painel esquerdo, selecione **Computação e aplicativos**.  
   A janela **Computação e aplicativos** é aberta.

   ![Corrigir as configurações de segurança][1]

2. Selecione **Corrigir configurações de segurança**.  
   A janela **Configurações de segurança** será aberta.

   ![A janela “Configurações de segurança”][2]

   A seção superior do painel exibe:

   - **Regras com falha por gravidade**: O número total de regras que a configuração do sistema operacional falhou nas VMs e computadores, divididos por gravidade.
   - **Regras com falha por tipo**: O número total de regras que a configuração do sistema operacional falhou nas VMs e computadores, divididos por tipo.
   - **Regras do Windows com falha**: O número total de regras com falha por configurações do sistema operacional Windows.
   - **Regras do Linux com falha**: O número total de regras com falha por configurações do sistema operacional Linux.

   A seção inferior do painel lista todas as regras com falha nas VMs e nos computadores e a severidade da atualização ausente. A lista contém os seguintes elementos:

   - **CCEID**: O identificador exclusivo CCE para a regra. A Central de Segurança usa a CCE (Common Configuration Enumeration) para atribuir identificadores exclusivos a regras de configuração.
   - **Nome**: O nome da regra com falha.
   - **Tipo de regra**: O tipo de regra de *Chave do Registro*, *Política de segurança*, *Política de auditoria* ou *IIS*.
   - **Nº de VMs e computadores**: O número total de VMs e computadores aos quais a regra com falha se aplica.
   - **Gravidade da regra**: O valor *Crítico*, *Importante* ou *Aviso* da CCE.
   - **Estado**: O estado atual da recomendação:

     - **Aberto**: A recomendação ainda não foi resolvida.
     - **Em andamento**: A recomendação está sendo aplicada atualmente aos recursos e não é necessário que você realize nenhuma ação.
     - **Resolvido**: A recomendação foi aplicada. Quando o problema é resolvido, a entrada fica esmaecida.

3. Para exibir os detalhes de uma regra com falha, selecione-a na lista.

   ![Exibição detalhada de uma regra de configuração com falha][3]

   A exibição detalhada exibe as seguintes informações:

   - **Nome**: O nome da regra.
   - **CCIED**: O identificador exclusivo CCE para a regra.
   - **Versão do SO**: A versão do sistema operacional da VM ou do computador.
   - **Gravidade da regra**: O valor *Crítico*, *Importante* ou *Aviso* da CCE.
   - **Descrição completa**: A descrição da regra.
   - **Vulnerabilidade**: Explicação da vulnerabilidade ou do risco se a regra não for aplicada.
   - **Possível Impacto**: O impacto de negócios quando a regra é aplicada.
   - **Contramedida**: As etapas de correção.
   - **Valor esperado**: O valor esperado quando a Central de Segurança analisa a configuração do sistema operacional da VM em relação à regra.
   - **Valor real**: O valor retornado após a análise da configuração do sistema operacional da VM em relação à regra.
   - **Operação da regra**: A operação da regra usada pela Central de Segurança durante a análise da configuração do sistema operacional da VM em relação à regra.

4. Na parte superior da janela de exibição detalhada, selecione **Pesquisar**.  
   A pesquisa abre uma lista de workspaces que têm VMs e computadores com a incompatibilidade de configurações de segurança selecionada. A seleção do workspace é mostrada apenas quando a regra selecionada se aplica a várias VMs que estão conectadas a diferentes workspaces.

   ![Workspaces listados][4]

5. Selecione um workspace.  
   Uma consulta de pesquisa de logs do Azure Monitor é aberta, filtrada para o espaço de trabalho com a incompatibilidade de configurações de segurança.

   ![Workspace com a vulnerabilidade do sistema operacional][5]

6. Selecione um computador na lista.  
   Um novo resultado da pesquisa é aberto com informações filtradas somente para esse computador.

   ![Informações detalhadas sobre o computador selecionado][6]

## <a name="next-steps"></a>Próximos passos
Este artigo mostrou como implementar a recomendação da Central de Segurança para "Corrigir configurações de segurança". Para saber como personalizar avaliações de configuração de segurança, consulte [Personalizar configurações de segurança do sistema operacional na Central de Segurança do Azure (versão prévia)](security-center-customize-os-security-config.md).

Para examinar as configurações específicas que estão sendo monitoradas, veja a [lista de regras de configuração recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). A Central de Segurança usa a CCE (Common Configuration Enumeration) para atribuir identificadores exclusivos a regras de configuração. Para obter mais informações, visite o site da [CCE](https://nvd.nist.gov/cce/index.cfm).

Para saber mais sobre a Central de Segurança, confira os seguintes recursos:

* Para obter uma lista de VMs Windows e Linux compatíveis, consulte [Plataformas com suporte na Central de Segurança do Azure](security-center-os-coverage.md).
* Para saber como configurar políticas de segurança para suas assinaturas e seus grupos de recursos do Azure, consulte [Definindo políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md).
* Para saber como as recomendações ajudam você a proteger seus recursos do Azure, consulte [Gerenciando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).
* Para saber como monitorar a integridade de seus recursos do Azure, consulte [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md).
* Para saber como gerenciar e responder a alertas de segurança, consulte [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md).
* Para saber como monitorar o status da integridade de suas soluções de parceiros, consulte [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md).
* Para encontrar respostas para as perguntas frequentes sobre como usar o serviço, consulte [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md).
* Para obter postagens no blog sobre segurança e conformidade do Azure, visite o [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
