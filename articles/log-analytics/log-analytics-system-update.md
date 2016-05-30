<properties
	pageTitle="Solução de Avaliação de Atualização do Sistema no Log Analytics | Microsoft Azure"
	description="Você pode usar a solução Atualizações do Sistema no Log Analytics para ajudá-lo a aplicar as atualizações ausentes aos servidores em sua infraestrutura."
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
	ms.date="05/06/2016"
	ms.author="banders"/>

# Solução de Avaliação de Atualização do Sistema no Log Analytics


Você pode usar a solução Atualizações do Sistema no Log Analytics para ajudá-lo a aplicar as atualizações ausentes aos servidores em sua infraestrutura. Depois que a solução for instalada, você poderá exibir as atualizações ausentes em seus servidores monitorados usando o bloco **Avaliação da Atualização do Sistema** na página **Visão Geral** no OMS.

Se forem encontradas atualizações ausentes, os detalhes serão mostrados no painel **Atualizações**. Você pode usar o painel **Atualizações** para trabalhar com as atualizações ausentes e desenvolver um plano para aplicá-las aos servidores que precisam delas.

## Instalando e configurando a solução
Use as informações a seguir para instalar e configurar a solução.

- Adicione a solução Avaliação da Atualização do Sistema ao seu espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md). Não é necessária nenhuma configuração.

## Detalhes da coleta de dados da Atualização do Sistema

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para a Avaliação da Atualização do Sistema.

| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
|---|---|---|---|---|---|---|
|Windows|![Sim](./media/log-analytics-system-update/oms-bullet-green.png)|![Sim](./media/log-analytics-system-update/oms-bullet-green.png)|![Não](./media/log-analytics-system-update/oms-bullet-red.png)| ![Não](./media/log-analytics-system-update/oms-bullet-red.png)|![Sim](./media/log-analytics-system-update/oms-bullet-green.png)| Pelo menos, 2 vezes por dia e 15 minutos após a instalação de uma atualização|


### Para trabalhar com atualizações

1. Na página **Visão Geral**, clique no bloco **Avaliação de Atualização do Sistema**. ![imagem da página Visão geral](./media/log-analytics-system-update/oms-updates01.png)
2. No painel **Atualizações**, exiba as categorias de atualização. ![imagem da página Atualizações](./media/log-analytics-system-update/oms-updates02.png)
3. Role para a direita da página para exibir a folha **Tipo de Atualizações Ausentes** e clique em **Atualizações de Segurança**. ![imagem da página Atualizações](./media/log-analytics-system-update/oms-updates03.png)
4. Na página Pesquisa, é mostrada uma lista de atualizações de segurança ausentes dos servidores na sua infraestrutura. Clique em uma ID de um artigo da Base de Dados de Conhecimento (KBID) para exibir mais informações sobre a atualização que está ausente. Neste exemplo, *KBID 3032323*. ![imagem da página Atualizações](./media/log-analytics-system-update/oms-updates04.png)
5. Seu navegador da Web abre o artigo da Base de Dados de Conhecimento que descreve a atualização. ![imagem do artigo da base de dados de conhecimento](./media/log-analytics-system-update/oms-updates05.png)
6. Usando as informações que você encontrou, crie um plano para aplicar atualizações ausentes.

## Próximas etapas

- [Pesquise nos logs](log-analytics-log-searches.md) para exibir dados detalhados da atualização do sistema.

<!---HONumber=AcomDC_0518_2016-->