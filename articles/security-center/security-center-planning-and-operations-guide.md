---
title: "Guia de planejamento e operações da Central de Segurança | Microsoft Docs"
description: "Este documento ajuda você a planejar antes de adotar a Central de Segurança do Azure e fornece considerações sobre as operações diárias."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: f984e4a2-ac97-40bf-b281-2f7f473494c4
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: c502e4363dbaa37455d1aad90d1e9fa855fd09b0
ms.contentlocale: pt-br
ms.lasthandoff: 06/17/2017


---
# <a name="azure-security-center-planning-and-operations-guide"></a>Guia de planejamento e operações da Central de Segurança do Azure
Este guia destina-se a profissionais de TI (tecnologia da informação), arquitetos de TI, analistas de segurança da informação e administradores de nuvem cujas organizações planejam usar a Central de Segurança do Azure.

>[!NOTE] 
>A partir do início de junho de 2017, a Central de Segurança usará o Microsoft Monitoring Agent para coletar e armazenar dados. Veja [Migração da Plataforma Central de Segurança do Azure](security-center-platform-migration.md) para saber mais. As informações deste artigo representam a funcionalidade da Central de Segurança após a transição para o Microsoft Monitoring Agent.
>

## <a name="planning-guide"></a>Guia de planejamento
Este guia aborda um conjunto de etapas e tarefas que você pode seguir para otimizar seu uso da Central de Segurança com base no modelo de gerenciamento de nuvem e nos requisitos de segurança de sua organização. Para tirar total proveito da Central de Segurança, é importante entender como as pessoas ou equipes diferentes em sua organização usam o serviço para atender às necessidades de desenvolvimento e operações seguras, monitoramento, administração resposta a incidentes. As principais áreas a serem consideradas ao planejar o uso da Central de Segurança são:

* Funções de segurança e controles de acesso
* Políticas de segurança e recomendações 
* Coleta de dados e armazenamento
* Monitoramento contínuo de segurança
* Resposta a incidentes

Na próxima seção, você aprenderá a planejar cada uma dessas áreas e aplicar essas recomendações de acordo com suas necessidades.

> [!NOTE]
> Leia as [Perguntas frequentes (FAQ) da Central de Segurança do Azure](security-center-faq.md) para obter uma lista das perguntas comuns que também podem ser úteis durante as fases de design e planejamento.
> 

## <a name="security-roles-and-access-controls"></a>Funções de segurança e controles de acesso
Dependendo do tamanho e da estrutura de sua organização, vários indivíduos e equipes podem usar a Central de Segurança para executar tarefas variadas relacionadas à segurança. No diagrama a seguir, você tem um exemplo de pessoas fictícias e suas respectivas funções e responsabilidades de segurança:

