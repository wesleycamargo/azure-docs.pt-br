<properties
   pageTitle="Gerenciar servidores e dados de uso"
   description="Saiba mais sobre a quantidade de dados enviada para o serviço Insights Operacionais dos seus servidores"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2015"
   ms.author="banders" />

# Gerenciar servidores e dados de uso

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

O Insights Operacionais coleta dados e envia ao serviço do Insights Operacionais periodicamente. Você pode usar o painel **Uso** para exibir quantos dados estão sendo enviados para o serviço do Insights Operacionais. O painel **Uso** também mostra quantos dados estão sendo enviados diariamente por pacotes de inteligência e com que frequência os grupos de gerenciamento estão enviando dados.

>[AZURE.NOTE]Se você tiver uma conta gratuita, está limitado ao envio de 500 MB de dados para o serviço de informações operacionais diariamente. Se você atingir o limite diário, a análise de dados para e é retomada no início do dia seguinte.

Você pode exibir seu uso usando o bloco **Servidores e Uso** no painel **Visão Geral** no Insights Operacionais.

![imagem do bloco Servidores e Uso](./media/operational-insights-usage/overview-servers-usage.png)

Se tiver excedido o limite de uso diário ou se estiver próximo do limite, você também pode remover uma solução para reduzir a quantidade de dados enviados para o serviço do Insights Operacionais. Para obter mais informações sobre a remoção de soluções, consulte [Usar a Galeria de Soluções para adicionar ou remover soluções](operational-insights-add-solution.md).

Se um grupo de gerenciamento do Operations Manager tiver problemas para enviar dados para o Insights Operacionais, você pode solucionar o problema ou remover o grupo do Insights Operacionais, se necessário.

![imagem do painel Uso](./media/operational-insights-usage/usage-dash.png)

O painel **Uso** exibe as seguintes informações:

- Uso médio por dia

- Uso de dados para cada solução para hoje

- Com que frequência os servidores em cada grupo de gerenciamento estão enviando dados para o serviço do Insights Operacionais

## Para trabalhar com dados de uso

1. Na página **Visão Geral**, clique no bloco **Servidores e Uso**.

2. No painel **Uso**, exiba as categorias de uso que mostram áreas com as quais você está preocupado.

3. Se houver dados sobre uma solução que esteja desnecessariamente usando muito da cota alocada, convém remover essa solução.

## Para solucionar problemas ou remover grupos de gerenciamento

1. Na página **Visão Geral**, clique no bloco **Servidores e Uso**.

2. No painel **Uso**, exiba informações sobre os grupos de gerenciamento que não estão enviando dados.

3. Se houver um grupo de gerenciamento que não esteja enviando dados, clique em **Solucionar Problemas** para obter informações detalhadas para a solução de problemas. Se não desejar manter um grupo de gerenciamento e todos os agentes que se reportam a ele, clique em **Remover**.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=July15_HO4-->