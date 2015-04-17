<properties 
	pageTitle="Criando pontos de extremidade" 
	description="Criando pontos de extremidade de serviço Web no Aprendizado de Máquina do Azure" 
	services="machine-learning" 
	authors="hiteshmadan" 
	manager="padou" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="02/19/2015"
	ms.author="himad"/>


# Criando pontos de extremidade

O Aprendizado de Máquina do Azure permite criar vários pontos de extremidade de um serviço Web publicado.  Cada ponto de extremidade é individualmente endereçado, limitado e gerenciado, independentemente de outros pontos de extremidade do serviço Web.  Há uma URL e chave de autorização exclusiva para cada ponto de extremidade.

Isso permite que os usuários do Aprendizado de Máquina do Azure criem serviços Web que pode então ser vendidos para seus próprios clientes.  Cada ponto de extremidade pode ser personalizado individualmente com seus próprios modelos treinados, estando ainda vinculado ao experimento que criou este serviço Web.  Além disso, todas as atualizações de um experimento podem ser aplicadas seletivamente a um ponto de extremidade sem substituir as personalizações.

## Etapas de criação de ponto de extremidade
- Abra manage.windowsazure.com e clique em Aprendizado de Máquina na coluna à esquerda.  Clique no espaço de trabalho que contém o serviço Web no qual você está interessado.
![Navigate to workspace](./media/machine-learning-create-endpoint/figure-1.png)


- Clique na guia "Serviços Web".
![Navigate to web services](./media/machine-learning-create-endpoint/figure-2.png)


- Clique no serviço Web que você está interessado em ver na lista de pontos de extremidade disponíveis.
![Navigate to endpoint](./media/machine-learning-create-endpoint/figure-3.png)


- Clique no botão Adicionar ponto de extremidade na parte inferior.  Preencha com um nome e descrição, certifique-se de que não há nenhum outro ponto de extremidade com o mesmo nome neste serviço Web.  Deixe a limitação com o valor padrão a menos que você tenha requisitos especiais.
Se você quiser saber mais sobre a limitação, visite a página [Escala de Pontos de extremidade de API](machine-learning-scaling-endpoints.md).
![Create endpoint](./media/machine-learning-create-endpoint/figure-4.png)


Quando o ponto de extremidade é criado, você pode consumi-lo por meio de APIs síncronas, APIs de lote e planilhas do Excel.

<!--HONumber=49-->