![Funções](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

A Central de Segurança permite que essas pessoas atendam a várias responsabilidades. Por exemplo:

**Jeff (proprietário de carga de trabalho de nuvem)**

* Gerenciar uma carga de trabalho de nuvem e seus recursos relacionados
* Responsáveis pela implementação e manutenção das proteções de acordo com a política de segurança da empresa

**Ellen (CISO/CIO)**

* Responsável por todos os aspectos de segurança da empresa
* Deseja entender a postura de segurança da empresa nas cargas de trabalho de nuvem
* Precisa ser informado dos riscos e principais ataques

**David (segurança de TI)**

* Define as políticas de segurança da empresa para garantir as devidas proteções no local
* Monitora a conformidade com as políticas
* Gera relatórios de liderança ou auditores

**Judy (Operações de Segurança)**

* Monitora e responde a alertas de segurança 24/7
* Escala para o Proprietário da Carga de Trabalho de Nuvem ou Analista de Segurança de TI

**Sam (Analista de Segurança)**

* Investiga os ataques
* Trabalhar com o Proprietário da Carga de Trabalho de Nuvem para aplicar a correção 

A Central de Segurança usa o [RBAC (Controle de Acesso Baseado em Função)](../active-directory/role-based-access-control-configure.md) que fornece [funções internas](../active-directory/role-based-access-built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure. Quando um usuário abre a Central de Segurança, ele vê apenas as informações relacionadas aos recursos aos quais tem acesso. Isso significa que o usuário recebe a função de Leitor, Colaborador ou Proprietário para a assinatura ou grupo de recursos ao qual o recurso pertence. Além dessas funções, há duas funções específicas da Central de Segurança:

- **Leitor de segurança**: o usuário que pertence a essa função é capaz de ver os direitos da Central de segurança, que inclui recomendações, alertas, política e integridade, mas não será capaz de fazer alterações.
- **Administrador de segurança**: o mesmo que o leitor de segurança, mas ele também pode atualizar a política de segurança, ignorar as recomendações e alertas.

As funções da Central de Segurança descritas acima não têm acesso a outras áreas de serviço do Azure como Armazenamento, Web e Móveis ou Internet das Coisas.  

> [!NOTE]
> Um usuário precisa ser pelo menos o proprietário de uma assinatura, do grupo de recursos ou colaborador para ser capaz de ver a Central de Segurança no Azure. 
> 
> 

Usando as pessoas explicadas no diagrama anterior, o seguinte RBAC seria necessário:

**Jeff (proprietário de carga de trabalho de nuvem)**

* Proprietário/Colaborador do grupo de recursos

**David (segurança de TI)**

* Proprietário da assinatura/Colaborador ou o administrador de segurança

**Judy (Operações de Segurança)**

* Leitor de assinatura ou o Leitor de segurança para exibir alertas
* Proprietário/Colaborador ou Administrador de Segurança da assinatura necessário para descartar os alertas

**Sam (Analista de Segurança)**

* Leitor de assinatura para exibir alertas
* Proprietário/Colaborador da Assinatura necessário para descartar os alertas
* O acesso ao espaço de trabalho poderá ser exigido

Algumas informações importantes a serem consideradas:

* Somente os Proprietários/Colaboradores da assinatura e Administradores de segurança podem editar uma política de segurança
* Somente os Proprietários e os Colaboradores da assinatura e do grupo de recursos podem aplicar recomendações de segurança para um recurso

Ao planejar o controle de acesso usando o RBAC para a Central de Segurança, compreenda quem em sua organização usará a Central de Segurança. Além disso, quais tipos de tarefas irão executar, em seguida, configurar o RBAC de acordo.

> [!NOTE]
> Recomendamos que você atribua a função menos permissiva necessária para os usuários realizarem suas tarefas. Por exemplo, os usuários que precisarem apenas exibir informações sobre o estado de segurança dos recursos, mas não precisarem executar ações, por exemplo, aplicar recomendações ou editar políticas, deverão receber a função de Leitor.
> 
> 

## <a name="security-policies-and-recommendations"></a>Políticas de segurança e recomendações 
Uma política de segurança define o conjunto de controles recomendados para os recursos na assinatura ou grupo de recursos especificado. Na Central de Segurança, você define as políticas de acordo com os requisitos de segurança de sua empresa e com o tipo de aplicativo ou confidencialidade dos dados.

As políticas habilitadas no nível da assinatura serão propagadas automaticamente para todos os grupos de recursos na assinatura, como mostrado no diagrama a seguir:

![Políticas de Segurança](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig2-newUI.png)

> [!NOTE]
> Se você precisar revisar quais políticas foram alteradas, poderá usar os [Logs de Auditoria do Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2015/03/10/audit-logs-for-azure-events/). As alterações na política são sempre registradas nos Logs de auditoria do Azure.
> 
> 

### <a name="security-recommendations"></a>Recomendações de segurança
Antes de configurar as políticas de segurança, examine cada uma das [recomendações de segurança](security-center-recommendations.md)e determine se essas políticas são apropriadas para suas várias assinaturas e grupos de recursos. Também é importante entender que ação deve ser realizada para lidar com as [Recomendações de segurança](https://docs.microsoft.com/en-us/azure/security-center/security-center-recommendations) e quem na sua organização será responsável por monitorar novas recomendações e tomar as medidas necessárias.

A Central de Segurança recomendará que você forneça os detalhes de contato da segurança para sua assinatura do Azure. Essas informações serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente têm sido acessados por uma pessoa não autorizada ou ilegal. Leia [Fornecer detalhes de contato da segurança na Central de Segurança do Azure](security-center-provide-security-contact-details.md) para obter mais informações sobre como habilitar essa recomendação.

## <a name="data-collection-and-storage"></a>Coleta de dados e armazenamento
A Central de Segurança do Azure usa o Microsoft Monitoring Agent (ele é o mesmo agente usado pelos serviços Operations Management Suite e Log Analytics) para coletar dados de segurança de suas máquinas virtuais. Os dados coletados por esse agente são armazenados nos seus espaços de trabalho do Log Analytics.

### <a name="agent"></a>Agente

Depois que a coleta de dados é habilitada na política de segurança, o Microsoft Monitoring Agent (para [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) ou [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)) é instalado em todas as VMs do Azure e nas que vierem a ser criadas.  Se a VM já possui o Microsoft Monitoring Agent instalado, a Central de Segurança do Azure otimizará o agente instalado atual. O processo do agente foi projetado para não ser invasivo e ter um impacto muito pequeno sobre o desempenho da VM.

O Microsoft Monitoring Agent para Windows exige o uso da porta TCP 443. Confira o [artigo de solução de problemas](security-center-troubleshooting-guide.md) para obter mais detalhes.

Se em algum momento você quiser desabilitar a Coleta de dados, desative-a na política de segurança. No entanto, como o Microsoft Monitoring Agent pode ser usado por outro serviço de gerenciamento e monitoramento do Azure, o agente não será desinstalado automaticamente quando você desligar a coleta de dados na Central de Segurança. Você pode desinstalar o agente manualmente, se necessário.

> [!NOTE]
> Para obter uma lista das VMs com suporte, leia as [Perguntas frequentes (FAQ) da Central de Segurança do Azure](security-center-faq.md).
> 

### <a name="workspace"></a>Espaço de trabalho

Os dados coletados do Microsoft Monitoring Agent (em nome da Central de Segurança do Azure) são armazenados em um espaço de trabalho do Log Analytics existente associado à sua assinatura do Azure ou em um novo espaço, levando em conta a área geográfica da VM. 

No portal do Azure, você pode navegar para ver uma lista de espaços de trabalho do Log Analytics, incluindo aqueles criados pela Central de Segurança do Azure. Um grupo de recursos relacionados será criado para novos espaços de trabalho. Ambos seguirão esta convenção de nomenclatura: 

* Espaço de trabalho: *DefaultWorkspace-[ID da assinatura]-[localização geográfica]*
* Grupo de recursos: *DefaultResouceGroup-[localização geográfica]*

No caso de espaços de trabalho criados pela Central de Segurança do Azure, os dados serão retidos por 30 dias. No caso dos espaços de trabalho existentes, a retenção ocorre com base no tipo de preço do espaço de trabalho.

> [!NOTE]
> A Microsoft está comprometida com a proteção da privacidade e da segurança dos dados. A Microsoft obedece às diretrizes rígidas de conformidade e segurança — da codificação à operação de um serviço. Para saber mais sobre manipulação de dados e privacidade, leia [Segurança de dados da Central de Segurança do Azure](security-center-data-security.md).
> 

## <a name="ongoing-security-monitoring"></a>Monitoramento contínuo de segurança
Após a configuração inicial e a aplicação das recomendações da Central de Segurança, a próxima etapa será considerar os processos operacionais da Central de Segurança.

Para acessar a Central de Segurança no Portal do Azure, clique em **Procurar** e digite **Central de Segurança** no campo **Filtro**. Os modos de exibição que o usuário obtém estão de acordo com esses filtros aplicados. O exemplo abaixo mostra um ambiente com muitos problemas a serem resolvidos:

![painel Transações da Web](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig6.png)

> [!NOTE]
> A Central de Segurança não interferirá nos seus procedimentos normais de operação. Ela vai monitorar passivamente as implantações e fornecerá recomendações com base nas políticas de segurança habilitadas.

Ao aceitar usar a Central de Segurança em seu ambiente atual do Azure pela primeira vez, revise todas as recomendações, o que pode ser feito no bloco **Recomendações** ou de acordo com o recurso (**Computação**, **Rede**, **Armazenamento e Dados** e **Aplicativo**).

Depois de endereçar todas as recomendações, a seção **Prevenção** deverá ficar verde para todos os recursos endereçados. Nesse ponto, o monitoramento contínuo fica mais fácil, pois você só executará ações com base nas alterações nos blocos de integridade e de recomendações de segurança do recurso.

A seção **Detecção** é mais reativa, pois são alertas sobre os problemas que estão acontecendo agora ou que ocorreram no passado e foram detectados pelos controles da Central de Segurança e sistemas de terceiros. O bloco Alertas de Segurança mostra gráficos de barras que representam o número de alertas de detecção de ameaças encontrados em cada dia, e sua distribuição entre as várias categorias de gravidade (baixa, média, alta). Para obter mais informações sobre os Alertas de Segurança, leia [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md).

> [!NOTE]
> Você também pode aproveitar o Microsoft Power BI para visualizar os dados da Central de Segurança. Leia [Obter informações nos dados da Central de Segurança do Azure com o Power BI](security-center-powerbi.md).
> 
> 

### <a name="monitoring-for-new-or-changed-resources"></a>Monitoramento de recursos novos ou alterados
A maioria dos ambientes do Azure são dinâmicos, com novos recursos gerados e excluídos regularmente, com configurações ou alterações etc. A Central de Segurança ajuda a garantir que você tenha visibilidade sobre o estado de segurança desses novos recursos.

Quando você adiciona novos recursos (VMs, Bancos de Dados SQL) ao seu Ambiente do Azure, a Central de Segurança descobre automaticamente esses recursos e começa a monitorar a segurança. Isso também inclui as funções Web do PaaS e as funções de trabalho. Se a Coleta de Dados estiver habilitada na [Política de Segurança](security-center-policies.md), os recursos de monitoramento adicionais serão habilitados automaticamente para as máquinas virtuais.

![Principais áreas](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. Nas máquinas virtuais, clique em **Computação**, na seção **Prevenção**. Os possíveis problemas com a habilitação dos dados ou recomendações relacionadas ocorrerão na guia **Visão geral** e na seção **Recomendações de monitoramento**.
2. Veja as **Recomendações** para ver quais riscos de segurança, se houver, foram identificados para o novo recurso.
3. É muito comum que, quando novas VMs são adicionadas ao seu ambiente, apenas o sistema operacional seja instalado inicialmente. Talvez o proprietário do recurso precise de algum tempo para implantar outros aplicativos que serão usados por essas VMs.  É ideal que você saiba o objetivo final dessa carga de trabalho. Ele será um Servidor de Aplicativos? Com base no que essa nova carga de trabalho será, você pode habilitar a **Política de Segurança**apropriada, que é a terceira etapa neste fluxo de trabalho.
4. À medida que novos recursos são adicionados ao seu ambiente do Azure, é possível que novos alertas apareçam no bloco **Alertas de Segurança** . Sempre verifique se há novos alertas nesse bloco e tome ações de acordo com as recomendações da Central de Segurança.

Convém também monitorar regularmente o estado dos recursos existentes para identificar as alterações de configuração que criaram riscos de segurança, desvios das linhas de base recomendadas e alertas de segurança. Inicie no painel da Central de Segurança. A partir daí, há três áreas principais a serem analisadas de forma consistente.

![Operações](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. O painel da seção **Prevenção** fornece acesso rápido aos recursos chave. Use esta opção para monitorar Computação, Rede, Armazenamento e dados e Aplicativos.
2. O painel **Recomendações** permite que você examine as recomendações da Central de Segurança. Durante o monitoramento contínuo, você pode achar que não há recomendações diárias, o que é normal, pois endereçou todas as recomendações na configuração inicial da Central de Segurança. Por esse motivo, talvez você não tenha novas informações nesta seção diariamente e precisará acessá-la apenas de acordo com a necessidade.
3. A seção **Detecção** pode ser alterada de forma muito ou pouco frequente. Sempre examine os alertas de segurança e tome ações com base nas recomendações da Central de Segurança.

## <a name="incident-response"></a>Resposta a incidentes
A Central de Segurança detecta e alerta você sobre as ameaças à medida que elas ocorrem. As organizações devem monitorar novos alertas de segurança e tomar as medidas necessárias para investigar com mais profundidade ou corrigir o ataque. Para obter mais informações sobre como funciona a detecção de ameaças da Central de Segurança, leia [Recursos de detecção da Central de Segurança do Azure](security-center-detection-capabilities.md).

Embora este artigo não tenha a intenção de ajudá-lo a criar seu próprio plano de Resposta a Incidentes, usaremos a Resposta de Segurança do Microsoft Azure no ciclo de vida da Nuvem como a base para os estágios de resposta a incidentes. Os estágios são mostrados no diagrama a seguir:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Você pode usar o [Guia de Tratamento de Incidentes de Segurança do Computador](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) do NIST (Instituto Nacional de Padrões e Tecnologia) como uma referência para ajudar a criar seu próprio plano.
> 

Você pode usar os Alertas da Central de Segurança durante os estágios a seguir:

* **Detectar**: identifica uma atividade suspeita em um ou mais recursos. 
* **Avaliar**: realiza a avaliação inicial para obter mais informações sobre a atividade suspeita.
* **Diagnosticar**: usa as etapas de correção para realizar o procedimento técnico para endereçar o problema.

Cada Alerta de segurança fornece informações que podem ser usadas para entender melhor a natureza do ataque e sugerir possíveis atenuações. Alguns alertas também fornecem links para mais informações ou para outras fontes de informações no Azure. Você pode usar as informações fornecidas para pesquisa e para começar o processo de atenuação, e também pode pesquisar dados relacionados à segurança que fiquem armazenados no espaço de trabalho.

O exemplo a seguir mostra uma atividade de RDP suspeita ocorrendo:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Como você pode ver, essa folha mostra detalhes sobre o horário do ataque, o nome do host de origem, a VM de destino e também fornece etapas de recomendação. Em algumas circunstâncias, as informações de origem do ataque podem estar vazias. Leia [Informações de Origem Ausentes nos Alertas da Central de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) para obter mais informações sobre esse tipo de comportamento.

No vídeo [Como Utilizar a Central de Segurança do Azure e o Microsoft Operations Management Suite para obter uma Resposta a Incidentes](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703), você pode ver algumas demonstrações que podem ajudar a entender como a Central de Segurança pode ser usada em cada um desses estágios.

> [!NOTE]
> Leia [Aproveitando a Central de Segurança do Azure para a Resposta a Incidentes](security-center-incident-response.md) para saber mais sobre como usar os recursos da Central de Segurança como auxílio durante o processo de Resposta a incidentes. 
> 
> 

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu a planejar a adoção da Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Gerenciando e respondendo a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : encontre postagens no blog sobre conformidade e segurança do Azure.


