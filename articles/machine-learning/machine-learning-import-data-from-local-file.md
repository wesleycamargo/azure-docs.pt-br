<properties
	pageTitle="Importar dados no Estúdio de Aprendizado de Máquina de um arquivo local | Microsoft Azure"
	description="Como importar os dados de treinamento para o Estúdio de Aprendizado de Máquina do Azure de um arquivo local."
	keywords="importar dados, formato de dados, tipos de dados, fontes de dados, dados de treinamento"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="garye;bradsev" />


# Importe os seus dados de treinamento no Estúdio de Aprendizado de Máquina do Azure de um arquivo local

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Para usar seus próprios dados no Estúdio de Aprendizado de Máquina, você pode antes carregar antecipadamente um arquivo de dados do disco rígido local para criar um módulo de conjunto de dados em seu espaço de trabalho.


## Importar dados de um arquivo local

Você pode importar dados de um disco rígido local fazendo o seguinte:

1. clique em **+NOVO** na parte inferior da janela do Estúdio de Aprendizado de Máquina.
2. Selecione **CONJUNTO DE DADOS** e **DO ARQUIVO LOCAL**.
3. Na caixa de diálogo **Carregar um novo conjunto de dados**, navegue até o arquivo que deseja carregar
4. Digite um nome, identifique o tipo de dados e, opcionalmente, insira uma descrição. Uma descrição é recomendada - ela permite que você registre quaisquer características sobre os dados que você desejará lembrar ao usar os dados no futuro.
5. A caixa de seleção **Esta é a nova versão de um conjunto de dados existente** permite que você atualize um conjunto de dados existente com novos dados. Basta clicar nesta caixa de seleção e digitar o nome de um conjunto de dados existente.

Durante o carregamento, você verá uma mensagem de que seu arquivo está sendo carregado. O tempo de carregamento depende do tamanho de seus dados e da velocidade da sua conexão com o serviço. Se souber que o arquivo levará muito tempo, você pode fazer outras coisas dentro do Estúdio de Aprendizado de Máquina enquanto espera. No entanto, fechar o navegador fará com que o carregamento de dados falhe.

Uma vez carregados, seus dados são armazenados em um módulo de conjunto de dados e ficam disponíveis para qualquer experimento em seu espaço de trabalho. Você pode encontrar o conjunto de dados, juntamente com todos os conjuntos de dados de exemplo pré-carregados, na lista **Conjuntos de Dados Salvos** na paleta de módulos quando estiver editando um experimento. Você pode arrastar e soltar o conjunto de dados na tela do experimento quando quiser usar esse conjunto de dados para outras análises e aprendizado de máquina.

<!---HONumber=AcomDC_0218_2016-->