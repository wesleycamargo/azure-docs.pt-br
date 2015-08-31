<properties
	pageTitle="Suplemento do Excel para serviços Web de aprendizado de máquina | Microsoft Azure"
	description="Como acessar serviços Web de aprendizado de máquina do Azure diretamente no Excel sem escrever nenhum código."
	services="machine-learning"
	documentationCenter=""
	authors="tedway"
	manager="paulettm"
	editor="cgronlun"
    tags=""/>

<tags
	ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="08/10/2015"
	ms.author="tedway;garye" />

# Suplemento do Excel para serviços Web de aprendizado de máquina do Azure

O Excel torna fácil chamar serviços Web diretamente, sem a necessidade de escrever nenhum código.

## Etapas para usar um serviço Web existente

1. Abra o arquivo do Excel de exemplo aqui ou na guia de serviços Web **PAINEL**.
2. Escolha um serviço Web clicando nele - "Titanic Survivor Predictor (exemplo de suplemento do Excel) [Score]" neste exemplo.

    ![Selecionar o serviço Web][01]

3. Você será levado à seção **Prever**. Selecione uma célula no Excel e clique em **Usar dados de exemplo**. Se preferir, edite os dados no Excel.

	![Seção Prever][02]

4. Realce os dados e clique no ícone de intervalo de dados de entrada.
5. Em **Saída**, insira o número da célula em que você deseja colocar a saída.
6. Clique em **Prever**.

## Etapas para adicionar um novo serviço Web

1. Publicar um serviço Web ([essa página](machine-learning-walkthrough-5-publish-web-service.md) explica como fazer isso) ou localizar um serviço Web existente.
2. Após ter um serviço Web, clique na seção **SERVIÇOS WEB** no painel à esquerda do estúdio do aprendizado de máquina e selecione o serviço Web a ser consumido.
3. Na guia **PAINEL** do serviço Web, copie a chave de API para o serviço Web.
4. No Excel, vá para a seção **Serviços Web** (se você estiver na seção **Prever**, clique na seta para voltar para ir para a lista de serviços Web).

	![Seção Serviços Web][03]

5. Clique em **Adicionar Serviço Web**.
6. Cole a chave na caixa de texto rotulada **Chave de API**.
7. Na guia **PAINEL** do serviço Web, clique no link **página de Ajuda da API**.
8. Copie o URI de solicitação em **Solicitar** e cole que na caixa de texto chamada **URL**.
9. Clique em **Adicionar**.

	![URL e chave de API][04]

10.	Para usar o serviço Web, siga as instruções acima, "Etapas para usar um serviço Web existente".

## Compartilhar sua pasta de trabalho

Se você salvar sua pasta de trabalho, as chaves de API dos serviços Web adicionados também serão salvas. Isso significa que você só deve compartilhar a pasta de trabalho com pessoas confiáveis.


[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png

<!---HONumber=August15_HO8-->