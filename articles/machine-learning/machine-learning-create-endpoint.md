<properties 
	pageTitle="Criando pontos de extremidade de serviço Web no Aprendizado de Máquina | Microsoft Azure" 
	description="Criando pontos de extremidade de serviço Web no Aprendizado de Máquina do Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="hiteshmadan" 
	manager="padou" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="02/10/2016"
	ms.author="himad"/>


# Criando pontos de extremidade

O Aprendizado de Máquina do Azure permite criar vários pontos de extremidade de um serviço Web publicado. Cada ponto de extremidade é individualmente endereçado, limitado e gerenciado, independentemente de outros pontos de extremidade do serviço Web. Há uma URL e chave de autorização exclusiva para cada ponto de extremidade.

Isso permite que os usuários do Aprendizado de Máquina do Azure criem serviços Web que pode então ser vendidos para seus próprios clientes. Cada ponto de extremidade pode ser personalizado individualmente com seus próprios modelos treinados, estando ainda vinculado ao experimento que criou este serviço Web. Além disso, todas as atualizações de um experimento podem ser aplicadas seletivamente a um ponto de extremidade sem substituir as personalizações.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Etapas de criação de ponto de extremidade
- Abra [http://manage.windowsazure.com](http://manage.windowsazure.com) e clique em **Aprendizado de Máquina** na coluna à esquerda. Clique no espaço de trabalho que contém o serviço Web no qual você está interessado. ![Navegar até o espaço de trabalho](./media/machine-learning-create-endpoint/figure-1.png)


- Clique na guia **Serviços Web**. ![Navegar até os serviços Web](./media/machine-learning-create-endpoint/figure-2.png)


- Clique no Serviço Web que você está interessado em ver na lista de pontos de extremidade disponíveis. ![Navegar até o ponto de extremidade](./media/machine-learning-create-endpoint/figure-3.png)


- Clique no botão **Adicionar Ponto de Extremidade**, na parte inferior. Preencha com um nome e descrição, certifique-se de que não há nenhum outro ponto de extremidade com o mesmo nome neste serviço Web. Deixe a limitação com o valor padrão, a menos que você tenha requisitos especiais. Se você quiser saber mais sobre a limitação, visite a página [Escala de Pontos de extremidade de API](machine-learning-scaling-endpoints.md). ![Criar ponto de extremidade](./media/machine-learning-create-endpoint/figure-4.png)


Quando o ponto de extremidade é criado, você pode consumi-lo por meio de APIs síncronas, APIs de lote e planilhas do Excel. Além de adicionar pontos de extremidade com essa interface do usuário, você também pode usar as APIs de gerenciamento do ponto de extremidade programaticamente para adicionar pontos de extremidade. Para obter mais informações sobre como utilizar serviços Web de Aprendizado de Máquina, consulte [Como consumir um serviço Web de Aprendizado de Máquina do Azure publicado](machine-learning-consume-web-services.md).
 

<!---HONumber=AcomDC_0211_2016-->