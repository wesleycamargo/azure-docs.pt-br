<properties
	pageTitle="Consumir um serviço Web de Aprendizado de Máquina do Excel | Microsoft Azure"
	description="Consumir um serviço Web de Aprendizado de Máquina do Azure do Excel"
	services="machine-learning"
	documentationCenter=""
	authors="tedway"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="tedway"/>

# Consumindo um Serviço Web de Aprendizado de Máquina do Azure do Excel

 O Estúdio de Aprendizado de Máquina do Azure torna fácil chamar serviços Web diretamente do Excel, sem a necessidade de escrever nenhum código.

Se estiver usando o Excel 2013 (ou posterior) ou o Excel Online, é recomendável usar o [suplemento do Excel](machine-learning-excel-add-in-for-web-services.md).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Etapas

Publicar um serviço Web. [Esta página](machine-learning-walkthrough-5-publish-web-service.md) explica como fazer isso. Atualmente, o recurso de pasta de trabalho do Excel só tem suporte para serviços de Solicitação/Resposta que têm uma única saída (ou seja, um único rótulo de pontuação).

Após ter um serviços Web, clique na seção **SERVIÇOS WEB** à esquerda do estúdio e selecione o serviço Web a ser consumido no Excel.

**Serviço Web Clássico**

1. Na guia **PAINEL** para o serviço Web, há uma linha para o serviço de **SOLICITAÇÃO/RESPOSTA**. Se esse serviço tiver uma única saída, você deve ver o link **Baixar Pasta de Trabalho do Excel** nessa linha.

	![][1]

2. Clique em **Baixar Pasta de Trabalho do Excel**.

**Novo serviço Web**

1. No portal do Serviço Web de Aprendizado de Máquina do Azure, selecione **Consumir**.
2. Na página Consumir, na seção **Opções de consumo do serviço Web**, clique no ícone do Excel.

**Usando a pasta de trabalho**

1. Abra a pasta de trabalho.

2. Será exibido um Aviso de Segurança; clique no botão **Habilitar Edição**.

	![][2]

3. Será exibido um Aviso de Segurança. Clique no botão **Habilitar Conteúdo** para executar macros na sua planilha.

	![][3]
4. Depois que as macros estiverem habilitadas, uma tabela será gerada. As colunas em azul são requeridas como entrada para o serviço Web RRS ou **PARÂMETROS**. Observe a saída do serviço RRS, **VALORES PREVISTOS**, em verde. Quando todas as colunas para uma determinada linha estiverem preenchidas, a pasta de trabalho automaticamente chama a API de pontuação e exibe os resultados de pontuação.

	![][4]

5. Para pontuar mais de uma linha, preencha a segunda linha com dados e os valores previstos serão produzidos. Você pode até mesmo colar várias linhas ao mesmo tempo.

Você pode usar qualquer um dos recursos do Excel (gráficos, Power Map, formatação condicional, etc.) com os valores previstos para ajudar na visualização dos dados.


## Compartilhar sua pasta de trabalho

Para as macros funcionarem, sua Chave de API precisa fazer parte da planilha. Isso significa que você deve compartilhar a pasta de trabalho apenas com entidades/pessoas confiáveis.

## Atualizações automáticas

É feita uma chamada RRS nessas duas situações:

1. Na primeira vez em que uma linha tem conteúdo em todos os seus **PARÂMETROS**

2. Sempre que qualquer um dos **PARÂMETROS** for alterado em uma linha que tinha todos os seus **PARÂMETROS** preenchidos.

[1]: ./media/machine-learning-consuming-from-excel/excellink.png
[2]: ./media/machine-learning-consuming-from-excel/enableeditting.png
[3]: ./media/machine-learning-consuming-from-excel/enablecontent.png
[4]: ./media/machine-learning-consuming-from-excel/sampletable.png

<!---HONumber=AcomDC_0720_2016-->