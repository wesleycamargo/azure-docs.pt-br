---
title: "Controles de aplicativo adaptáveis na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda a usar controles de aplicativo adaptáveis na Central de Segurança do Azure para colocar aplicativos em execução em VMs do Azure na lista de permissões."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: yurid
ms.openlocfilehash: ee15b602dc90b0e777b7ccd29572b9d560ee719b
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="adaptive-application-controls-in-azure-security-center-preview"></a>Controles de aplicativo adaptáveis na Central de Segurança do Azure (Versão prévia)
Saiba como configurar o controle de aplicativo na Central de Segurança do Azure usando este passo a passo.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>O que são controles de aplicativo adaptáveis na Central de Segurança?
Os controles de aplicativo adaptáveis ajudam a controlar quais aplicativos podem ser executados em suas VMs localizadas no Azure, o que ajuda a proteger suas VMs de malware, dentre outros benefícios. A Central de Segurança usa o aprendizado de máquina para analisar os processos em execução na VM e ajuda a aplicar regras de lista de permissões usando essa inteligência. Isso simplifica bastante o processo de configurar e manter listas de permissões de aplicativo, permitindo que você:

- Bloqueie ou emita alertas sobre tentativas de execução de aplicativos mal-intencionados, inclusive os que poderiam ser ignorados pelas soluções antimalware
- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.
- Evite o uso de softwares indesejados em seu ambiente.
- Evite a execução de aplicativos antigos e sem suporte.
- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.
- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

## <a name="how-to-enable-adaptive-application-controls"></a>Como habilitar os controles de aplicativo adaptáveis?
Os controles de aplicativo adaptáveis o ajudam a definir um conjunto de aplicativos que podem ser executados em grupos de recursos configurados. Este recurso só está disponível para computadores Windows (todas as versões, clássica ou Azure Resource Manager). As etapas a seguir podem ser usadas para configurar a lista de permissões de aplicativos na Central de Segurança:

