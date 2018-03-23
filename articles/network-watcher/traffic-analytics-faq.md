---
title: Perguntas frequentes sobre Análise de Tráfego do Azure | Microsoft Docs
description: Obtenha respostas para algumas perguntas frequentes sobre a Análise de Tráfego.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: fd97e0ca7615691c537dcb1dc18643627046742d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Perguntas frequentes sobre Análise de Tráfego

1.  Quais são os pré-requisitos para usar a Análise de Tráfego?

    Análise de Tráfego exige os pré-requisitos a seguir:

    - Uma assinatura habilitada para Observador de Rede
    - Logs de fluxo do NSG habilitados para os NSGs que deseja monitorar
    - Uma Conta de Armazenamento do Microsoft Azure para armazenar logs de arquivos brutos
    - Espaço de trabalho do Log Analytics (OMS), com acesso para gravação e leitura

2.  Em quais regiões do Azure estão disponíveis a Análise de Tráfego?

    Enquanto estiver em versão prévia, você pode usar a Análise de Tráfego para NSGs em qualquer uma das **regiões com suporte** a seguir: Centro-oeste dos EUA, Leste dos EUA, Leste dos EUA 2, Centro-Norte dos EUA, Centro-Sul dos EUA, EUA Central, Oeste dos EUA, Oeste dos EUA 2, Europa Ocidental, Europa Setentrional, Oeste do Reino Unido, Sul do Reino Unido, Leste da Austrália e Sudeste da Austrália. O espaço de trabalho do Log Analytics deve existir no Centro-oeste dos EUA, Leste dos EUA, Europa Ocidental, Sudeste da Austrália ou a região Sul do Reino Unido.

3.  Os NSGs que eu habilitei os logs de fluxo podem estar em regiões diferentes do meu Espaço de Trabalho OMS?

    sim

4.  Vários NSGs podem ser configurados dentro de um único espaço de trabalho?

    sim

5.  É possível utilizar um Espaço de Trabalho OMS existente?

    Sim, se você selecionar um espaço de trabalho existente, certifique-se de que ele foi migrado para a nova linguagem de consulta. Se você não quiser atualizar o espaço de trabalho, será necessário criar um novo. Para obter mais informações sobre a nova linguagem de consulta, consulte [Atualização do Azure Log Analytics para a nova pesquisa de logs ](../log-analytics/log-analytics-log-search-upgrade.md).

6.  A minha Conta de Armazenamento do Microsoft Azure pode estar em uma assinatura e meu Espaço de Trabalho OMS estar em uma assinatura diferente?

    sim

7.  É possível armazenar logs brutos em Conta de Armazenamento diferente, em assinatura diferente?

    Não Você pode armazenar logs brutos em qualquer conta de armazenamento onde um NSG esteja habilitado para logs de fluxo, no entanto, tanto a conta de armazenamento quanto os logs brutos devem estar na mesma assinatura e região.

8.  Se eu receber um erro "Não encontrado" ao configurar um NSG para Análise de Tráfego, como poderei resolver isso?

    Selecione uma região com suporte listada na pergunta 2. Se você selecionar uma região sem suporte, receberá um erro "Não encontrado".

9.  Nos logs de fluxo do NSG estou recebendo o status de NSG como "Falha ao Carregar", o que fazer em seguida?

    O provedor Microsoft.Insights deve estar registrado para que o log de fluxo funcione corretamente. Se você não tiver certeza se o provedor Microsoft.Insights está registrado ou não para sua assinatura, substitua *xxxxx-xxxxx-xxxxxx-xxxx* no seguinte comando e, em seguida, execute os comandos do PowerShell a seguir:

    ```powershell-interactive
    **Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
    **Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
    ```

10. Configurei a solução. Por que não visualizo nada no painel?

    O painel pode levar até 30 minutos para aparecer pela primeira vez. Primeiro, a solução deverá agregar dados suficientes para que ele obtenha informações relevantes antes que qualquer relatório seja gerado. 

