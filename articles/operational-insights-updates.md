<properties 
   pageTitle="Atualizar servidores com atualizações do sistema"
   description="Saiba como usar o pacote de inteligência de atualizações do sistema no Insights Operacionais Microsoft Azure para ajudá-lo a aplicar as atualizações ausentes a servidores em sua infraestrutura"
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
   ms.date="03/20/2015"
   ms.author="banders" />

# Atualizar servidores com atualizações do sistema

Você pode usar o pacote de inteligência de atualizações do sistema no Insights Operacionais do Microsoft Azure para ajudá-lo a aplicar as atualizações ausentes a servidores na sua infraestrutura. Instale o pacote de inteligência para atualizar o agente do Operations Manager e o módulo de configuração básica do Insights Operacionais. Informações de atualização são lidas nos servidores monitorados e os dados de atualização são enviados para o serviço Insights Operacionais na nuvem para processamento. Lógica é aplicada aos dados de atualização e o serviço de nuvem registra os dados. Se forem encontradas atualizações ausentes, elas serão mostradas no painel **Atualizações**. Você pode usar o painel **Atualizações** para trabalhar com as atualizações ausentes e desenvolver um plano para aplicá-las aos servidores que precisam delas.

## Use as atualizações do sistema para atualizar servidores

Antes de usar as atualizações do sistema no Insights Operacionais do Microsoft Azure, você deve ter o pacote de inteligência instalado. Para saber mais sobre como instalar pacotes de inteligência, consulte [Usar a Galeria para adicionar ou remover pacotes de inteligência](operational-insights-add-intelligence-packs.md). Após a instalação, você pode exibir as atualizações que estão faltando nos seus servidores monitorados usando o bloco **Avaliação de Atualização do Sistema** na página **Visão Geral** no Insights Operacionais. 

![imagem do bloco Avaliação da Atualização do Sistema](./media/operational-insights-updates/overview-update.png)

O bloco abre o painel **Atualizações**, no qual você pode exibir um resumo geral das atualizações ausentes. A página detalha as seguintes categorias:

- Servidores com atualizações de segurança ausentes

- Servidores que não foram atualizados recentemente

- Atualizações que devem ser aplicadas a servidores específicos

- Tipo de atualizações que estão faltando

Você pode clicar em qualquer bloco ou item para exibir seus detalhes na página de **Pesquisa** para obter mais informações sobre a atualização ausente. 

![imagem do painel Atualizações](./media/operational-insights-updates/gallery-sysupdate-01.png)

![imagem do painel Atualizações](./media/operational-insights-updates/gallery-sysupdate-02.png)

## Resultados da pesquisa##
Os resultados da pesquisa de atualização incluem:

- Servidor

- Título da atualização

- ID da Base de Dados de Conhecimento

- A atualização de produto é para

- Gravidade da atualização

- Data da publicação

Os resultados da pesquisa *Server* incluem:

- Nome do servidor

- Nome da versão do sistema operacional

- Método para habilitar a atualização automática

- Dias desde a última atualização

- Versão do agente do Windows Update

## Para trabalhar com atualizações

1. Na página **Visão Geral**, clique no bloco **Avaliação de Atualização do Sistema**.

2. No painel **Atualizações**, exiba as categorias de atualização e escolha uma com a qual trabalhar.

3. Clique em um bloco ou qualquer item para exibir informações detalhadas sobre ele na página de **Pesquisa**.

4. Usando as informações que você encontrou, crie um plano para aplicar atualizações ausentes.


<!--HONumber=52-->