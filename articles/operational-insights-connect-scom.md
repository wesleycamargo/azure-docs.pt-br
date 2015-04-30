<properties 
   pageTitle="Conectar-se a Insights Operacionais do System Center Operations Manager" 
   description="Saiba mais sobre como se conectar a Insights Operacionais por meio do Operations Manager." 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# Conectar-se a Insights Operacionais do System Center Operations Manager 

Você pode se conectar ao Insights operacional em um ambiente existente do System Center Operations Manager. Isso permitirá usar os agentes do Operations Manager existentes como os agentes doe Insights Operacionais.

 >[AZURE.NOTE] Suporte para Insights Operacionais está disponível no Operations Manager 2012 SP1 UR6 e UR2 e Operations Manager 2012 R2. Foi adicionado suporte a proxy no SCOM 2012 SP1 UR7 e no SCOM 2012 R2 UR3.

## Conectar o SCOM a Insights Operacionais e adicionar agentes

1. No console do Operations Manager, clique em **Administração**.

2. Expanda o **Insights Operacionais** nó e clique em **Conexão do Insights Operacionais**.

3. Clique no link **Registrar-se em Insights Operacionais** e siga as instruções na tela. 

4. Depois de concluir o assistente de registro, clique em **Adicionar um computador/grupo**.

5. Na caixa de diálogo **Pesquisa de Computador**, você pode pesquisar computadores ou grupos monitorados pelo Operations Manager. Selecione os computadores ou grupos para carregar no Insights Operacionais, clique em **Adicionar** e em **OK**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

[Definir as configurações de Proxy e Firewall (opcional)](https://msdn.microsoft.com/library/azure/dn884643.aspx)

<!--HONumber=52-->