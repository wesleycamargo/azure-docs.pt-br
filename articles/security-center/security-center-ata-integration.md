---
title: "Conexão do Microsoft Advanced Threat Analytics à Central de Segurança do Azure | Microsoft Docs"
description: "Saiba como a Central de Segurança do Azure integra-se ao Microsoft Advanced Threat Analytics."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: e1b9e598af3b55c1d9591e5c1e529a80ae3319ca
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2017
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Conexão do Microsoft Advanced Threat Analytics à Central de Segurança do Azure
Este documento ajuda você a configurar a integração entre o Microsoft Advanced Threat Analytics e a Central de Segurança do Azure.

## <a name="why-add-advanced-threat-analytics-data"></a>Por que adicionar dados do Advanced Threat Analytics?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) é uma plataforma local que ajuda a detectar comportamentos suspeitos do usuário. Quando conectada, é possível exibir ações suspeitas detectadas pelo ATA na Central de Segurança. Essa integração permite que você exiba, correlacione e investigue todos os alertas de segurança relacionados às suas cargas de trabalho de nuvem híbrida na Central de Segurança. 

## <a name="how-do-i-configure-this-integration"></a>Como faço para configurar essa integração?
Supondo que você já tenha o ATA instalado e funcionando corretamente localmente, siga estas etapas para configurar essa integração:

1. Faça logon na Central do ATA e acesse o portal do ATA.
2. Clique em **Servidor Syslog** no painel esquerdo.

    ![Servidor syslog](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. No campo **ponto de extremidade do servidor Syslog**, digite 127.0.0.7 (deve ser esse endereço) e digite 5114 na porta (recomendado). O número da porta é uma recomendação, qualquer porta exclusiva deve funcionar. Deixe todas as outras opções como estão e clique em **Salvar**.
4. Clique em **Notificações** no painel esquerdo e habilite todas as notificações do Syslog (recomendadas) conforme mostra a imagem a seguir:

    ![Notificações](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Clique em **Salvar**.
6. Abra o painel **Central de Segurança**.
7. No painel esquerdo, clique em **Soluções de Segurança**.
8. Em **Advanced Threat Analytics**, clique em **ADICIONAR**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. Vá para a última etapa e clique em **Agente de download**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. Na página **Adicionar novo computador não Azure**, selecione o espaço de trabalho.

    ![Não Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. Na página **Agente Direto**, baixe o agente apropriado do Windows e anote a **ID do Espaço de Trabalho** e a **Chave Primária**.

    ![Agente direto](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. Instale o agente no Centro do ATA. Durante a instalação, selecione a opção **Conectar o agente ao Azure Log Analytics (OMS)** e forneça a *ID do espaço de trabalho* e a *chave primária*, quando solicitado.


Depois de concluir a instalação, a integração será concluída e você poderá ver os novos alertas enviados do ATA à Central de Segurança em **Alertas de Segurança** e **Pesquisa**. A solução aparece na página **Soluções de Segurança** em **Soluções conectadas**. 

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Microsoft ATA à Central de Segurança. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Conexão do Azure Active Directory Identity Protection à Central de Segurança do Azure](security-center-aadip-integration.md)
* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Segurança de dados da Central de Segurança do Azure](security-center-data-security.md) – saiba como os dados são gerenciados e protegidos na Central de Segurança do Azure.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenha as últimas notícias de segurança e informações do Azure.


