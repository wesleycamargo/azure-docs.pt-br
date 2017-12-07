---
title: "Desenvolvimento do Agile de projetos de ciência de dados - Azure Machine Learning | Microsoft Docs"
description: "Como os desenvolvedores podem executar um projeto de ciência de dados de modo sistemático, com controle de versão e colaborativo em uma equipe de projeto usando o Processo de Ciência de Dados da Equipe."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: 686f751b241d49d116948711c683f4b504d5d5f9
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="agile-development-of-data-science-projects"></a>Desenvolvimento do Agile de projetos de ciência de dados

Este documento descreve como os desenvolvedores podem executar um projeto de ciência de dados de modo sistemático, com controle de versão e colaborativo em uma equipe de projeto usando o TDSP [(Processo de Ciência de Dados da Equipe)](overview.md). O TDSP é uma estrutura desenvolvida pela Microsoft que fornece uma sequência estruturada de atividades para executar soluções de análise de previsão baseadas em nuvem de forma eficaz. Para obter uma descrição das funções pessoais e das tarefas associadas que são tratadas por uma equipe de ciência de dados com padronização nesse processo, consulte [Tarefas e funções do Processo de Ciência de Dados da Equipe](roles-tasks.md). 

Este artigo inclui instruções sobre como: 

1. fazer **planejamento de sprint** para itens de trabalho envolvidos em um projeto.<br> Se não estiver familiarizado com o planejamento de sprint, você poderá encontrar detalhes e informações gerais [aqui](https://en.wikipedia.org/wiki/Sprint_(software_development) "aqui"). 
2. **adicionar itens de trabalho** em sprints. 

> [!NOTE]
> As etapas necessárias para configurar um ambiente de equipe de TDSP usando o VSTS (Visual Studio Team Services) são descritas no conjunto de instruções a seguir. Elas especificam como executar essas tarefas com VSTS, pois é como implementar o TDSP na Microsoft.  Se você escolher usar VSTS, os itens (3) e (4) na lista anterior são os benefícios que você obtém naturalmente. Se outra plataforma de hospedagem de código for usada para o seu grupo, as tarefas que o líder da equipe precisará concluir provavelmente não serão alteradas. Mas a maneira de concluir essas tarefas será diferente. Por exemplo, o item na seção seis, **Vincular um item de trabalho com uma Git branch**, talvez não seja tão fácil quanto no VSTS.
>
>

A figura a seguir ilustra um planejamento de sprint típico, codificação e fluxo de trabalho de controle do código-fonte envolvidos na implementação de um projeto de ciência de dados:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologia 

Na estrutura de planejamento de sprint do TDSP, há quatro tipos usados com frequência de **itens de trabalho**: **Recurso**, **História de usuário**, **Tarefa** e **Bug**. Cada projeto de equipe mantém uma lista de pendências única para todos os itens de trabalho. Não há nenhuma lista de pendências no nível do repositório Git em um projeto de equipe. Aqui estão as definições:

- **Recurso**: um recurso corresponde a uma interação de projeto. Interações diferentes com um cliente são consideradas recursos diferentes. Da mesma forma, é melhor considerar diferentes fases de um projeto com um cliente como recursos diferentes. Se escolher um esquema como ***ClientName-EngagementName*** para nomear os recursos, então você poderá reconhecer facilmente o contexto do projeto/interação nos próprios nomes.
- **História**: histórias são itens de trabalho diferentes que são necessárias para concluir um recurso (projeto) de ponta a ponta. Exemplos de histórias incluem:
    - Obtenção de dados 
    - Exploração de dados 
    - Geração de recursos
    - Criação de modelos
    - Operacionalização de modelos 
    - Retreinamento de modelos
- **Tarefa**: tarefas são itens de trabalho de documento ou código atribuído ou outras atividades que precisam ser realizadas para concluir uma história específica. Por exemplo, as tarefas na história *Obtenção de dados* podem ser:
    -  Obtenção de credenciais do SQL Server 
    -  Upload de dados para o SQL Data Warehouse. 
- **Bug**: bugs normalmente se referem às correções que são necessárias para um código existente ou um documento que são feitas ao concluir uma tarefa. Se o bug for causado por falta de estágios ou tarefas respectivamente, ele poderá ser encaminhado como sendo uma história ou uma tarefa. 

> [!NOTE]
> Conceitos são emprestados de recursos, histórias, tarefas e bugs do SCM (Gerenciamento de Código de Software) a ser usado na ciência de dados. Eles podem diferir ligeiramente das suas definições de SCM convencionais.
>
>

> [!NOTE]
> Os cientistas de dados podem achar mais fácil usar um modelo agile especificamente alinhado com os estágios do ciclo de vida de TDSP. Com isso em mente, foi criado um modelo de planejamento de sprint derivado do Agile, em que Épicos, Histórias etc. são substituídos por estágios do ciclo de vida TDSP ou subestágios. Para obter instruções sobre como criar um modelo do agile, veja [Configurar o processo de ciência de dados do agile no Visual Studio Online](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Planejamento de sprint 

O planejamento de sprint é útil para priorização de projeto e planejamento e alocação de recursos. Muitos cientistas de dados estão envolvidos com vários projetos e cada um deles pode levar meses para ser concluído. Os projetos geralmente são executados em ritmos diferentes. No servidor do VSTS, você pode facilmente criar, gerenciar e acompanhar itens de trabalho em seu projeto de equipe e realizar planejamento de sprint para garantir que seus projetos estão avançando conforme o esperado. 

Execute [este link](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) para as instruções passo a passo sobre planejamento de sprint no VSTS. 


## 3. <a name='AddFeature-3'></a>Adicionar um recurso  

Depois que o repositório do projeto for criado em um projeto de equipe, vá para a página de **Visão geral** da equipe e clique em **Gerenciar trabalho**.

![2](./media/agile-development/2-sprint-team-overview.png)

Para incluir um recurso da lista de pendências, clique em **Lista de pendências** --> **Recursos** --> **Novo**, digite no recurso **Título**(geralmente o nome do projeto) e, em seguida, clique em **Adicionar**.

![3](./media/agile-development/3-sprint-team-add-work.png)

Clique duas vezes no recurso que você acabou de criar. Preencha as descrições, atribua os membros da equipe para esse recurso e defina parâmetros de planejamento para esse recurso. 

Também é possível vincular esse recurso ao repositório do projeto. Clique em **Adicionar link** sob a seção **Desenvolvimento**. Depois de concluir a edição do recurso, clique em **Salvar e fechar** para sair.


## 4. <a name='AddStoryunderfeature-4'></a>Adicionar História no recurso 

No recurso, as histórias podem ser adicionadas para descrever as principais etapas necessárias para concluir o projeto (recurso). Para adicionar uma nova história, clique no sinal **+** à esquerda do recurso no modo de exibição de lista de pendências.  

![4](./media/agile-development/4-sprint-add-story.png)

Você pode editar os detalhes da história, como status, descrição, comentários, planejamento e prioridade na janela pop-up.

![5](./media/agile-development/5-sprint-edit-story.png)

Você pode vincular essa história a um repositório existente clicando em **+ Adicionar link** em **Desenvolvimento**. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Adicionar uma tarefa a uma história 

As tarefas são etapas detalhadas específicas que são necessárias para concluir cada história. Depois de concluir todas as tarefas de uma história, a história deve ser concluída também. 

Para adicionar uma tarefa a uma história, clique no sinal **+** ao lado do item da história, selecione **Tarefa**e, em seguida, preencha as informações detalhadas desta tarefa na janela pop-up.

![7](./media/agile-development/7-sprint-add-task.png)

Após a criação de recursos, histórias e tarefas, você pode exibi-los nos modos de exibição **Lista de pendências** ou **Painel** para controlar seu status.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a> Configurar um modelo de trabalho do TDSP Agile no Visual Studio Online

Este artigo explica como configurar um modelo de processo de ciência de dados do agile que usa os estágios de ciclo de vida de ciência de dados TDSP e rastreia itens de trabalho com o Visual Studio Online (vso). As etapas a seguir mostram o passo a passo de um exemplo de configuração do modelo de processo agile específico de ciência de dados *AgileDataScienceProcess* e mostram como criar itens de trabalho de ciência de dados com base no modelo.

### <a name="agile-data-science-process-template-setup"></a>Configuração do modelo de processo de ciência de dados Agile

1. Navegue até a home page do servidor, **Configurar** -> **Processo**.

    ![10](./media/agile-development/10-settings.png) 

2. Navegue até **Todos os processos** -> **Processos** em **Agile** e clique em **Criar processo herdado**. Em seguida, nomeie o processo como "AgileDataScienceProcess" e clique em **Criar processo**.

    ![11](./media/agile-development/11-agileds.png)

3. Na guia **AgileDataScienceProcess** -> **Tipos de item de trabalho**, desabilite os tipos de item de trabalho **Epic**, **Recurso**, **História do Usuário** e **Tarefa** em **Configurar -> Desabilitar**

    ![12](./media/agile-development/12-disable.png)

4. Navegue até a guia **AgileDataScienceProcess** -> **Níveis de lista de pendências**. Renomeie "Epics" como "Projetos TDSP" clicando em **Configurar** -> **Editar/Renomear**. Na mesma caixa de diálogo, clique em **+Novo tipo de item de trabalho** em "Projeto de Ciência de Dados" e defina o valor de **Tipo de item de trabalho padrão** como "Projeto TDSP" 

    ![13](./media/agile-development/13-rename.png)  

5. Da mesma forma, altere o nome de lista de pendências "Recursos" para "Estágios TDSP" e adicione o seguinte ao **Novo tipo de item de trabalho**:

    - Noções básicas sobre negócios
    - Aquisição de Dados
    - Modelagem
    - Implantação

6. Renomeie "História do Usuário" como "Subestágios TDSP" com o tipo de item de trabalho padrão definido como o tipo recém-criado "Subestágio TDSP".

7. Defina as "Tarefas" para o Tipo de item de trabalho "Tarefa TDSP" recém-criado 

8. Depois destas etapas, os Níveis de registro posterior devem ser assim:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Criar itens de trabalho de ciência de dados

Após a criação do modelo do processo de ciência de dados, você poderá criar e controlar seus itens de trabalho de ciência de dados que correspondem ao ciclo de vida do TDSP.

1. Quando você criar um novo projeto de equipe, selecione "Agile\AgileDataScienceProcess" como o **Processo de item de trabalho**:

    ![15](./media/agile-development/15-newproject.png)

2. Navegue até o projeto de equipe recém-criado e clique em **Trabalho** -> **Listas de pendências**.

3. Torne "Projetos TDSP" visível clicando em **Definir as configurações de equipe** e marque "Projetos TDSP"; em seguida, salve.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Agora você pode começar a criar os itens de trabalho específicos de ciência de dados.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Veja um exemplo de como os itens de trabalho de projeto de ciência de dados devem ser exibidos:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Próximas etapas

[Codificação colaborativa com Git](collaborative-coding-with-git.md) descreve como fazer o desenvolvimento de colaboração de código para projetos de ciência de dados usando o Git como a estrutura de desenvolvimento de código compartilhado e como vincular essas atividades de codificação ao trabalho planejado com o processo do agile.

Aqui estão os links adicionais para recursos em processos do agile.

- Processo do Agile [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Tipos e fluxo de trabalho de item de trabalho de processo do Agile [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Também são fornecidas instruções passo a passo que demonstram todas as etapas do processo para **cenários específicos**. Eles estão listados e vinculados a descrições em miniatura no artigo [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 
