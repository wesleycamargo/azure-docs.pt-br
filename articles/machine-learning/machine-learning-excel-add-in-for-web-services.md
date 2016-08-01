<properties
	pageTitle="Suplemento do Excel para serviços Web de aprendizado de máquina | Microsoft Azure"
	description="Como usar serviços Web de Aprendizado de Máquina do Azure diretamente no Excel sem escrever nenhum código."
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
	ms.date="07/06/2016"
	ms.author="tedway;garye" />

# Suplemento do Excel para serviços Web de aprendizado de máquina do Azure

O Excel torna fácil chamar serviços Web diretamente, sem a necessidade de escrever nenhum código.

## Etapas para usar um serviço Web existente na pasta de trabalho

1. Abra o [arquivo de exemplo do Excel](http://aka.ms/amlexcel-sample-2), que contém o suplemento do Excel e os dados sobre passageiros do Titanic.
2. Escolha o serviço Web clicando nele - "Titanic Survivor Predictor (exemplo de suplemento do Excel) [Pontuação]" neste exemplo.

    ![Selecionar o serviço Web][01]

3. Você será levado à seção **Prever**. Esta pasta de trabalho já contém dados de exemplo, mas para uma pasta de trabalho em branco você pode selecionar uma célula no Excel e clicar em **Usar dados de exemplo**.
4. Selecione os dados com cabeçalhos e clique no ícone de intervalo de dados de entrada. Verifique se a caixa "Meus dados contêm cabeçalhos" está marcada.
5. Em **Saída**, insira o número da célula em que você deseja colocar a saída, por exemplo. "H1" aqui.
6. Clique em **Prever**.

	![Seção Prever][02]

## Etapas para adicionar um novo serviço Web

Publicar um serviço Web ([essa página](machine-learning-walkthrough-5-publish-web-service.md) explica como fazer isso) ou usar um serviço Web existente.

Obtenha a chave de API para o seu serviço Web. Onde fazer isso depende se você publicou um serviço Web clássico de um novo serviço Web.

**Serviço Web Clássico**

1. No Estúdio de Aprendizado de Máquina, clique na seção **SERVIÇOS WEB** no painel esquerdo e selecione o serviço Web.

	![Selecionar o serviço Web do Studio][04]

2. Copie a chave de API para o serviço Web.

	![Chave de API do Studio][05]

3. Na guia **PAINEL** do serviço Web, clique no link **SOLICITAÇÃO/RESPOSTA**.
4. Procure a seção **URI da solicitação**. Copie e salve a URL.

**Novo serviço Web**

1. No portal de serviços Web de Aprendizado de Máquina do Azure, clique em **Serviços Web** e, em seguida, selecione o serviço Web.
2. Clique em **Consumo**.
3. Procure a seção **Informações básicas de consumo**. Copie e salve a **Chave primária** e a URL de **solicitação-resposta**.


## Etapas para adicionar um novo serviço Web

1. Publicar um serviço Web ([essa página](machine-learning-walkthrough-5-publish-web-service.md) explica como fazer isso) ou usar um serviço Web existente.
2. No Excel, vá para a seção **Serviços Web** (se você estiver na seção **Prever**, clique na seta para voltar para ir para a lista de serviços Web).

	![Vá para a seleção de serviço Web][03]
3. Clique em **Adicionar Serviço Web**.
4. Cole a URL na caixa de texto do complemento do Excel chamada **URL**.
5. Cole a chave de API/primária na caixa de texto chamada **Chave de API**.
6. Clique em **Adicionar**.

	![URL e chave de API para um serviço Web clássico.][06]

10.	Para usar o serviço Web, siga as instruções acima, "Etapas para usar um serviço Web existente".

## Compartilhar sua pasta de trabalho

Se você salvar sua pasta de trabalho, as chaves de API/primária dos serviços Web adicionados também serão salvas. Isso significa que você só deve compartilhar a pasta de trabalho com pessoas confiáveis.

Faça quaisquer perguntas abaixo ou em nosso [fórum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png

<!---HONumber=AcomDC_0720_2016-->