1. Abra o painel **Central de Segurança**.
2. No painel esquerdo, selecione **Controles de aplicativo adaptáveis** localizado em **Proteção de nuvem avançada**.

    ![Defesa](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

A página **Controles de aplicativo adaptativo** aparece.

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

A seção **Grupos de Recursos** contém três guias:

* **Configurado**: lista de grupos de recursos contendo as VMs que foram configuradas com controle de aplicativo.
* **Recomendado**: lista de grupos de recursos para os quais o controle de aplicativos é recomendado. A Central de Segurança usa o aprendizado de máquina para identificar as VMs que são boas candidatas a controle de aplicativo com base na consistência com que elas executam os mesmos aplicativos.
* **Nenhuma recomendação**: lista de grupos de recursos contendo VMs sem nenhuma recomendação de controle de aplicativo. Por exemplo, VMs que sempre têm aplicativos mudando e que ainda não estão estáveis.

### <a name="configure-a-new-application-control-policy"></a>Configurar uma nova política de controle de aplicativo
1. Clique na guia **Recomendado** para obter uma lista de grupos de recursos com as recomendações de controle de aplicativo:

  ![Recomendadas](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

  A lista inclui:

  - **NOME**: o nome da assinatura, do grupo de recursos ou do recurso
  - **VMs**: o número de máquinas virtuais no grupo de recursos
  - **ESTADO**: o estado das recomendações, que, na maioria dos casos, será Em aberto
  - **GRAVIDADE**: o nível de gravidade das recomendações

2. Selecione um grupo de recursos para abrir a opção **Criar regras de controle de aplicativo**.

  ![Regras de controle de aplicativo](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. Em **Selecionar VMs**, revise a lista de VMs recomendadas e desmarque as que não devem receber o controle de aplicativo. Em **Selecionar processos para regras de lista de permissões**, revise a lista de aplicativos recomendados e desmarque o que não deve ser aplicado. A lista inclui:

  - **NOME**: o caminho completo do aplicativo
  - **PROCESSOS**: quantos aplicativos residem em cada caminho
  - **COMUNS**: “Sim” indica que esses processos foram executados na maioria das VMs no grupo de recursos.
  - **EXPLORÁVEL**: um ícone de aviso indica se os aplicativos podem ser usados por um invasor para ignorar a lista de permissões de aplicativos. É recomendável examinar esses aplicativos antes da aprovação.
  - **USUÁRIOS**: usuários com permissão para executar o aplicativo

4. Após concluir suas seleções, selecione **Criar**.

Por padrão, a Central de Segurança sempre habilita o controle de aplicativo no modo *Auditoria*. Após verificar que a lista de permissões não tem nenhum efeito adverso sobre sua carga de trabalho, você pode alterar para o modo *Impor*.

A Central de Segurança conta com um mínimo de duas semanas de dados para criar uma linha de base e preencher as recomendações exclusivas por grupo de VMs. Os novos clientes da camada standard da Central de Segurança devem esperar que seus grupos de máquinas virtuais sejam exibidos primeiro na guia *sem recomendações*.

> [!NOTE]
> Como uma melhor prática de segurança, a Central de Segurança sempre tentará criar uma regra de fornecedor para os aplicativos que devem estar na lista de permissões, e somente se um aplicativo não tiver informações sobre o fornecedor (também conhecido como não assinado) é que uma regra de caminho será criada para o caminho completo do EXE específico.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Editando e monitorado um grupo configurado com controle de aplicativo

1. Para editar e monitorar um grupo configurado com controle do aplicativo, retorne até a página **Controles de aplicativo adaptáveis** e selecione **CONFIGURADO** em **Grupos de Recursos**:

  ![Grupos de recursos](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  A lista inclui:

  - **NOME**: o nome da assinatura, do grupo de recursos ou do recurso
  - **VMs**: o número de máquinas virtuais no grupo de recursos
  - **MODO**: modo de auditoria registrará tentativas de execução de aplicativos que não estão na lista de permissões. O bloqueio não permitirá a execução de aplicativos que não estão na lista de permissões
  - **PROBLEMAS**: as violações atuais

2. Selecione um grupo de recursos para fazer alterações na página **Editar política de controle de aplicativo**.

  ![Proteção](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. Em **Modo de proteção**, você pode escolher entre as seguintes opções:

  - **Auditoria**: nesse modo, a solução de controle de aplicativo não impõe as regras e faz apenas auditoria da atividade nas VMs protegidas. Isso é recomendado nos casos em que você deseja primeiro observar o comportamento geral antes de bloquear a execução de um aplicativo na VM de destino.
  - **Impor**: nesse modo, a solução de controle de aplicativo impoe as regras e verifica se os aplicativos que não têm permissão para execução estão bloqueados.

  Como mencionado anteriormente, por padrão, uma nova política de controle de aplicativo sempre é configurada no modo *Auditoria*. Em **Extensão de política**, você pode adicionar seus próprios caminhos de aplicativo que devem figurar na lista de permissões. Ao adicionar esses caminhos, a Central de Segurança criará as regras apropriadas para esses aplicativos, além das regras que já estão em vigor.

  Na seção **Problemas Recentes**, as violações atuais existentes são listadas.

  ![Problemas](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

  Essa lista inclui:
  - **PROBLEMAS**: violações que foram registradas em log, o que pode incluir o seguinte:

      - **ViolationsBlocked**: quando a solução está ativada no modo Impor e há uma tentativa de executar aplicativo que não está na lista de permissões.
      - **ViolationsAudited**: quando a solução está ativada no modo Impor e há uma execução de aplicativo que não está na lista de permissões.
      - **RulesViolatedManually**: quando um usuário tentou configurar regras manualmente nas máquinas virtuais e não por meio do portal de gerenciamento do ASC.

 - **NÃO. DE VMS**: o número de máquinas virtuais com esse tipo de problema.

  Se você clicar em cada linha, será redirecionado para a página [Log de Atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), onde poderá ver informações sobre todas as VMs com esse tipo de violação. Se você clicar nas reticências no final de cada linha, poderá excluir essa entrada específica. A seção **Máquinas virtuais configuradas** lista as VMs às quais essas regras se aplicam.

  ![Máquinas virtuais configuradas](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

  Em **Regras de lista de permissões de fornecedores**, a lista contém:

  - **REGRA**: os aplicativos para o qual uma regra de fornecedor foi criada com base nas informações de certificado encontradas para cada aplicativo
  - **USERS**: número de usuários com permissão para executar cada aplicativo

  Confira [Noções básicas sobre as Regras de Fornecedor no Applocker](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker) para obter mais informações.

  ![Regras de lista de permissões](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

  Se você clicar nas reticências no final de cada linha, poderá excluir a regra específica ou editar os usuários com permissões.

  A seção **regras de lista de permissões de caminhos** lista o caminho do aplicativo inteiro (incluindo o executável) para os aplicativos que não são assinados com um certificado digital, mas ainda são atuais nas regras de lista de permissões.

  > [!NOTE]
  > Por padrão, como uma melhor prática de segurança, a Central de Segurança sempre tentará criar uma regra de fornecedor para os arquivos EXE que devem estar na lista de permissões, e somente se um arquivo EXE não tiver informações sobre o fornecedor (também conhecido como não assinado) é que uma regra de caminho será criada para o caminho completo do EXE específico.

  ![Regras de lista de permissões de caminhos](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

  A lista contém:
  - **NOME**: o caminho completo do executável
  - **USERS**: número de usuários com permissão para executar cada aplicativo

  Se você clicar nas reticências no final de cada linha, poderá excluir a regra específica ou editar os usuários com permissões.

4. Depois de fazer alterações nos **Controles de aplicativo adaptáveis**, clique no botão **Salvar**. Se você decidir não aplicar as alterações, clique em **Descartar**.

### <a name="not-recommended-list"></a>Lista de não recomendados

A Central de Segurança só recomenda a adição de aplicativos à lista de permissões para máquinas virtuais que executem um conjunto estável de aplicativos. As recomendações não serão criadas se os aplicativos nas VMs associadas sofrerem mudança.

![Recomendações](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

A lista contém:
- **NOME**: o nome da assinatura, do grupo de recursos ou do recurso
- **VMs**: o número de máquinas virtuais no grupo de recursos

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a usar controles de aplicativo adaptáveis na Central de Segurança do Azure para colocar aplicativos em execução em VMs do Azure na lista de permissões. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como solucionar problemas comuns na Central de Segurança.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
