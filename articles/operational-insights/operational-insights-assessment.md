<properties
   pageTitle="Otimizar seu ambiente com pacotes de inteligência de avaliação"
   description="Usar soluções de Avaliação para avaliar o risco e a integridade de seus ambientes de servidor em intervalos regulares"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/10/2015"
   ms.author="banders" />

# Otimizar seu ambiente com pacotes de inteligência de avaliação

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Você pode usar a solução Avaliação para avaliar o risco e a integridade de seus ambientes de servidor em intervalos regulares. Eles fornecem uma lista priorizada de recomendações específicas para sua infraestrutura de servidor implantado. Recomendações categorizadas em seis áreas de foco que ajudam a compreender rapidamente o risco e a integridade da infraestrutura e a facilmente adotar medidas para diminuir riscos e melhorar a integridade.

Atualmente, as seguintes soluções de avaliação estão disponíveis:

- Active Directory

- SQL Server

As recomendações baseiam-se no conhecimento e na experiências obtidas pelos engenheiros da Microsoft com base em milhares de visitas a clientes. Cada recomendação fornece diretrizes sobre como por que cada questão pode ser relevante para você e como implementar as alterações sugeridas.

Você pode escolher as áreas de foco que são mais importantes para sua organização e acompanhar seu progresso no sentido de executar um ambiente íntegro e sem riscos.

Instale as soluções para atualizar o agente de do Operations Manager e o módulo de configuração de base para o Insights Operacionais e adicione o arquivo AdvisorAssessment.exe a servidores monitorados. Dados de configuração são lidos e, em seguida, são enviados para o serviço de Insights Operacionais na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados. Quando as avaliações são concluídas, informações resumidas para áreas de foco são mostradas no painel **Avaliação** para a infraestrutura em seu ambiente. Usando as informações no painel **Avaliação**, você pode exibir e executar as ações recomendadas para sua infraestrutura de servidor.

![imagem do bloco de Avaliação do SQL](./media/operational-insights-assessment/overview-sql-assess.png)

![imagem do painel de avaliação do SQL](./media/operational-insights-assessment/gallery-ad-01.png)


## Perguntas frequentes sobre soluções de avaliação

*Com que frequência uma avaliação é executada?*<br> A avaliação é executada a cada sete dias.

*Há uma maneira de configurar a frequência com que a avaliação é executada?*<br> Não no momento.

*Se outro servidor for descoberto após ter adicionado uma solução de avaliação, ele será avaliado?*<br> Sim, assim que for descoberto, ele é avaliado a partir de então a cada sete dias.

*Se um servidor for encerrado, quando ele será removido da avaliação?*<br> Se um servidor não enviar dados por três semanas, ele será removido.

*Qual é o nome do processo que faz a coleta de dados?*<br> AdvisorAssessment.exe

*Quanto tempo leva para os dados serem coletados?*<br> A coleta de dados real no servidor leva aproximadamente 1 hora. Pode levar mais tempo em servidores que têm um grande número de servidores do Active Directory ou instâncias ou bancos de dados SQL.

*Que tipo de dados é coletado?*<br> Os seguintes tipos de dados são coletados:

- WMI
- Registro
- Contadores de desempenho
- Exibições de gerenciamento dinâmico SQL (DMV).

*Há uma maneira de configurar quando os dados são coletados?*<br> Não no momento.

*Por que é necessário configurar uma conta Executar como?*<br> Para o SQL Server, um pequeno número de consultas SQL é executado. Para elas serem executadas, uma conta Executar como com permissões VIEW SERVER STATE devem ser usadas para SQL. Além disso, para consultar WMI, são necessárias credenciais de administrador local.

*Por que apenas as 10 principais recomendações são exibidas?*<br> Em vez de apresentar uma lista exaustiva de tarefas, é recomendável que você se concentre em tratar as recomendações priorizadas primeiro. Depois de tratar dessas recomendações, recomendações adicionais serão disponibilizadas. Se preferir ver a lista detalhada, exiba todas as recomendações usando a pesquisa de log do Insights Operacionais.

*É possível ignorar uma recomendação?*<br> Não no momento.

## Compreendendo como as recomendações são priorizadas

Cada recomendação feita recebe um valor de ponderação que identifica a importância relativa da recomendação. Somente as dez recomendações mais importantes são mostradas.

### Como os pesos são calculados

Os pesos são valores agregados com base em três fatores principais:

- A *probabilidade* de que um problema identificado cause problemas. Uma probabilidade mais alta é igual a uma pontuação geral maior para a recomendação.

