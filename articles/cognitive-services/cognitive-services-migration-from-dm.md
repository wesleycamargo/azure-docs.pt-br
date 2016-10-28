
<properties
	pageTitle="Migrar para a API de Recomendações de Serviços Cognitivos do Azure desde a API de Recomendações do DataMarket | Microsoft Azure"
	description="Recomendações do aprendizado de máquina do Azure – migração para o serviço cognitivo de recomendações"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="luisca"/>


# Migrar para a API de Recomendações dos Serviços Cognitivos do Azure da API de Recomendações do DataMarket
Este artigo mostra como migrar da [API de Recomendações do Microsoft DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) para a [API de Recomendações dos Serviços Cognitivos do Microsoft Azure](https://www.microsoft.com/cognitive-services/pt-BR/recommendations-api).

A API de Recomendações do DataMarket vai parar de aceitar novos clientes em 31 de dezembro de 2016 e será substituída em 28 de fevereiro de 2017.

## Como eu começo a usar a API de Recomendações de Serviços Cognitivos do Azure?

Para migrar para a API de Recomendações de Serviços Cognitivos, faça o seguinte:

1.	Se você ainda não tiver uma assinatura do Azure, [inscreva-se](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) para obter uma.

1.	As instruções passo a passo do [Guia de Início Rápido](cognitive-services-recommendations-quick-start.md) para inscrever-se na API de Recomendações de Serviços Cognitivos e usá-la programaticamente.

1.	Vá para a [página de aterrissagem da API de Recomendações de Serviços Cognitivos](https://www.microsoft.com/cognitive-services/pt-BR/recommendations-api) para saber mais sobre o serviço e para encontrar documentação.

## Usei a interface do usuário de Recomendações para criar meus modelos. Há uma ferramenta semelhante para a API de Recomendações de Serviços Cognitivos?

Claro que não! A URL para a nova [interface do usuário de Recomendações](http://recommendations-portal.azurewebsites.net/) é http://recommendations-portal.azurewebsites.net.

>[AZURE.NOTE] Suas credenciais do DataMarket não funcionarão aqui. Inscreva-se em uma assinatura no Portal do Azure e obtenha a Chave de Conta necessária para usar a nova [interface do usuário de Recomendações](http://recommendations-portal.azurewebsites.net/). Se você não tiver uma Chave de Conta, veja a Tarefa 1 do [Guia de Início Rápido](cognitive-services-recommendations-quick-start.md).

##O novo formato de API é igual à da API de Recomendações do DataMarket?

A API oferece suporte aos mesmos cenários e aos fluxos de processo como os cenários com suporte na versão do DataMarket, mas a interface real da API foi atualizada para estar de acordo com [as diretrizes da API REST da Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). As APIs são mais consistentes e funcionam melhor com ferramentas que ofereçam suporte ao Swagger.

Para entender cada uma das APIs, confira o [API do Explorer](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db). Use o botão *Experimente* para testar uma chamada de API. O formato dos arquivos consumidos pela API de Recomendações (arquivos de catálogo e de uso) não mudou, portanto você pode continuar usando os mesmos arquivos e/ou infraestrutura que criou para gerar esses arquivos.

##Quais são alguns recursos novos da API de Recomendações de Serviços Cognitivos?

Nos últimos dois meses, lançamos novos recursos para a API de Recomendações de Serviços Cognitivos:
-	Interface do usuário de Recomendações para treinar e testar modelos sem escrever uma única linha de código
-	Pontuação do Lote para fornecer milhares de recomendações de uma só vez
-	Suporte a métricas de compilação para consultar a qualidade dos modelos de recomendações
-	Suporte para regras de negócio
-	Capacidade para enumerar e baixar arquivos de catálogo e de uso
-	Suporte de compilação de classificação para consultar a qualidade dos recursos de item em um modelo de recomendações
-	Adicionada a capacidade de procurar por um produto no catálogo

## Quando a Microsoft interromperá o suporte à API de Recomendações do DataMarket?

A [API de Recomendações no DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) não aceitará mais novos clientes depois de 31 de dezembro de 2016 e será completamente substituída em 28 de fevereiro de 2017.

## E se eu não tiver os dados necessários para recriar meus modelos na API de Recomendações de Serviços Cognitivos?

Queremos tornar essa transição a mais fácil possível para você. Podemos ajudá-lo a mover seus modelos antigos de sua conta do DataMarket para sua nova assinatura da API de Recomendações de Serviços Cognitivos do Azure. Entre em contato conosco pelo email [mlapi@microsoft.com](mailto://mlapi@microsoft.com). Pediremos que você forneça sua ID de assinatura do DataMarket e sua ID de assinatura dos Serviços Cognitivos antes de associarmos os modelos à sua nova conta.

<!---HONumber=AcomDC_0914_2016-->