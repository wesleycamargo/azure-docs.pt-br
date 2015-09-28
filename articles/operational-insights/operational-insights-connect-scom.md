<properties
   pageTitle="Conectar-se a Insights Operacionais do System Center Operations Manager"
   description="Saiba mais sobre como se conectar a Insights Operacionais por meio do Operations Manager."
   services="operational-insights"
   documentationCenter=""
   authors="lauracr"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/10/2015"
   ms.author="lauracr"/>

# Conectar-se a Insights Operacionais do System Center Operations Manager


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Você pode se conectar ao Insights operacional em um ambiente existente do System Center Operations Manager. Isso permitirá que você use agentes do Operations Manager existentes para coleta de dados. Para obter informações adicionais sobre como usar o Operations Manager com Insights Operacionais, consulte [Considerações sobre o Operations Manager com Insights Operacionais](operational-insights-operations-manager.md).

Se você usar o Operations Manager para monitorar qualquer uma das seguintes cargas de trabalho, precisará definir as contas de executar como do Operations Manager para elas. Consulte [Considerações sobre o Operations Manager com Insights Operacionais](operational-insights-operations-manager.md) para obter informações sobre como definir contas Executar Como.

- Avaliação do SQL
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE]Suporte para Insights Operacionais está disponível no Operations Manager 2012 SP1 UR6 e UR2 e Operations Manager 2012 R2. Foi adicionado suporte de proxy no System Center Operations Manager 2012 SP1 UR7 e System Center Operations Manager 2012 R2 UR3.

## Para conectar o Operations Manager a Insights Operacionais e adicionar agentes

1. No console do Operations Manager, clique em **administração**.

2. Expanda o nó **Insights operacionais** e clique em **Conexão de Insights Operacionais**.

3. Clique no link **Registrar-se em Insights Operacionais** e siga as instruções na tela.

4. Depois de concluir o assistente de registro, clique em **Adicionar um computador/grupo**. ![Operations Manager: adicionar um computador/grupo](./media/operational-insights-connect-scom/om01.png)
5. Na caixa de diálogo **Pesquisa de Computador**, você pode pesquisar computadores ou grupos monitorados pelo Operations Manager. Selecione os computadores ou grupos para carregar no Insights Operacionais, clique em **Adicionar** e em **OK**. ![Operations Manager: adicionar computadores](./media/operational-insights-connect-scom/om02.png)
## Próximas etapas

[Definir as configurações de Proxy e Firewall (opcional)](operational-insights-proxy-firewall.md)

<!---HONumber=Sept15_HO3-->