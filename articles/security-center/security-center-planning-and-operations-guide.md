<properties
   pageTitle="Guia de planejamento e operações da Central de Segurança | Microsoft Azure"
   description="Este documento ajuda você a planejar antes de adotar a Central de Segurança do Azure e fornece considerações sobre as operações diárias."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/12/2016"
   ms.author="yurid"/>

# Guia de planejamento e operações da Central de Segurança do Azure
Este guia destina-se a profissionais de TI (tecnologia da informação), arquitetos de TI, analistas de segurança da informação e administradores de nuvem cujas organizações planejam usar a Central de Segurança do Azure.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure.

## Visão geral a Central de Segurança do Azure
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

Leia as [Perguntas frequentes (FAQ) da Central de Segurança do Azure](security-center-faq.md) para obter uma lista das perguntas comuns que também podem ser úteis durante a fase de design e planejamento.

## Guia de planejamento
Este guia aborda um conjunto de etapas e tarefas que você pode seguir para otimizar seu uso da Central de Segurança do Azure com base no modelo de gerenciamento de nuvem e nos requisitos de segurança de sua organização. Para tirar total proveito da Central de Segurança do Azure, é importante entender como pessoas ou equipes diferentes em sua organização usarão o serviço para atender às necessidades de desenvolvimento e operações seguras, monitoramento, administração resposta a incidentes. As principais áreas a serem consideradas ao planejar usar a Central de Segurança do Azure são:

- Funções de segurança e controles de acesso
- Políticas de segurança e recomendações
- Coleta de dados e armazenamento
- Monitoramento contínuo de segurança
- Resposta a incidentes

Na próxima seção, você aprenderá a planejar para cada uma dessas áreas e a aplicar essas recomendações de acordo com suas necessidades.

## Funções de segurança e controles de acesso

Dependendo do tamanho e da estrutura de sua organização, vários indivíduos e equipes podem usar a Central de Segurança para executar tarefas variadas relacionadas à segurança. Veja abaixo um exemplo de personas fictícias e suas respectivas funções e responsabilidades de segurança:

![Funções](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01.png)

A Central de Segurança do Azure permite que essas pessoas atendam a essas várias responsabilidades. Por exemplo:

**Jeff (proprietário de carga de trabalho de nuvem)**

- Exibe e completa recomendações da Central de Segurança no Portal do Azure
- Também pode usar um sistema de tíquetes para controlar alterações (preenche as recomendações usando a API)

**Rex (CISO/CIO)**

- Exibe relatórios da Central de Segurança no Power BI ou Excel

**David (segurança de TI)**

- Define a política de segurança e exibe a integridade da segurança no Portal do Azure
- Analisa os dados e gera relatórios no Power BI

**Sam (operações de segurança)**

- Exibe e realiza triagens de alertas da Central de Segurança no Portal do Azure
- Pode usar um painel existente (preenche os alertas usando a API)

**Sherlock (analista de segurança)**

- Exibe alertas da Central de Segurança no Portal do Azure
- Pode usar um painel existente (preenche os alertas usando a API)
- Analisa as tendências de alertas no Power BI
- Analisa os logs de eventos do armazenamento

