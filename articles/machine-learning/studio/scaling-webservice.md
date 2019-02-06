---
Título: Dimensionar um serviço Web do Machine Learning Studio – titleSuffix: Descrição do Azure Machine Learning Studio: Saiba como aumentar a simultaneidade de um serviço da web do Azure Machine Learning Studio adicionando pontos de extremidade adicionais.
services: machine-learning ms.service: machine-learning ms.subservice: studio ms.topic: article

autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT ms.date: 23/01/2017
---
# <a name="scaling-an-azure-machine-learning-studio-web-service-by-adding-additional-endpoints"></a>Dimensionando um serviço Web do Azure Machine Learning Studio adicionando mais pontos de extremidade
> [!NOTE]
> Este tópico descreve as técnicas aplicáveis a um Serviço Web do Machine Learning **Clássico**. 
> 
> 

Por padrão, cada serviço Web publicado é configurado para oferecer suporte a 20 solicitações simultâneas, podendo chegar a 200. O Azure Machine Learning otimiza essa configuração automaticamente para fornecer o melhor desempenho ao serviço Web, e o valor do portal é ignorado. 

Se você planeja chamar a API com uma carga maior que o valor suportado de 200 para o Máximo de Chamadas Simultâneas, é preciso criar vários pontos de extremidade no mesmo serviço Web. Você pode, então, distribuir a carga aleatoriamente entre todos eles.

O dimensionamento de um serviço Web é uma tarefa comum. Entre os motivos para dimensionar estão oferecer suporte a mais de 200 solicitações simultâneas, aumentar a disponibilidade por meio de vários pontos de extremidade ou fornecer pontos de extremidade separados ao serviço Web. É possível aumentar a escala adicionando mais pontos de extremidade ao serviço Web por meio do portal do [Serviço Web do Azure Machine Learning](https://services.azureml.net/).

Para saber mais sobre a adição de novos pontos de extremidade, consulte [Criando pontos de extremidade](create-endpoint.md).

Tenha em mente que usar uma contagem de simultaneidade alta pode ser prejudicial se você não estiver chamando a API com uma taxa correspondentemente alta. Você pode ver tempos limite esporádicos e/ou picos na latência se colocar uma carga relativamente baixa em uma API configurada para alta carga.

As APIs síncronas são normalmente usadas em situações onde uma baixa latência é desejada. A latência aqui indica o tempo necessário para a API concluir uma solicitação e não se responsabiliza por quaisquer atrasos na rede. Digamos que você tenha uma API com uma latência de 50 ms. Para consumir totalmente a capacidade disponível com alto nível de limitação e o Máximo de Chamadas Simultâneas = 20, você precisa chamar esta API 20 * 1000 / 50 = 400 vezes por segundo. Estendendo isso ainda mais, um Máximo de Chamadas Simultâneas de 200 permite que você chame a API 4000 vezes por segundo, supondo que a latência seja de 50 ms.

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png
