<properties
   pageTitle="Atualizar servidores com atualizações do sistema"
   description="Saiba como você pode usar a solução Atualizações do Sistema no Insights Operacionais do Microsoft Azure para ajudá-lo a aplicar as atualizações ausentes a servidores na sua infraestrutura"
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

# Atualizar servidores com atualizações do sistema

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Você pode usar a solução Atualizações do Sistema no Insights Operacionais do Microsoft Azure para ajudá-lo a aplicar as atualizações ausentes a servidores na sua infraestrutura. Instale a solução para atualizar o agente do Operations Manager e o módulo de configuração básica do Insights Operacionais. Informações de atualização são lidas nos servidores monitorados e os dados de atualização são enviados para o serviço Insights Operacionais na nuvem para processamento. Lógica é aplicada aos dados de atualização e o serviço de nuvem registra os dados. Se forem encontradas atualizações ausentes, elas serão mostradas no painel **Atualizações**. Você pode usar o painel **Atualizações** para trabalhar com as atualizações ausentes e desenvolver um plano para aplicá-las aos servidores que precisam delas.

## Use as atualizações do sistema para atualizar servidores

Para poder usar as atualizações do sistema no Insights Operacionais do Microsoft Azure, você deve ter a solução instalada. Para saber mais sobre a instalação de soluções, consulte [Usar a Galeria de Soluções para adicionar ou remover soluções](operational-insights-add-solution.md). Após a instalação, você pode exibir as atualizações que estão faltando nos seus servidores monitorados usando o bloco **Avaliação de Atualização do Sistema** na página **Visão Geral** no Insights Operacionais.

### Para trabalhar com atualizações

1. Na página **Visão Geral**, clique no bloco **Avaliação de Atualização do Sistema**. ![imagem da página Visão geral](./media/operational-insights-updates/updates01.png)
2. No painel **Atualizações**, exiba as categorias de atualização. ![imagem da página Atualizações](./media/operational-insights-updates/updates02.png)
3. Role para a direita da página para exibir a folha **Tipo de Atualizações Ausentes** e clique em **Atualizações de Segurança**. ![imagem da página Atualizações](./media/operational-insights-updates/updates03.png)
4. Na página Pesquisa, é mostrada uma lista de atualizações de segurança ausentes dos servidores na sua infraestrutura. Clique em uma ID de um artigo da Base de Dados de Conhecimento (KBID) para exibir mais informações sobre a atualização que está ausente. Neste exemplo, *KBID 3032323*. ![imagem da página Atualizações](./media/operational-insights-updates/updates04.png)
5. Seu navegador da Web abre o artigo da Base de Dados de Conhecimento que descreve a atualização. ![imagem da página Atualizações](./media/operational-insights-updates/updates05.png)
6. Usando as informações que você encontrou, crie um plano para aplicar atualizações ausentes.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=July15_HO4-->