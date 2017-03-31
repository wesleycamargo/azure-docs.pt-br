---
title: "Guia de planejamento e operações da Central de Segurança | Microsoft Docs"
description: "Este documento ajuda você a planejar antes de adotar a Central de Segurança do Azure e fornece considerações sobre as operações diárias."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: f984e4a2-ac97-40bf-b281-2f7f473494c4
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 71fdf71d48959856697603c765e299f8a40b089e
ms.lasthandoff: 12/08/2016


---
# <a name="azure-security-center-planning-and-operations-guide"></a>Guia de planejamento e operações da Central de Segurança do Azure
Este guia destina-se a profissionais de TI (tecnologia da informação), arquitetos de TI, analistas de segurança da informação e administradores de nuvem cujas organizações planejam usar a Central de Segurança do Azure.

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
> 

## <a name="security-roles-and-access-controls"></a>Funções de segurança e controles de acesso
Dependendo do tamanho e da estrutura de sua organização, vários indivíduos e equipes podem usar a Central de Segurança para executar tarefas variadas relacionadas à segurança. No diagrama a seguir, você tem um exemplo de pessoas fictícias e suas respectivas funções e responsabilidades de segurança:

![Funções](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-ga.png)

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
* Corrige os alertas ou trabalha com o Proprietário da Carga de Trabalho de nuvem para aplicar a correção 