A Central de Segurança do Azure usa [RBAC (Controle de Acesso baseado em função do Azure)](../active-directory/role-based-access-control-configure.md) que fornece [funções internas](../active-directory/role-based-access-built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure. Quando um usuário abre a Central de Segurança do Azure, ele vê apenas informações relacionadas a recursos aos quais ele tem acesso, ou seja, o usuário recebe a função de Proprietário, Colaborador ou Leitor para a assinatura ou grupo de recursos ao qual um recurso pertence. Usando os personas acima, a seguinte RBAC seria necessária:

**Jeff (proprietário de carga de trabalho de nuvem)**

- Proprietário/Colaborador do grupo de recursos

**David (segurança de TI)**

- Proprietário/Colaborador da assinatura

**Sam (operações de segurança)**

- Leitor de assinatura para exibição de alertas
- Proprietário/Colaborador da assinatura necessário para dispensar alertas

**Sherlock (analista de segurança)**

- Leitor de assinatura para exibição de alertas
- Proprietário/Colaborador da assinatura necessário para corrigir ou dispensar alertas
- O acesso ao armazenamento pode ser necessário

Algumas informações importantes a serem consideradas:

- Somente os colaboradores e proprietários de assinatura podem editar uma política de segurança
- Somente proprietários e colaboradores da assinatura e do grupo de recursos podem aplicar as recomendações de segurança de um recurso

Ao planejar o controle de acesso usando a RBAC para a Central de Segurança do Azure, lembre-se de entender quem em sua organização usará a Central de Segurança do Azure e quais tipos de tarefas eles executarão. Em seguida, configure a RBAC adequadamente.

> [AZURE.NOTE] Recomendamos que você atribua a função menos permissiva necessária para os usuários realizarem suas tarefas. Por exemplo, os usuários que precisarem apenas exibir informações sobre o estado de segurança dos recursos, mas não precisarem executar ações, por exemplo, aplicar recomendações ou editar políticas, deverão receber a função de Leitor.

## Políticas de segurança e recomendações
Uma política de segurança define o conjunto de controles que são recomendados para os recursos na assinatura ou grupo de recursos especificado. Na Central de Segurança do Azure, você define as políticas de acordo com os requisitos de segurança de sua empresa e com o tipo de aplicativo ou confidencialidade dos dados.

Políticas habilitadas no nível da assinatura serão propagadas automaticamente para todos os grupos de recursos dentro da assinatura, conforme mostra o diagrama a seguir:

![Políticas de Segurança](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig2.png)

Conforme mostra a figura acima, as políticas de segurança para grupos de recursos podem ser herdadas do nível da assinatura.

Em alguns cenários nos quais você pode ter recursos em um grupo de recursos que exigem um conjunto diferente de políticas, você pode desabilitar a herança e aplicar políticas personalizadas a um Grupo de Recursos específico.

Se você precisar de políticas personalizadas em grupos de recursos específicos, você deve desabilitar a herança no grupo de recursos e alterar as políticas de segurança. Por exemplo, se você tiver algumas cargas de trabalho que não exigem a política de Transparent Data Encryption do SQL, desative a política no nível da assinatura e habilite-a somente nos grupos de recursos nos quais o TDE do SQL é necessário.

Ao começar a criar políticas personalizadas para grupos de recursos diferentes, você deve planejar a implantação de sua política sabendo que, no caso de um conflito de políticas (assinatura versus grupo de recursos), a política do grupo de recursos prevalecerá.

> [AZURE.NOTE] Se você precisar revisar as políticas que foram alteradas, use os [Azure Audit Logs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2015/03/10/audit-logs-for-azure-events/) (Logs de auditoria do Azure). As alterações na política são sempre registradas nos Logs de auditoria do Azure.

### Recomendações de segurança

Antes de configurar as políticas de segurança, examine cada uma das [recomendações de segurança](security-center-recommendations.md) e determine se são apropriadas às suas várias assinaturas e grupos de recursos. Também é importante entender qual ação será executada para resolver as Recomendações de segurança.

**Proteção de ponto de extremidade**: se uma máquina virtual não tiver uma solução de proteção de ponto de extremidade habilitada, a Central de Segurança do Azure recomendará a instalação de uma. Se você tiver uma solução de proteção de ponto de extremidade preferida já adotada localmente, será necessário decidir se você usará o mesmo antimalware para suas VMs do Azure. A Central de Segurança do Azure fornece várias opções de proteção de ponto de extremidade. Você pode usar o Microsoft Antimalware gratuito ou escolher entre uma lista de soluções de proteção de ponto de extremidade de parceiros integrados. Para saber mais sobre como implantar o antimalware usando a Central de Segurança do Azure, leia [Instalar proteção de ponto de extremidade na Central de Segurança do Azure](security-center-install-endpoint-protection.md).

**Atualizações do sistema**: a Central de Segurança do Azure identificará as máquinas virtuais sem segurança ou atualizações críticas do sistema operacional. Considere quem será responsável pela aplicação das atualizações quando necessário e como elas serão aplicadas. Muitas organizações usam o WSUS, Windows Update ou outra ferramenta.

**Configurações de linha de base**: se as configurações de sistema operacional da máquina virtual não coincidirem com as linhas de base recomendadas, uma recomendação será exibida. Examine o conjunto de linhas de base [aqui](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) e considere como as configurações do sistema operacional serão aplicadas.

**Criptografia de disco**: se houver discos de máquinas virtuais que não estão criptografados, a Central de Segurança do Azure recomendará a aplicação do Azure Disk Encryption, que aproveita o BitLocker para Windows e DM-Crypt para Linux para fornecer criptografia de volume para os sistema operacional e discos de dados. Essa recomendação redirecionará você a um [guia passo a passo](security-center-disk-encryption.md) contendo instruções sobre como executar essa criptografia.

Lembre-se de que há diversos cenários de criptografia que você precisa solucionar. Será necessário planejar os requisitos exclusivos para cada um desses cenários:

- Criptografia de novas máquinas virtuais do Azure de VHDs que você criptografou usando suas próprias chaves de criptografia
- Criptografia de novas máquinas virtuais do Azure que foram criadas da Galeria do Azure
- Criptografia de máquinas de virtuais do Azure que já estão em execução no Azure

O planejamento dos requisitos será diferente para cada um desses cenários. Confira o [white paper sobre Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) para obter detalhes sobre cada um desses cenários.

**Firewall do aplicativo Web**: a Central de Segurança do Azure identificará as máquinas virtuais que executam aplicativos Web e recomendará a instalação de um WAF (Firewall do Aplicativo Web). Avalie as soluções de parceiros disponíveis para determinar qual é a melhor opção para sua organização e determinar como a solução será licenciada (os parceiros podem oferecer suporte a modelos Traga sua própria licença e/ou pré-pago). Para saber mais sobre como implantar um firewall de aplicativo Web em suas VMs do Azure usando a Central de Segurança do Azure, leia [Adicionar um firewall de aplicativo Web na Central de Segurança do Azure](security-center-add-web-application-firewall.md).

**Rede virtual**: a Central de Segurança do Azure avaliará a infraestrutura e a configuração de sua [Rede virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-network/) para verificar se o [Grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) foi aplicado e configurado corretamente com as regras de tráfego de entrada. Você deve considerar quais regras de tráfego devem ser definidas e comunicar isso às pessoas que aplicarão as recomendações relacionadas à segurança.

## Coleta de dados e armazenamento

Recomendamos que você ative a coleta de dados para cada uma das suas assinaturas porque isso garantirá que o monitoramento de segurança esteja disponível para todas as suas VMs. A coleta de dados é habilitada por meio do Agente de Monitoramento do Azure (ASMAgentLauncher.exe) e da extensão de Monitoramento de Segurança do Azure (ASMMonitoringAgent.exe).

A extensão de Monitoramento de Segurança do Azure verifica a existência de várias configurações de segurança relevantes e coleta os logs de segurança da máquina virtual. Esses dados são enviados para uma conta de armazenamento especificada por você. O gerenciador de verificação (ASMSoftwareScanner.exe) também será instalado na máquina virtual e será usado como um verificador de patch.

Após a habilitação da coleta de dados na política de segurança, o agente e as extensões de monitoramento serão instaladas automaticamente em todas as máquinas virtuais existentes e qualquer outra nova com suporte que sejam provisionadas no Azure. O processo do agente não é invasivo e não afeta o desempenho da VM.

Se em algum momento você quiser desabilitar a Coleta de dados, desative-a na política de segurança. Para excluir os agentes de monitoramento implantados anteriormente, selecione a opção de menu Excluir Agentes.

> [AZURE.NOTE] Para obter uma lista de VMs com suporte, leia as [Perguntas frequentes (FAQ) da Central de Segurança do Azure](security-center-faq.md).

Para cada região em que você tiver máquinas virtuais em execução, você deverá escolher a conta de armazenamento na qual os dados coletados dessas máquinas virtuais serão armazenados. Se você não escolher uma conta de armazenamento para cada região, ela será criada para você. Você pode escolher o local do armazenamento por região ou armazenar todas as informações em um local central. Enquanto as políticas de segurança podem ser definidas no nível de assinatura do Azure e no nível do grupo de recursos, a região de sua conta de armazenamento pode ser selecionada apenas no nível da assinatura.

Se você estiver usando uma conta de armazenamento compartilhada entre diferentes recursos do Azure, leia o artigo [Metas de desempenho e escalabilidade do Armazenamento do Azure](../storage/storage-scalability-targets.md) para saber mais sobre limites e restrições de tamanho. Sua assinatura também tem limites de armazenamento da conta, examine [Assinatura do Azure e limites, cotas e restrições de serviço](../azure-subscription-service-limits.md) para entender melhor esses limites.

> [AZURE.NOTE] Os custos associados a esse armazenamento não estão incluídos no preço do serviço da Central de Segurança do Azure e serão cobrados separadamente de acordo com os [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

As considerações de desempenho e escalabilidade também devem ser planejadas de acordo com o tamanho do ambiente do Azure e com os recursos que estão consumindo a sua conta de armazenamento. Leia [Lista de verificação de escalabilidade e desempenho do Armazenamento do Microsoft Azure](../storage/storage-performance-checklist.md).

## Monitoramento contínuo de segurança

Após a configuração inicial e aplicação das recomendações da Central de Segurança do Azure, a próxima etapa é considerar os processos operacionais da Central de Segurança do Azure.

Para acessar a Central de Segurança do Azure no Portal do Azure, clique em **Procurar** e digite **Central de Segurança** no campo **Filtro**. Os modos de exibição obtidos pelo usuário estão de acordo com esses filtros aplicados.

A Central de Segurança do Azure não interferirá com seus procedimentos normais de operação, ela monitorará passivamente as implantações e fornecerá recomendações com base nas políticas de segurança habilitadas.

O painel da Central de Segurança do Azure é dividido em duas partes principais:

- Prevenção
- Detecção

Ao habilitar a coleta de dados pela primeira vez na Central de Segurança do Azure para seu ambiente atual do Azure, não deixe de revisar todas as recomendações, o que pode ser feito na folha **Recomendações** ou de acordo com o recurso (**Máquina Virtual**, **Rede**, **SQL** e **Aplicativo**).

Depois de abordar todas as recomendações, a seção **Prevenção** deverá ficar verde para todos os recursos resolvidos. Nesse ponto, o monitoramento contínuo fica mais fácil, pois você só executará ações com base nas alterações nos blocos de integridade e de recomendações de segurança do recurso.

A seção**Detecção** é mais reativa, pois são alertas sobre problemas que estão acontecendo agora, ou que ocorreram no passado e foram detectados por controles da Central de Segurança do Azure e por sistemas de terceiros. O bloco Alertas de Segurança mostra gráficos de barras que representam o número de alertas de detecção de ameaças encontrados em cada dia, e sua distribuição entre as várias categorias de gravidade (baixa, média, alta). Para saber mais sobre os Alertas de Segurança, leia [Gerenciamento e resposta aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md).

> [AZURE.NOTE] Você também pode aproveitar o Microsoft Power BI para visualizar os dados da Central de Segurança do Azure. Leia [Obter informações dos dados da Central de Segurança do Azure com o Power BI](security-center-powerbi.md)

### Monitoramento de recursos novos ou alterados

A maioria dos ambientes do Azure são dinâmicos, com novos recursos gerados e excluídos regularmente, com configurações ou alterações etc. A Central de Segurança do Azure ajuda a garantir que você tenha visibilidade sobre o estado de segurança desses novos recursos.

Quando você adiciona novos recursos (VMs, Bancos de Dados SQL) ao seu ambiente do Azure, a Central de Segurança do Azure descobre automaticamente esses recursos e começa a monitorar a segurança. Se a Coleta de Dados estiver habilitada na Política de segurança, os recursos de monitoramento adicionais serão habilitados automaticamente para as máquinas virtuais.

![Principais áreas](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3.png)

1.	Para máquinas virtuais, acesse o bloco **Integridade de segurança dos recursos**, clique em **Máquinas Virtuais**. Quaisquer problemas com a habilitação da coleta de dados ou recomendações relacionadas ocorrerá na seção **Recomendações de monitoramento**.
2.	Veja as **Recomendações** e saiba quais riscos de segurança, se houver algum, foram identificados para o novo recurso.
3.	É muito comum que, quando novas VMs são adicionadas ao seu ambiente, apenas o sistema operacional seja instalado inicialmente. Talvez o proprietário do recurso precise de algum tempo para implantar outros aplicativos que serão usados por essas VMs. É ideal que você saiba o objetivo final dessa carga de trabalho. Ele será um Servidor de Aplicativos? Com base no que essa nova carga de trabalho será, você pode habilitar a **Política de segurança** apropriada, o que é a terceira etapa neste fluxo de trabalho.
4.	À medida que novos recursos são adicionados ao seu ambiente do Azure, é possível que novos alertas sejam exibidos no bloco **Alertas de Segurança**. Sempre verifique se há novos alertas nesse bloco e execute ações de acordo com as recomendações da Central de Segurança do Azure.

Convém também monitorar regularmente o estado dos recursos existentes para identificar as alterações de configuração que criaram riscos de segurança, desvios das linhas de base recomendadas e alertas de segurança. Inicie no painel da Central de Segurança do Azure. A partir daí, há três áreas principais a serem analisadas de forma consistente.

![Operações](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4.png)

1.	O painel **Integridade da segurança dos recursos** fornece acesso rápido aos seus principais recursos. Use esta opção para monitorar suas Máquinas Virtuais, Redes, SQL e Aplicativos.
2.	O painel **Recomendações** permite que você analise as recomendações da Central de Segurança do Azure. Durante o monitoramento contínuo, talvez não existam recomendações diárias, o que é normal, já que você atendeu a todas as recomendações na configuração inicial da Central de Segurança do Azure. Por esse motivo, talvez você não tenha novas informações nesta seção diariamente e precisará acessá-la apenas de acordo com a necessidade.
3.	O painel **Detecção** pode ser alterado de forma muito frequente ou pouco frequente. Sempre examine os alertas de segurança e execute ações com base nas recomendações da Central de Segurança do Azure.

## Resposta a incidentes

A Central de Segurança do Azure detecta e alerta você sobre ameaças à medida que elas ocorrem. As organizações devem monitorar novos alertas de segurança e tomar as medidas necessárias para investigar com mais profundidade ou corrigir o ataque.

> [AZURE.NOTE] Este artigo não tem a intenção de ajudar você a criar seu próprio plano de Resposta a incidentes. Você pode usar o [Computer Security Incident Handling Guide](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Guia de tratamento de incidentes de segurança do computador) do NIST (Instituto Nacional de Padrões e Tecnologia) como uma referência para ajudar você a criar seu próprio plano.

Cada Alerta de segurança fornece informações que podem ser usadas para entender melhor a natureza do ataque e sugerir possíveis atenuações. Alguns alertas também fornecem links para mais informações ou para outras fontes de informações no Azure. Você pode usar as informações fornecidas como base para mais pesquisas e para começar a atenuação.

O exemplo a seguir mostra uma atividade de RDP suspeita ocorrendo:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5.png)

Como você pode ver, essa folha mostra detalhes sobre o horário do ataque, o nome do host de origem, a VM de destino e também fornece etapas de recomendação. Em algumas circunstâncias, as informações de origem do ataque podem estar vazias. Leia [Missing Source Information in Azure Security Center Alerts](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) (Falta informações de origem nos alertas da Central de Segurança do Azure) para saber mais sobre esse tipo de comportamento.

| **Nível** | **Detecção de ataque** | **Proposta de atenuação** |
|------------------|------------------------------------------|-----------------------------------------------------------------------------------------------|
| Rede | Detecção de força bruta de RDP | Reduzir a superfície de ataque removendo um ponto de extremidade aberto desnecessário, configurar ACLs, usar uma senha forte |
| Máquinas virtuais | SVCHOST executando a partir do diretório errado | Mover a VM para uma sub-rede diferente (isolada), examiná-la e recriá-la, |
| Aplicativo | Injeção de SQL no Banco de Dados do Azure | Tornar mais rígidas as configurações do banco de dados |



## Próximas etapas
Neste documento, você aprendeu a configurar políticas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure

<!---HONumber=AcomDC_0713_2016-->