- O *impacto* da questão na sua organização se ela causar um problema. Um impacto maior é igual a uma pontuação geral maior para a recomendação.

- O *esforço* necessário para implementar a recomendação. Um esforço maior é igual a uma pontuação geral menor para a recomendação.

A importância de cada recomendação é expressa como um percentual da pontuação total disponível para todas as áreas de foco. Por exemplo, se uma recomendação na área de foco de segurança e conformidade tiver uma pontuação de 5%, implementar essa recomendação aumentará sua pontuação geral de segurança e conformidade em 5%.

### Áreas de foco

**Segurança e conformidade** - proteja a reputação da sua organização protegendo-se contra ameaças de segurança e violações, impondo políticas corporativas e atendendo aos requisitos de conformidade técnica, legal e normativa.

**Disponibilidade e continuidade dos negócios** - mantenha seus serviços disponíveis e sua empresa lucrativa garantindo a resiliência da infraestrutura e tendo o nível certo de proteção de seu negócio em caso de desastre.

**Desempenho e escalabilidade** - ajude a sua organização a crescer e a inovar assegurando que seu ambiente de TI possa cumprir aos requisitos de desempenho atuais e responder rapidamente às necessidades comerciais.

**Atualização, implantação e migração** - posicione seu departamento de TI para ser o principal agente de alteração e inovação, aproveitando totalmente de novas tecnologias para liberar mais valor comercial para unidades organizacionais, os funcionários e os clientes.

**Operações e monitoramento** - reduza seu orçamento de manutenção de TI simplificando as operações de TI e implementando um programa de manutenção preventiva abrangente para maximizar o desempenho comercial.

**Gerenciamento de alterações e de configuração** - proteja as operações diárias da sua organização e certifique-se de que as alterações não afetarão negativamente a sua empresa ao estabelecer procedimentos de controle de alterações, bem como ao acompanhar e auditar as configurações do sistema.

### Você deve visar à pontuação de 100% em cada área de foco?

Não necessariamente. As recomendações baseiam-se no conhecimento e na experiências obtidas pelos engenheiros da Microsoft em milhares de visitas a clientes. No entanto, nenhuma infraestrutura de servidor é igual à outra, assim, recomendações específicas podem ser mais ou menos relevantes para você. Por exemplo, algumas recomendações de segurança podem ser menos relevantes se as máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem relatórios e coleta de dados ad hoc de baixa prioridade. Problemas importantes para empresas maduras podem ser menos importantes para uma empresa start-up. Você pode preferir identificar quais áreas de foco são suas prioridades e depois examinar como suas pontuações mudam ao longo do tempo.

Cada recomendação feita inclui diretrizes sobre por que ela pode ser importante. Você deve usar essas diretrizes para avaliar se é adequado implementar a recomendação considerando a natureza de seus serviços de TI e as necessidades comerciais da sua organização.

## Use as recomendações de área de foco de avaliação

Antes de usar uma solução de avaliação do Insights Operacionais do Microsoft Azure, você deve ter a solução instalada. Para saber mais sobre a instalação de soluções, consulte [Usar a Galeria de Soluções para adicionar ou remover soluções](operational-insights-setup-workspace.md). Após a instalação, você pode exibir o resumo das recomendações usando o bloco de avaliação da página Visão Geral do Insights Operacionais.

Você pode resumir avaliações de conformidade para sua infraestrutura e então fazer uma análise detalhada das recomendações.

![imagem de recomendações de avaliação do SQL](./media/operational-insights-assessment/gallery-ad-03.png)



### Para exibir as recomendações para uma área de foco e tomar uma ação corretiva

1. Na página **Visão Geral**, clique no bloco **Avaliação** para sua infraestrutura de servidor.

2. Na página **Avaliação**, revise as informações resumidas em uma das folhas da área de foco e clique em uma para exibir as recomendações para a área de foco.

3. Em qualquer uma das páginas da área de foco, você pode exibir as recomendações priorizadas para seu ambiente. Clique em uma recomendação para exibir seus detalhes sobre por que a recomendação foi feita. Os detalhes aparecem sob **Objetos Afetados**.

4. Execute ações corretivas sugeridas em **Ações Sugeridas**. Quando o item tiver sido resolvido, avaliações posteriores gravarão que essas ações recomendadas foram executadas e sua pontuação de conformidade aumentará. Os itens corrigido aparecem como **Objetos Passados**.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=Sept15_HO3-->