11.  Se eu receber a mensagem a seguir: "Não foi possível localizar dados nesse espaço de trabalho para o intervalo de tempo selecionado. Tente alterar o intervalo de tempo ou selecione um espaço de trabalho diferente", como faço para resolver isso?

        Experimente as opções a seguir:
        - Tente alterar o intervalo de tempo na barra superior.
        - Selecione um espaço de trabalho do Log Analytics diferente na barra superior
        - Tente acessar a Análise de Tráfego após 30 minutos, se tiver sido habilitada recentemente
    
        Se os problemas persistirem, manifeste as preocupações no [Fórum Voz do Usuário](https://feedback.azure.com/forums/217313-networking?category_id=195844).

12.  Se eu receber a mensagem a seguir: "1) Analisando seus logs de fluxo NSG pela primeira vez. Esse processo pode demorar de 20 a 30 minutos para ser concluído. Verifique novamente após algum tempo. 2) Se a etapa acima não funcionar e seu espaço de trabalho estiver sob a SKU gratuita, então, verifique o uso de espaço de trabalho aqui para validar a quota, caso contrário consulte as perguntas frequentes para obter mais informações", como faço para resolver isso?

        Você pode receber o erro pelos seguintes motivos:
        - A Análise de Tráfego pode ter sido habilitada recentemente e pode estar agregando dados suficientes para que obtenha informações relevantes antes que qualquer relatório possa ser gerado. Neste caso, tente novamente após 30 minutos
        - Seu espaço de trabalho OMS está sob a SKU gratuita e violou os limites de cota. Nesse caso, será necessário utilizar um espaço de trabalho em uma SKU com capacidade maior.
    
        Se os problemas persistirem, manifeste as preocupações no [Fórum Voz do Usuário](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
13.  Se eu receber a seguinte mensagem: "Parece que há dados do recurso (Topologia) e nenhuma informação de fluxo. Enquanto isso, clique aqui para ver os dados do recurso e consulte as perguntas frequentes para obter mais informações", como faço para resolver isso?

        Você está vendo a informação de recursos no painel, no entanto, não há estatísticas relacionadas ao fluxo presentes. Os dados podem estar ausentes porque não há fluxo de comunicação entre os recursos. Aguarde 60 minutos e verifique o status novamente. Se você tiver certeza de que a comunicação flui entre os recursos, então, manifeste as preocupações no [Fórum Voz do Usuário](https://feedback.azure.com/forums/217313-networking?category_id=195844).

14.  Como a Análise de Tráfego é precificada?

        Não haverá cobrança de encargos enquanto a Análise de Tráfego estiver em Visualização pública. A geração de Logs de Fluxo do NSG e a retenção de dados em um espaço de trabalho OMS estão sujeitas a encargos nas taxas publicadas.

15.  Como posso navegar usando o Teclado na Exibição do Mapa Geográfico?

        A página do mapa geográfico contém duas seções principais:
    
        - **Faixa**: a faixa posicionada na parte superior do Mapa Geográfico fornece a capacidade de selecionar filtros de distribuição de tráfego por meio de botões como Implantação/Sem Implantação/Ativo/Inativo/Análise de Tráfego Habilitada/Análise de Tráfego não habilitada/Tráfego de países/Tráfego mal-intencionado permitido/Mal-intencionado/Benigno e informações de legenda. Na seleção dos botões definidos, o respectivo filtro é aplicado no Mapa, como se um usuário selecionasse o botão de filtro "Ativo" sob a faixa, então, o mapa destaca os datacenters "ativos" na implantação.
        - **Mapa**: a seção Mapa posicionada abaixo da faixa mostra a distribuição de tráfego entre os países de datacenters do Azure.
    
        **Navegação do teclado na Faixa**
    
        - Por padrão, a seleção na página do mapa geográfico para a faixa é o botão "Azure DCs" do filtro.
        - Para navegar até outro botão de filtros, é possível utilizar a tecla `Tab` ou `Right arrow` para mover para o próximo. Para navegação regressiva, utilize a tecla `Shift+Tab` ou a `Left arrow`. A precedência de direção da navegação progressiva é da esquerda para a direita, seguida de cima para baixo.
        - Pressione a tecla de seta `Enter` ou `Down` para aplicar o filtro selecionado. Com base na implantação e seleção de filtro, um ou vários nós na seção Mapa são destacados.
            - Para alternar entre a **Faixa** e o **Mapa**, pressione `Ctrl+F6`.
        
        **Navegação do teclado no Mapa**
    
        - Após selecionar qualquer filtro na faixa e pressionar `Ctrl+F6`, o foco move-se para um dos nós destacados (**Datacenter do Azure** ou **País/Região**) na exibição do mapa.
        - Para navegar até outros nós destacados no mapa, é possível utilizar as teclas `Tab` ou `Right arrow` para movimento progressivo e a `Shift+Tab` ou `Left arrow` para movimento regressivo.
        - Para selecionar qualquer nó destacado no mapa, utilize a tecla `Enter` ou `Down arrow`.
        - Na seleção de quaisquer desses nós, o foco move-se para a **Caixa de Ferramentas de Informações** do nó. Por padrão, o foco move-se para o botão próximo à **Caixa de Ferramentas de Informações**. Para uma navegação detalhada na exibição da **Caixa**, utilize as teclas `Right` e `Left arrow` para avançar e retroceder, respectivamente. Pressionar `Enter` tem o mesmo efeito que selecionar o botão com foco na **Caixa de Ferramentas de Informações**.
        - Pressionar `Tab` enquanto o foco está na **Caixa de Ferramentas de Informações**, o foco move-se para os pontos de extremidade no mesmo continente que o nó selecionado. É possível utilizar as teclas `Right` e `Left arrow` para navegar por esses pontos de extremidade.
        - Para navegar até outros pontos de extremidade de fluxo/cluster de continentes, utilize `Tab` para movimento progressivo e `Shift+Tab` para movimento regressivo.
        - Quando o foco estiver em `Continent clusters`, utilize as teclas de seta `Enter` ou `Down` para destacar os pontos de extremidade dentro do cluster do continente. Para navegar através dos pontos de extremidade e o botão próximo à caixa de informações do cluster do continente, utilize a tecla `Right` ou `Left arrow` para movimento progressivo e regressivo, respectivamente. Em qualquer ponto de extremidade você poderá utilizar `Shift+L` para alternar para a linha de conexão do nó selecionado para o ponto de extremidade. Pressionar `Shift+L` novamente o navega até o ponto de extremidade selecionado.
        
        Em qualquer estágio:
    
        - `ESC` recolhe a seleção expandida.
        - A tecla `UP Arrow` realiza a mesma ação que `ESC`. A tecla `Down arrow` realiza a mesma ação que `Enter`.
        - Utilize `Shift+Plus` para ampliar e `Shift+Minus` para reduzir.