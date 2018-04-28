---
title: Coortes do uso do Azure Application Insights | Microsoft Docs
description: Analisar diferentes conjuntos ou usuários, sessões, eventos ou operações que tenham algo em comum
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/10/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: 68453499cc6477cc079a342906614e6873938fc8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="application-insights-cohorts"></a>Coortes do Application Insights

Uma coorte é um conjunto de usuários, sessões, eventos ou operações que têm algo em comum. No Azure Application Insights, as coortes são definidas por uma consulta do Analytics. Se você estiver analisando um conjunto específico de usuários ou eventos repetidamente, as coortes podem fornecer mais flexibilidade para expressar exatamente o conjunto no qual você está interessado.

![Painel de coortes](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Coortes em comparação com filtros básicos

Embora os coortes sejam usados de forma semelhante aos filtros, o fato de a definição das coortes ser criada a partir de consultas do Analytics personalizadas permite que sejam muito mais adaptáveis e complexas. Ao contrário de filtros, você pode salvar as coortes para que outros membros da equipe possam reutilizá-las.

Você pode definir um coorte de usuários que tenham todos testado um novo recurso em seu aplicativo. Com essa coorte salva em seu recurso Application Insights, a análise futura desse grupo específico de usuários está a um clique.

> [!NOTE]
> Depois de criadas, as coortes estão disponíveis nas ferramentas de Usuários, Sessões, Eventos e Fluxos de usuário.

## <a name="example-engaged-users"></a>Exemplo: usuários envolvidos

Sua equipe define usuário envolvido como qualquer pessoa que usar seu aplicativo cinco ou mais vezes em um determinado mês. Vamos definir uma coorte desses usuários envolvidos.

1. Abra a ferramenta **Coortes**.

2. Clique na guia **Galeria de modelos**. Aqui você encontrará uma coleção de modelos para várias coortes.

3. Escolha **Usuários envolvidos** – por dias usados”.

    Há três parâmetros para esse coorte:
      * **Atividades** que permitem que você escolha quais eventos e modos de exibição de página devem contar como “uso”.
      * **Período** a definição de um mês.
      * **UsedAtleastCustom** o número de vezes que precisam usar algo dentro de um período para contar como usuário envolvido.

4. Altere **UsedAtleastCustom** para “5+ dias” e deixe **Período** para o padrão de 28 dias.

    ![Imagem](.\media\app-insights-usage-cohorts\003.png)

    Agora essa coorte representa todas as IDs de usuário foram enviadas com qualquer exibição personalizada de evento ou de página em cinco dias diferentes nos últimos 28 dias.

5. Clique em **Salvar**.

   > [!TIP]
   >  Nomeie seu coorte, como “Usuários envolvidos (5+ dias)” e salve-a em “Meus relatórios” ou “Relatórios compartilhados” dependendo de se você quer que outras pessoas com acesso a esse recurso do Application Insights veja esse coorte.

6. Clique em **Voltar à galeria**.

### <a name="what-can-you-do-with-this-cohort"></a>O que você pode fazer com essa coorte?

Abra a ferramenta **Usuários** > Na lista suspensa **Mostrar** > Escolha a coorte criada em **Usuários que pertencem a...**

Agora, a ferramenta Usuários é filtrada para essa coorte de usuários:

![Painel de usuários filtrado para uma determinada coorte](.\media\app-insights-usage-cohorts\004.png)

Alguns aspectos importantes a observar:
   * Este é um conjunto que você não poderia ter criado por meio de filtros normais. A lógica de data é mais avançada.
   * Você pode filtrar essa coorte usando os filtros normais na ferramenta de Usuários. Portanto, embora a coorte esteja definida no em janelas de 28 dias, você ainda pode ajustar o intervalo de tempo na ferramenta de Usuários para que seja de 30, 60 ou 90 dias. 

Isso permite que você faça perguntas mais sofisticadas como: _para as pessoas que foram envolvidas nos últimos dias 28, como essas mesmas pessoas se comportaram nos últimos 60 dias?_ que seriam impossíveis expressar por meio do construtor de consultas.

## <a name="example-events-cohort"></a>Exemplo: coortes de eventos

Você também pode fazer coortes de eventos. Vamos definir uma coorte dos eventos e modos de exibição de página e veja como usá-los a partir de outras ferramentas. Isso pode ser útil para definir um conjunto de eventos que sua equipe considera _uso ativo_, ou para definir um conjunto de eventos relacionados a um determinado recurso novo.

1. Abra a ferramenta **Coortes**.

2. Clique na guia **Galeria de modelos**. Aqui você encontrará uma coleção de modelos para várias coortes.

3. Escolha **Seletor de eventos**.

    ![Captura de tela do Seletor de eventos](.\media\app-insights-usage-cohorts\006.png)

4. No menu suspenso **Atividades**, selecione os eventos que você quer que estejam na coorte

5. Salve a coorte e dê a ela um nome.

## <a name="example-active-users-where-you-modify-query"></a>Exemplo: os usuários ativos em que você modifica a consulta

As duas coortes anteriores foram definidas usando os menus suspensos. Mas podemos também definir coortes com consultas do Analytics para obter total flexibilidade. Vamos ver como fazer isso criando uma coorte de usuários do Reino Unido.

![Imagem animada mostrando o uso da ferramenta Coortes](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Abra a ferramenta **Coortes** > Clique na guia **Galeria de modelos** > Escolha **Coorte de usuários em branco**.

    ![Coorte de usuários em branco](.\media\app-insights-usage-cohorts\001.png)

    Há três seções:
       * Uma seção de texto Markdown onde você pode descrever a coorte mais detalhadamente para outras pessoas da sua equipe.

       * Uma seção de parâmetros que você pode usar para fazer seus próprios parâmetros, como **Atividades** e outros menus suspensos dos dois exemplos anteriores.

       * Uma seção de consulta que você usa para definir a coorte usando uma consulta do Analytics.

    Na seção de consulta, você [grava uma consulta do Analytics](https://docs.loganalytics.io/index) que seleciona o determinado conjunto de linhas que descrevem a coorte que você deseja definir. A ferramenta Coortes, em seguida, adiciona implicitamente uma cláusula “| summarize by user_Id” à consulta. Isso é visualizado abaixo da consulta em uma tabela para garantir que sua consulta esteja retornando os resultados.

    > [!NOTE]
    > Se você não vir a consulta, tente redimensionar a seção para torná-la mais alta e revelar a consulta. O .gif animado no começo desta seção ilustra o comportamento de redimensionamento.

2. Copie e cole o seguinte no editor de consultas:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Clique em **Executar Consulta**. Você deve ver as IDs de usuário aparecem na tabela. Caso contrário, altere para um país em que seu aplicativo tenha usuários.

4. Salve e nomeie a coorte.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

_Eu defini uma coorte de usuários de um determinado país. Quando eu comparo esse coorte na ferramenta de Usuários para definir apenas um filtro nesse país na ferramenta de Usuários, vejo resultados diferentes. Por quê?_

Coortes e filtros são diferentes. Suponha que você tem uma coorte de usuários do Reino Unido (definido como o exemplo acima) e compara os resultados para definir o filtro "País ou região = Reino Unido".

* A versão da coorte mostrará todos os eventos de usuários que tenham enviado pelo menos um evento do Reino Unido no intervalo de tempo atual. Se você dividir por país ou região, você provavelmente verá muitos países e regiões.
* A versão de filtros mostrará apenas os eventos do Reino Unido. Enquanto se você dividir por país ou região, você verá apenas o Reino Unido.

## <a name="learn-more"></a>Saiba mais
- [Linguagem de consulta do Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
- [Usuários, Sessões, Eventos](app-insights-usage-segmentation.md)
- [Fluxos de Usuário](app-insights-usage-flows.md)
- [Visão geral do uso](app-insights-usage-overview.md)