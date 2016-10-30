<properties
    pageTitle="Suplemento do Excel para serviços Web do Machine Learning | Microsoft Azure"
    description="Como usar os serviços Web do Azure Machine Learning diretamente no Excel sem escrever nenhum código."
    services="machine-learning"
    documentationCenter=""
    authors="tedway"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/05/2016"
    ms.author="tedway;garye" />


# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Suplemento do Excel para serviços Web do Azure Machine Learning

O Excel torna fácil chamar serviços Web diretamente, sem a necessidade de escrever nenhum código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Etapas para usar um serviço Web existente na pasta de trabalho

1. Abra o [arquivo de exemplo do Excel](http://aka.ms/amlexcel-sample-2), que contém o suplemento do Excel e os dados sobre passageiros do Titanic.
2. Escolha o serviço Web clicando nele - "Titanic Survivor Predictor (exemplo de suplemento do Excel) [Pontuação]" neste exemplo.

    ![Selecionar o serviço Web][01]

3. Você será levado à seção **Prever** .  Esta pasta de trabalho já contém dados de exemplo, mas para uma pasta de trabalho em branco você pode selecionar uma célula no Excel e clicar em **Usar dados de exemplo**.
4. Selecione os dados com cabeçalhos e clique no ícone de intervalo de dados de entrada.  Verifique se a caixa "Meus dados contêm cabeçalhos" está marcada.
5. Em **Saída**, insira o número da célula em que você deseja colocar a saída, por exemplo, "H1" aqui.
6. Clique em **Prever**.

    ![Seção Prever][02]

## <a name="steps-to-add-a-new-web-service"></a>Etapas para adicionar um novo serviço Web

Implante um serviço Web ou use um serviço Web existente. Para obter mais informações sobre como implantar um serviço Web, consulte [Etapa 5 do passo a passo: Implantar o serviço Web do Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md).

Obtenha a chave de API para o seu serviço Web. Onde fazer isso depende se você publicou um serviço Web do Machine Learning clássico de um novo serviço Web do Machine Learning.

**Use um serviço Web clássico** 

1. No Machine Learning Studio, clique na seção **SERVIÇOS WEB** no painel esquerdo e selecione o serviço Web.

    ![Selecione um serviço Web do Studio][04]

2. Copie a chave de API para o serviço Web.

    ![Chave de API do Studio][05]

3. Na guia **PAINEL** do serviço Web, clique no link **SOLICITAÇÃO/RESPOSTA**.
4. Procure a seção **URI da solicitação** .  Copie e salve a URL.

>[AZURE.NOTE] Agora é possível iniciar uma sessão do portal dos [Serviços Web do aprendizado do Azure Machine Learning](https://services.azureml.net) para obter a chave de API para um serviço Web do Machine Learning clássico.

**Use um novo serviço Web**

1. No portal de serviços Web do [Azure Machine Learning](https://services.azureml.net), clique em **Serviços Web** e, em seguida, selecione o serviço Web. 
2. Clique em **Consumo**.
3. Procure a seção **Informações básicas de consumo** . Copie e salve a **Chave primária** e a URL de **solicitação-resposta**.


## <a name="steps-to-add-a-new-web-service"></a>Etapas para adicionar um novo serviço Web

1. Implante um serviço Web ou use um serviço Web existente. Para obter mais informações sobre como implantar um serviço Web, consulte [Etapa 5 do passo a passo: Implantar o serviço Web do Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md).
2. Clique em **Consumo**.
3. Procure a seção **Informações básicas de consumo** . Copie e salve a **Chave primária** e a URL de **solicitação-resposta**.
2. No Excel, vá para a seção **Serviços Web** (se você estiver na seção **Prever**, clique na seta para voltar para ir para a lista de serviços Web).

    ![Vá para a seleção de serviço Web][03]
    
3. Clique em **Adicionar Serviço Web**.
4. Cole a URL na caixa de texto do complemento do Excel chamada **URL**.
5. Cole a chave de API/primária na caixa de texto chamada **Chave de API**.
6. Clique em **Adicionar**.

    ![URL e chave de API para um serviço Web clássico.][06]

10. Para usar o serviço Web, siga as instruções acima, "Etapas para usar um serviço Web existente".

## <a name="sharing-your-workbook"></a>Compartilhar sua pasta de trabalho

Se você salvar sua pasta de trabalho, as chaves de API/primária dos serviços Web adicionados também serão salvas. Isso significa que você só deve compartilhar a pasta de trabalho com pessoas confiáveis.

Faça suas perguntas na seção de comentário abaixo ou em nosso [fórum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png



<!--HONumber=Oct16_HO2-->


