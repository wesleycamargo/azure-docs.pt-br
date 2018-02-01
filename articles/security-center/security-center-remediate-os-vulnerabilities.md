---
title: "Corrigir configurações de segurança na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar a recomendação da Central de Segurança do Azure, “Corrigir configurações de segurança”."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 477973298d8cc9d99da78e36274933e0bb737c4f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Corrigir as configurações de segurança na Central de Segurança do Azure
A Central de Segurança do Azure analisa diariamente o SO (sistema operacional) de suas VMs (máquinas virtuais) e computadores para verificar a existência de configuração que possa tornar as VMs e computadores mais vulneráveis a ataques. A Central de Segurança recomenda que você resolva as vulnerabilidades quando sua configuração do sistema operacional não corresponder às regras de configuração de segurança e recomenda alterações de configuração para resolver essas vulnerabilidades.

Para obter mais informações sobre as configurações específicas que estão sendo monitoradas, veja a [lista de regras de configuração recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Para saber como personalizar avaliações de configuração de segurança, consulte [Personalizar configurações de segurança do sistema operacional na Central de Segurança do Azure (versão prévia)](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>Implementar a recomendação
A opção “Corrigir configurações de segurança” é apresentada como uma recomendação na Central de Segurança. A recomendação é exibida em **Recomendações** > **Computação**.

Este exemplo abrange a recomendação “Corrigir configurações de segurança” em **Computação**.
1. Na Central de Segurança, no painel esquerdo, selecione **Computação**.  
  A janela **Computação** será aberta.

   ![Corrigir as configurações de segurança][1]

2. Selecione **Corrigir configurações de segurança**.  
  A janela **Configurações de segurança** será aberta.

   ![A janela “Configurações de segurança”][2]

  A seção superior do painel exibe:

  - **Regras com falha por severidade**: o número total de regras que a configuração do sistema operacional falhou nas VMs e computadores, divididos por severidade.
  - **Regras com falha por tipo**: o número total de regras que a configuração do sistema operacional falhou nas VMs e computadores, divididos por tipo.
  - **Regras com falha do Windows**: o número total de regras com falha por configurações do sistema operacional Windows.
  - **Regras com falha do Linux**: o número total de regras com falha por configurações do sistema operacional Linux.

  A seção inferior do painel lista todas as regras com falha nas VMs e nos computadores e a severidade da atualização ausente. A lista contém os seguintes elementos:

  - **CCEID**: o identificador exclusivo CCE da regra. A Central de Segurança usa a CCE (Common Configuration Enumeration) para atribuir identificadores exclusivos a regras de configuração.
  - **Nome**: o nome da regra com falha.
  - **Tipo de regra**: o tipo de regra *Chave do Registro*, *Política de segurança* ou *Política de auditoria*.
  - **Nº de VMs e computadores**: o número total de VMs e computadores aos quais a regra com falha se aplica.
  - **Severidade da regra**: o valor *Crítico*, *Importante* ou *Aviso* da CCE.
  - **Estado**: o estado atual da recomendação:

    - **Aberta**: a recomendação ainda não foi resolvida.
    - **Em Andamento**: a recomendação está sendo aplicada atualmente aos recursos, e não é necessário que você realize nenhuma ação.
    - **Resolvido**: a recomendação foi aplicada. Quando o problema é resolvido, a entrada fica esmaecida.

3. Para exibir os detalhes de uma regra com falha, selecione-a na lista.

   ![Exibição detalhada de uma regra de configuração com falha][3]

   A exibição detalhada exibe as seguintes informações:

   - **Nome**: o nome da regra.
   - **CCEID**: o identificador exclusivo CCE da regra.
   - **Versão do sistema operacional**: a versão do sistema operacional da VM ou do computador.
   - **Severidade da regra**: o valor *Crítico*, *Importante* ou *Aviso* da CCE.
   - **Descrição completa**: a descrição da regra.
   - **Vulnerabilidade**: explicação da vulnerabilidade ou do risco se a regra não for aplicada.
   - **Impacto potencial**: o impacto para os negócios quando a regra é aplicada.
   - **Contramedida**: as etapas de correção.
   - **Valor esperado**: o valor esperado quando a Central de Segurança analisa a configuração do sistema operacional da VM em relação à regra.
   - **Valor real**: o valor retornado após a análise da configuração do sistema operacional da VM em relação à regra.
   - **Operação da regra**: a operação da regra usada pela Central de Segurança durante a análise da configuração do sistema operacional da VM em relação à regra.

4. Na parte superior da janela de exibição detalhada, selecione **Pesquisar**.  
  A pesquisa abre uma lista de espaços de trabalho que têm VMs e computadores com a incompatibilidade de configurações de segurança selecionada. A seleção do espaço de trabalho é mostrada apenas quando a regra selecionada se aplica a várias VMs que estão conectadas a diferentes espaços de trabalho.

   ![Espaços de trabalho listados][4]

5. Selecione um espaço de trabalho.  
  Uma consulta de pesquisa do Log Analytics é aberta, filtrada para o espaço de trabalho com a incompatibilidade de configurações de segurança.

   ![Espaço de trabalho com a vulnerabilidade do sistema operacional][5]

6. Selecione um computador na lista.  
  Um novo resultado da pesquisa é aberto com informações filtradas somente para esse computador.

   ![Informações detalhadas sobre o computador selecionado][6]

## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como implementar a recomendação da Central de Segurança para "Corrigir configurações de segurança". Para saber como personalizar avaliações de configuração de segurança, consulte [Personalizar configurações de segurança do sistema operacional na Central de Segurança do Azure (Versão Prévia)](security-center-customize-os-security-config.md).

Para examinar as configurações específicas que estão sendo monitoradas, veja a [lista de regras de configuração recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). A Central de Segurança usa a CCE (Common Configuration Enumeration) para atribuir identificadores exclusivos a regras de configuração. Para obter mais informações, visite o site da [CCE](https://nvd.nist.gov/cce/index.cfm).

Para saber mais sobre a Central de Segurança, confira os seguintes recursos:

* Para obter uma lista de VMs Windows e Linux compatíveis, consulte [Plataformas com suporte na Central de Segurança do Azure](security-center-os-coverage.md). 
* Para saber como configurar políticas de segurança para suas assinaturas e seus grupos de recursos do Azure, consulte [Definindo políticas de segurança na Central de Segurança do Azure](security-center-policies.md). 
* Para saber como as recomendações ajudam você a proteger seus recursos do Azure, consulte [Gerenciando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md). 
* Para saber como monitorar a integridade de seus recursos do Azure, consulte [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). 
* Para saber como gerenciar e responder a alertas de segurança, consulte [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md).
* Para saber como monitorar o status da integridade de suas soluções de parceiros, consulte [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md).
* Para encontrar respostas para as perguntas frequentes sobre como usar o serviço, consulte [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md).
* Para obter postagens no blog sobre segurança e conformidade do Azure, visite o [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
