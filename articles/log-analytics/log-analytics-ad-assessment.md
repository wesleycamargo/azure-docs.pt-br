<properties
	pageTitle="Otimizar seu ambiente com a solução de Avaliação do Active Directory no Log Analytics | Microsoft Azure"
	description="É possível usar a solução Avaliação do Active Directory para avaliar o risco e a integridade de seus ambientes de servidor em intervalos regulares."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Otimizar seu ambiente com a solução de Avaliação do Active Directory no Log Analytics

É possível usar a solução Avaliação do Active Directory para avaliar o risco e a integridade de seus ambientes de servidor em intervalos regulares. Este artigo o ajudará a instalar e usar a solução para que você possa tomar ações corretivas para problemas potenciais.

Esta solução fornece uma lista priorizada de recomendações específicas para sua infraestrutura de servidor implantado. As recomendações são categorizadas em quatro áreas de foco que vão ajudá-lo a entender o risco e tomar uma ação.

As recomendações baseiam-se no conhecimento e na experiência adquirida pelos engenheiros da Microsoft em milhares de visitas a clientes. Cada recomendação fornece diretrizes sobre por que um problema pode ser relevante para você e como implementar as alterações sugeridas.

Você pode escolher as áreas de foco que são mais importantes para sua organização e acompanhar seu progresso no sentido de executar um ambiente íntegro e sem riscos.

Depois de adicionar a solução e uma avaliação for concluída, as informações resumidas para áreas de foco serão mostradas no painel **Avaliação do AD** para a infraestrutura em seu ambiente. As seções a seguir descrevem como usar as informações no painel **Avaliação do AD**, no qual é possível exibir e executar as ações recomendadas para sua infraestrutura de servidor do Active Directory.

![imagem do bloco de Avaliação do SQL](./media/log-analytics-ad-assessment/ad-tile.png)

![imagem do painel de avaliação do SQL](./media/log-analytics-ad-assessment/ad-assessment.png)


## Instalando e configurando a solução
Use as informações a seguir para instalar e configurar as soluções.

- A solução de Avaliação do Active Directory requer o .NET Framework 4 instalado em cada computador que tem um agente do OMS.
- Adicione a solução de Avaliação do Active Directory ao espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md). Não é necessária nenhuma configuração.

    >[AZURE.NOTE] Depois de adicionar a solução, o arquivo AdvisorAssessment.exe é adicionado aos servidores com agentes. Os dados de configuração são lidos e, em seguida, enviados para o serviço OMS na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados.

## Detalhes da coleta de dados de avaliação

A tabela a seguir mostra os métodos de coleta de dados dos agentes, se o SCOM (Operations Manager) é necessário e com que frequência os dados são coletados por um agente.

| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
|---|---|---|---|---|---|---|
|Windows|![Sim](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![Sim](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![Não](./media/log-analytics-ad-assessment/oms-bullet-red.png)|	![Não](./media/log-analytics-ad-assessment/oms-bullet-red.png)|![Sim](./media/log-analytics-ad-assessment/oms-bullet-green.png)|	7 dias|


## Compreendendo como as recomendações são priorizadas

Cada recomendação feita recebe um valor de ponderação que identifica a importância relativa da recomendação. Somente as dez recomendações mais importantes são mostradas.

### Como os pesos são calculados

Os pesos são valores agregados com base em três fatores principais:

- A *probabilidade* de que um problema identificado cause problemas. Uma probabilidade mais alta é igual a uma pontuação geral maior para a recomendação.

- O *impacto* da questão na sua organização se ela causar um problema. Um impacto maior é igual a uma pontuação geral maior para a recomendação.

- O *esforço* necessário para implementar a recomendação. Um esforço maior é igual a uma pontuação geral menor para a recomendação.

A importância de cada recomendação é expressa como um percentual da pontuação total disponível para todas as áreas de foco. Por exemplo, se uma recomendação na área de foco de segurança e conformidade tiver uma pontuação de 5%, implementar essa recomendação aumentará sua pontuação geral de segurança e conformidade em 5%.

### Áreas de foco

**Segurança e conformidade** - Essa área de foco mostra recomendações para possíveis ameaças de segurança e violações, políticas corporativas, bem como os requisitos de conformidade técnica, legal e regulatória.

**Disponibilidade e continuidade dos negócios** - Essa área de foco mostra as recomendações para a disponibilidade de serviço, resiliência de sua infraestrutura e proteção dos negócios.

**Desempenho e escalabilidade** -Essa área de foco mostra recomendações para ajudar a expansão da infraestrutura de TI de sua organização, garantir que seu ambiente de TI atende aos requisitos de desempenho atuais e estar apta a responder às necessidades de infraestrutura em constante mudança.

**Atualização, implantação e migração** - Essa área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implantar o Active Directory em sua infraestrutura existente.

### Você deve visar à pontuação de 100% em cada área de foco?

Não necessariamente. As recomendações baseiam-se no conhecimento e nas experiências adquiridas pelos engenheiros da Microsoft em milhares de visitas a clientes. No entanto, nenhuma infraestrutura de servidor é igual à outra, assim, recomendações específicas podem ser mais ou menos relevantes para você. Por exemplo, algumas recomendações de segurança poderão ser menos relevantes se as máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem relatórios e coleta de dados ad hoc de baixa prioridade. Problemas importantes para empresas maduras podem ser menos importantes para uma empresa start-up. Você pode preferir identificar quais áreas de foco são suas prioridades e depois examinar como suas pontuações mudam ao longo do tempo.

Cada recomendação inclui diretrizes sobre sua importância. Você deve usar essas diretrizes para avaliar se é adequado implementar a recomendação considerando a natureza de seus serviços de TI e as necessidades comerciais da sua organização.

## Use as recomendações de área de foco de avaliação

Antes de usar uma solução de avaliação no OMS, é necessário ter a solução instalada. Para ler mais sobre como instalar as soluções, veja [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md). Após a instalação, é possível exibir o resumo das recomendações usando o bloco Avaliação na página Visão geral do OMS.

Veja as avaliações de conformidade resumidas para sua infraestrutura e faça uma busca detalhada das recomendações.


### Para exibir as recomendações para uma área de foco e tomar uma ação corretiva

1. Na página **Visão Geral**, clique no bloco **Avaliação** para sua infraestrutura de servidor.
2. Na página **Avaliação**, revise as informações resumidas em uma das folhas da área de foco e clique em uma para exibir as recomendações para a área de foco.
3. Em qualquer uma das páginas da área de foco, você pode exibir as recomendações priorizadas para seu ambiente. Clique em uma recomendação sob **Objetos Afetados** para exibir detalhes sobre o motivo pelo qual a recomendação foi feita.![imagem das recomendações da Avaliação](./media/log-analytics-ad-assessment/ad-focus.png)
4. É possível executar as ações corretivas sugeridas em **Ações Sugeridas**. Quando o item tiver sido resolvido, avaliações posteriores gravarão que essas ações recomendadas foram executadas e sua pontuação de conformidade aumentará. Os itens corrigido aparecem como **Objetos Passados**.

## Ignorar as recomendações

Se houver recomendações que deseja ignorar, você poderá criar um arquivo de texto que será usado pelo OMS para impedir que as recomendações sejam exibidas nos resultados da avaliação.

### Para identificar as recomendações que serão ignoradas

1.	Entre em seu espaço de trabalho e abra a Pesquisa de Log. Use a consulta a seguir para listar as recomendações que falharam para os computadores em seu ambiente.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Esta é uma captura de tela mostrando a consulta da Pesquisa de Log: ![recomendações com falha](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

2.	Escolha as recomendações que você deseja ignorar. Você usará os valores para RecommendationId no próximo procedimento.


### Criar e usar um arquivo de texto IgnoreRecommendations.txt

1.	Crie um arquivo chamado IgnoreRecommendations.txt.
2.	Cole ou digite a RecommendationId de cada recomendação que você deseja que o Log Analytics ignore em uma linha separada e, em seguida, salve e feche o arquivo.
3.	Coloque o arquivo na pasta a seguir em cada computador no qual você deseja que o OMS ignore as recomendações.
    - Em computadores com o Microsoft Monitoring Agent (conectado diretamente ou por meio do Operations Manager) - *SystemDrive*: \\Arquivos de Programas\\Microsoft Monitoring Agent\\Agent
    - No servidor de gerenciamento do Operations Manager - *SystemDrive*: \\Arquivos de Programa\\Microsoft System Center 2012 R2\\Operations Manager\\Server

### Para verificar se as recomendações são ignoradas

Após a execução da próxima avaliação agendada, por padrão a cada 7 dias, as recomendações especificadas são marcadas como *Ignoradas* e não aparecerão no painel de avaliação.

1. É possível usar as consultas da Pesquisa de Log a seguir para listar todas as recomendações ignoradas.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

2.	Se você decidir posteriormente que deseja ver as recomendações ignoradas, remova todos os arquivos IgnoreRecommendations.txt ou remova as RecommendationIDs deles.

## Perguntas frequentes sobre soluções da Avaliação do AD

*Com que frequência uma avaliação é executada?*
- A avaliação é executada a cada sete dias.

*Há uma maneira de configurar a frequência com que a avaliação é executada?*
- Não no momento.

*Se outro servidor for descoberto depois que eu adicionar uma solução de avaliação, ele será avaliado?*
- Sim, assim que for descoberto, ele é avaliado a partir de então a cada sete dias.

*Se um servidor for encerrado, quando ele será removido da avaliação?*
- Se um servidor não enviar dados por três semanas, ele será removido.

*Qual é o nome do processo que faz a coleta de dados?*
- AdvisorAssessment.exe

*Quanto tempo leva para os dados serem coletados?*
- A coleta de dados real no servidor leva aproximadamente 1 hora. Pode levar mais tempo em servidores que têm um grande número de servidores do Active Directory.

*Que tipo de dados é coletado?*
- Os seguintes tipos de dados são coletados:
    - WMI
    - Registro
    - Contadores de desempenho

*Há uma maneira de configurar quando os dados são coletados?*
- Não no momento.

*Por que apenas as 10 principais recomendações são exibidas?*
- Em vez de apresentar uma lista exaustiva de tarefas, é recomendável que você se concentre em tratar as recomendações priorizadas primeiro. Depois de tratar dessas recomendações, recomendações adicionais serão disponibilizadas. Se você preferir ver a lista detalhada, poderá ver todas as recomendações usando a Pesquisa de Log.

*É possível ignorar uma recomendação?*
- Sim, veja a seção [Ignorar recomendações](#ignore-recommendations) acima.


## Próximas etapas

- Use [Pesquisas de log no Log Analytics](log-analytics-log-searches.md) para exibir dados detalhados e recomendações da Avaliação do AD.

<!---HONumber=AcomDC_0504_2016-->