A Central de Segurança usa o [RBAC (Controle de Acesso Baseado em Função)](../active-directory/role-based-access-control-configure.md) que fornece [funções internas](../active-directory/role-based-access-built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure. Quando um usuário abre a Central de Segurança, ele vê apenas as informações relacionadas aos recursos aos quais tem acesso. Isso significa que o usuário recebe a função de Leitor, Colaborador ou Proprietário para a assinatura ou grupo de recursos ao qual o recurso pertence. 

> [!NOTE]
> Um usuário precisa ser pelo menos o proprietário de uma assinatura, do grupo de recursos ou colaborador para ser capaz de ver a Central de Segurança no Azure.
> 
> 

Usando as pessoas explicadas no diagrama anterior, o seguinte RBAC seria necessário:

**Jeff (proprietário de carga de trabalho de nuvem)**

* Proprietário/Colaborador do grupo de recursos

**David (segurança de TI)**

* Proprietário/Colaborador da Assinatura

**Judy (Operações de Segurança)**

* Leitor de Assinatura para Exibir Alertas
* Proprietário/Colaborador da Assinatura Necessário para Descartar os Alertas

**Sam (Analista de Segurança)**

* Leitor de Assinatura para Exibir Alertas
* Proprietário/Colaborador da Assinatura Necessário para Corrigir ou Descartar os Alertas
* O acesso ao armazenamento pode ser necessário

Algumas informações importantes a serem consideradas:

* Somente os Colaboradores e os Proprietários da assinatura podem editar uma política de segurança
* Somente os Proprietários e os Colaboradores da assinatura e do grupo de recursos podem aplicar recomendações de segurança para um recurso

Ao planejar o controle de acesso usando o RBAC para a Central de Segurança, compreenda quem em sua organização usará a Central de Segurança. Além disso, quais tipos de tarefas irão executar, em seguida, configurar o RBAC de acordo.

> [!NOTE]
> Recomendamos que você atribua a função menos permissiva necessária para os usuários realizarem suas tarefas. Por exemplo, os usuários que precisarem apenas exibir informações sobre o estado de segurança dos recursos, mas não precisarem executar ações, por exemplo, aplicar recomendações ou editar políticas, deverão receber a função de Leitor.
> 
> 

## <a name="security-policies-and-recommendations"></a>Políticas de segurança e recomendações 
Uma política de segurança define o conjunto de controles que são recomendados para os recursos na assinatura ou grupo de recursos especificado. Na Central de Segurança, você define as políticas de acordo com os requisitos de segurança de sua empresa e com o tipo de aplicativo ou confidencialidade dos dados.

As políticas habilitadas no nível da assinatura serão propagadas automaticamente para todos os grupos de recursos na assinatura, como mostrado no diagrama a seguir:

![Políticas de Segurança](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig2-ga.png)

Conforme mostra a figura anterior, as políticas de segurança para os grupos de recursos podem ser herdadas do nível da assinatura.

Em alguns cenários nos quais você pode ter recursos em um grupo de recursos que exigem um conjunto diferente de políticas, você pode desabilitar a herança e aplicar políticas personalizadas a um Grupo de Recursos específico.

Se você precisar de políticas personalizadas em grupos de recursos específicos, você deve desabilitar a herança no grupo de recursos e alterar as políticas de segurança. Por exemplo, se você tiver algumas cargas de trabalho que não exigem a política Transparent Data Encryption do SQL, desative-a no nível da assinatura e habilite-a somente nos grupos de recursos nos quais a TDE do SQL é necessária.

Ao começar a criar políticas personalizadas para grupos de recursos diferentes, você deve planejar a implantação de sua política sabendo que, no caso de um conflito de políticas (assinatura versus grupo de recursos), a política do grupo de recursos prevalecerá.

> [!NOTE]
> Se você precisar revisar quais políticas foram alteradas, poderá usar os [Logs de Auditoria do Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2015/03/10/audit-logs-for-azure-events/). As alterações na política são sempre registradas nos Logs de auditoria do Azure.
> 
> 

### <a name="security-recommendations"></a>Recomendações de segurança
Antes de configurar as políticas de segurança, examine cada uma das [recomendações de segurança](security-center-recommendations.md)e determine se essas políticas são apropriadas para suas várias assinaturas e grupos de recursos. Também é importante entender qual ação é executada para resolver as Recomendações de Segurança.

**Proteção do Ponto de Extremidade**: se uma máquina virtual não tiver uma solução de proteção do ponto de extremidade habilitada, a Central de Segurança recomendará a instalação de uma. Se você tiver uma solução de proteção do ponto de extremidade preferida já adotada no local, será necessário decidir se você usará o mesmo antimalware para suas VMs do Azure. A Central de Segurança fornece várias opções de proteção do ponto de extremidade.  Você pode usar o Microsoft Antimalware gratuito ou escolher entre uma lista de soluções de proteção de ponto de extremidade de parceiros integrados. Para saber mais sobre como implantar o antimalware usando a Central de Segurança, leia [Instalar Proteção do Ponto de Extremidade na Central de Segurança do Azure](security-center-install-endpoint-protection.md).

**Atualizações do Sistema**: a Central de Segurança identifica as máquinas virtuais sem as atualizações de segurança ou críticas do sistema operacional para o IaaS e os serviços de nuvem (PaaS). Considere quem é responsável pela aplicação das atualizações quando necessário e como elas são aplicadas. Muitas organizações usam o WSUS, Windows Update ou outra ferramenta.

**Configurações da Linha de Base**: se as configurações do sistema operacional da máquina virtual não corresponderem às linhas de base recomendadas, uma recomendação será exibida. Examine o conjunto de linhas de base [aqui](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) e considere como as configurações do sistema operacional são aplicadas.

**Criptografia de Disco**: se você tem discos da máquina virtual que não estão criptografados, a Central de Segurança recomenda aplicar o Azure Disk Encryption. Este recurso aproveita o BitLocker para Windows e o DM-Crypt para Linux para fornecer a criptografia do volume para o SO e os discos de dados. Essa recomendação redireciona você para um [guia passo a passo](security-center-disk-encryption.md) contendo instruções sobre como executar essa criptografia.

Lembre-se de que há diversos cenários de criptografia que você precisa solucionar. Será necessário planejar os requisitos exclusivos para cada um desses cenários:

* Criptografia de novas máquinas virtuais do Azure de VHDs que você criptografou usando suas próprias chaves de criptografia
* Criptografia de novas máquinas virtuais do Azure que foram criadas da Galeria do Azure
* Criptografia de máquinas de virtuais do Azure que já estão em execução no Azure

O planejamento dos requisitos será diferente para cada um desses cenários. Veja o [documento do Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) para obter detalhes sobre cada um desses cenários.

**Firewall do Aplicativo Web**: a Central de Segurança identificará as máquinas virtuais que executam os aplicativos Web e recomendará a instalação de um WAF (Firewall do Aplicativo Web). Avalie as soluções de parceiros disponíveis para determinar qual é a melhor opção para sua organização e determinar como a solução será licenciada (os parceiros podem oferecer suporte a modelos Traga sua própria licença e/ou pré-pago). Para obter mais informações sobre como implantar um firewall do aplicativo Web em suas VMs do Azure usando a Central de Segurança, leia [Adicionar um firewall do aplicativo Web à Central de Segurança do Azure](security-center-add-web-application-firewall.md).

O **Firewall da Próxima Geração**: permite o provisionamento de um dispositivo virtual dos principais fornecedores, incluindo o Check Point e, logo depois, o Cisco e o Fortinet. Isso estende as proteções da rede para além dos Grupos de Segurança da Rede, que são internos no Azure. A Central de Segurança descobrirá as implantações para as quais o Firewall da Próxima Geração é recomendado e irá habilitá-lo para fornecer um dispositivo virtual.

**Rede Virtual**: a Central de Segurança avalia a infraestrutura e a configuração da [Rede Virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-network/) para verificar se os [Grupos de Segurança da Rede](../virtual-network/virtual-networks-nsg.md) foram aplicados e configurados corretamente com as regras do tráfego de entrada. Você deve considerar quais regras de tráfego devem ser definidas e comunicar isso às pessoas que aplicarão as recomendações relacionadas à segurança.

A Central de Segurança recomendará que você forneça os detalhes de contato da segurança para sua assinatura do Azure. Essas informações serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente têm sido acessados por uma pessoa não autorizada ou ilegal. Leia [Fornecer detalhes de contato da segurança na Central de Segurança do Azure](security-center-provide-security-contact-details.md) para obter mais informações sobre como habilitar essa recomendação.

## <a name="data-collection-and-storage"></a>Coleta de dados e armazenamento
Recomendamos que você ative a coleta de dados para cada uma de suas assinaturas porque isso garantirá que o monitoramento da segurança esteja disponível para todas as VMs. A coleta de dados é habilitada por meio do Agente de Monitoramento do Azure (ASMAgentLauncher.exe) e da extensão de Monitoramento de Segurança do Azure (ASMMonitoringAgent.exe).

A extensão de Monitoramento de Segurança do Azure verifica a existência de várias configurações de segurança relevantes e coleta os logs de segurança da máquina virtual. Esses dados são enviados para uma conta de armazenamento especificada por você. O gerenciador de verificação (ASMSoftwareScanner.exe) também será instalado na máquina virtual e será usado como um verificador de patch.

O agente de ASM (Monitoramento de Segurança do Azure) tem a seguinte linha de base de volume:

* Volume de memória de 3 MB para a maior parte, com possíveis picos de 10 MB quando um cenário for executado a cada 12 horas.  
* CPU insignificante do processo e dos scanners persistentes. 
* Utilização do disco insignificante.

O agente de ASM tem uma cadeia de processos que pode totalizar aproximadamente 30 MB de memória.  Cada instância do agente de monitoramento pode usar até 3 GB de disco. Cada instância é limitada a 20% da CPU, embora na prática isso seja muito inferior. 

Após a habilitação da coleta de dados na política de segurança, o agente e as extensões de monitoramento serão instaladas automaticamente em todas as máquinas virtuais existentes e qualquer outra nova com suporte que sejam provisionadas no Azure.  O processo do agente foi projetado para não ser invasivo e ter um impacto muito pequeno sobre o desempenho da VM.

> [!NOTE]
> Para solucionar os problemas relacionados ao Agente de Monitoramento de Segurança do Azure, leia [Guia de Solução de Problemas da Central de Segurança do Azure](security-center-troubleshooting-guide.md).
> 
> 

Se em algum momento você quiser desabilitar a Coleta de dados, desative-a na política de segurança. Para excluir os agentes de monitoramento implantados anteriormente, selecione a opção de menu Excluir Agentes.

> [!NOTE]
> Para obter uma lista das VMs com suporte, leia as [Perguntas frequentes (FAQ) da Central de Segurança do Azure](security-center-faq.md).
> 
> 

Para cada região em que você tiver máquinas virtuais em execução, você deverá escolher a conta de armazenamento na qual os dados coletados dessas máquinas virtuais serão armazenados. Se você não escolher uma conta de armazenamento para cada região, ela será criada para você. Você pode escolher o local do armazenamento por região ou armazenar todas as informações em um local central. Enquanto as políticas de segurança podem ser definidas no nível da assinatura do Azure e no nível do grupo de recursos, a região de sua conta de armazenamento pode ser selecionada apenas no nível da assinatura.

Se você estiver usando uma conta de armazenamento compartilhada entre os diferentes recursos do Azure, leia o artigo [Metas de Desempenho e Escalabilidade do Armazenamento do Azure](../storage/storage-scalability-targets.md) para obter mais informações sobre os limites e as restrições do tamanho. Se sua assinatura também tiver limites da conta de armazenamento, examine a [Assinatura do Azure e limites, cotas e restrições do serviço](../azure-subscription-service-limits.md) para entender melhor esses limites.

Os custos associados a esse armazenamento não estão incluídos no preço do serviço da Central de Segurança e serão cobrados separadamente de acordo com as [taxas regulares de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Da perspectiva do planejamento, você deve estar ciente de que a Central de Segurança adicionará dados ao armazenamento do Azure, o que pode aumentar o custo entre um a três dólares por ano.

As considerações de desempenho e escalabilidade também devem ser planejadas de acordo com o tamanho do ambiente do Azure e com os recursos que estão consumindo a sua conta de armazenamento. Examine a [Lista de Verificação de Escalabilidade e Desempenho do Armazenamento do Microsoft Azure](../storage/storage-performance-checklist.md) para obter mais informações.

> [!NOTE]
> A Microsoft está comprometida com a proteção da privacidade e da segurança dos dados. A Microsoft obedece às diretrizes rígidas de conformidade e segurança — da codificação à operação de um serviço. Para saber mais sobre manipulação de dados e privacidade, leia [Segurança de dados da Central de Segurança do Azure](security-center-data-security.md).
> 
> 

## <a name="ongoing-security-monitoring"></a>Monitoramento contínuo de segurança
Após a configuração inicial e a aplicação das recomendações da Central de Segurança, a próxima etapa será considerar os processos operacionais da Central de Segurança.

Para acessar a Central de Segurança no Portal do Azure, clique em **Procurar** e digite **Central de Segurança** no campo **Filtro**. Os modos de exibição obtidos pelo usuário estão de acordo com esses filtros aplicados.

A Central de Segurança não interferirá nos seus procedimentos normais de operação. Ela vai monitorar passivamente as implantações e fornecerá recomendações com base nas políticas de segurança habilitadas.

O painel da Central de Segurança é dividido em duas partes principais:

* Prevenção
* Detecção

Ao habilitar a coleta de dados pela primeira vez na Central de Segurança para seu ambiente atual do Azure, revise todas as recomendações, o que pode ser feito na folha **Recomendações** ou de acordo com o recurso (**Máquina Virtual**, **Rede**, **SQL** e **Aplicativo**).

Depois de endereçar todas as recomendações, a seção **Prevenção** deverá ficar verde para todos os recursos endereçados. Nesse ponto, o monitoramento contínuo fica mais fácil, pois você só executará ações com base nas alterações nos blocos de integridade e de recomendações de segurança do recurso.

A seção **Detecção** é mais reativa, pois são alertas sobre os problemas que estão acontecendo agora ou que ocorreram no passado e foram detectados pelos controles da Central de Segurança e sistemas de terceiros. O bloco Alertas de Segurança mostra gráficos de barras que representam o número de alertas de detecção de ameaças encontrados em cada dia, e sua distribuição entre as várias categorias de gravidade (baixa, média, alta). Para obter mais informações sobre os Alertas de Segurança, leia [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md).

> [!NOTE]
> Você também pode aproveitar o Microsoft Power BI para visualizar os dados da Central de Segurança. Leia [Obter informações nos dados da Central de Segurança do Azure com o Power BI](security-center-powerbi.md).
> 
> 

### <a name="monitoring-for-new-or-changed-resources"></a>Monitoramento de recursos novos ou alterados
A maioria dos ambientes do Azure são dinâmicos, com novos recursos gerados e excluídos regularmente, com configurações ou alterações etc. A Central de Segurança ajuda a garantir que você tenha visibilidade sobre o estado de segurança desses novos recursos.

Quando você adiciona novos recursos (VMs, Bancos de Dados SQL) ao seu Ambiente do Azure, a Central de Segurança descobre automaticamente esses recursos e começa a monitorar a segurança. Isso também inclui as funções Web do PaaS e as funções de trabalho. Se a Coleta de Dados estiver habilitada na [Política de Segurança](security-center-policies.md), os recursos de monitoramento adicionais serão habilitados automaticamente para as máquinas virtuais.

![Principais áreas](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-ga.png)

1. Para as máquinas virtuais, acesse o bloco **Integridade de segurança dos recursos** e clique em **Máquinas Virtuais**. Quaisquer problemas com a habilitação da coleta de dados ou recomendações relacionadas ocorrerão na seção **Recomendações de Monitoramento** .
2. Veja as **Recomendações** para ver quais riscos de segurança, se houver, foram identificados para o novo recurso.
3. É muito comum que, quando novas VMs são adicionadas ao seu ambiente, apenas o sistema operacional seja instalado inicialmente. Talvez o proprietário do recurso precise de algum tempo para implantar outros aplicativos que serão usados por essas VMs.  É ideal que você saiba o objetivo final dessa carga de trabalho. Ele será um Servidor de Aplicativos? Com base no que essa nova carga de trabalho será, você pode habilitar a **Política de Segurança**apropriada, que é a terceira etapa neste fluxo de trabalho.
4. À medida que novos recursos são adicionados ao seu ambiente do Azure, é possível que novos alertas apareçam no bloco **Alertas de Segurança** . Sempre verifique se há novos alertas nesse bloco e tome ações de acordo com as recomendações da Central de Segurança.

Convém também monitorar regularmente o estado dos recursos existentes para identificar as alterações de configuração que criaram riscos de segurança, desvios das linhas de base recomendadas e alertas de segurança. Inicie no painel da Central de Segurança. A partir daí, há três áreas principais a serem analisadas de forma consistente.

![Operações](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4.png)

1. O painel **Integridade da segurança dos recursos** fornece um acesso rápido aos seus principais recursos. Use esta opção para monitorar suas Máquinas Virtuais, Redes, SQL e Aplicativos.
2. O painel **Recomendações** permite que você examine as recomendações da Central de Segurança. Durante o monitoramento contínuo, você pode achar que não há recomendações diárias, o que é normal, pois endereçou todas as recomendações na configuração inicial da Central de Segurança. Por esse motivo, talvez você não tenha novas informações nesta seção diariamente e precisará acessá-la apenas de acordo com a necessidade.
3. O painel **Detecção** pode ser alterado de forma muito ou pouco frequente. Sempre examine os alertas de segurança e tome ações com base nas recomendações da Central de Segurança.

## <a name="incident-response"></a>Resposta a incidentes
A Central de Segurança detecta e alerta você sobre as ameaças à medida que elas ocorrem. As organizações devem monitorar novos alertas de segurança e tomar as medidas necessárias para investigar com mais profundidade ou corrigir o ataque. Para obter mais informações sobre como funciona a detecção de ameaças da Central de Segurança, leia [Recursos de detecção da Central de Segurança do Azure](security-center-detection-capabilities.md).

Embora este artigo não tenha a intenção de ajudá-lo a criar seu próprio plano de Resposta a Incidentes, usaremos a Resposta de Segurança do Microsoft Azure no ciclo de vida da Nuvem como a base para os estágios de resposta a incidentes. Os estágios são mostrados no diagrama a seguir:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Você pode usar o [Guia de Tratamento de Incidentes de Segurança do Computador](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) do NIST (Instituto Nacional de Padrões e Tecnologia) como uma referência para ajudar a criar seu próprio plano.
> 
> 

Você pode usar os Alertas da Central de Segurança durante os estágios a seguir:

* **Detectar**: identifica uma atividade suspeita em um ou mais recursos. 
* **Avaliar**: realiza a avaliação inicial para obter mais informações sobre a atividade suspeita.
* **Diagnosticar**: usa as etapas de correção para realizar o procedimento técnico para endereçar o problema.

Cada Alerta de segurança fornece informações que podem ser usadas para entender melhor a natureza do ataque e sugerir possíveis atenuações. Alguns alertas também fornecem links para mais informações ou para outras fontes de informações no Azure. Você pode usar as informações fornecidas para pesquisar ainda mais e começar a atenuação.

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


