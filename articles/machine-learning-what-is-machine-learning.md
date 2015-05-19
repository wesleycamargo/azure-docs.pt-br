<properties 
    pageTitle="O que é Aprendizado da Máquina do Azure? | Microsoft Azure" 
    description="Visão geral do serviço Aprendizado da Máquina do Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="tedway" 
    manager="neerajkh" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/22/2015" 
    ms.author="tedway;olgali"/>


# O que aprendizado da máquina?
O aprendizado da máquina está em ação à sua volta. Quando você faz compras online, o aprendizado da máquina ajuda a recomendar outros produtos com base no que você comprou. Ao passar seu cartão de crédito, o aprendizado da máquina ajuda o banco a fazer a detecção de fraudes e notificá-lo se a transação parecer suspeita. O aprendizado da máquina é o processo de criar modelos para aprender com os dados existentes e fazer análises de previsão sobre dados futuros.

## O que é Aprendizado da Máquina do Azure?
O Aprendizado da Máquina do Azure é um serviço de análise de previsão poderoso baseado em nuvem que permite criar rapidamente soluções de análise. Criamos o Aprendizado da Máquina do Azure para democratizar o aprendizado da máquina: eliminamos o trabalho pesado envolvido na criação e na implantação da tecnologia de aprendizado da máquina para torná-lo acessível a todos. É um serviço totalmente gerenciado, o que significa que você não precisa comprar nenhum hardware nem gerenciar manualmente máquinas virtuais.

Você pode usar a ferramenta baseada em navegador [Studio do Aprendizado da Máquina](machine-learning-what-is-ml-studio.md) para criar rapidamente e automatizar os fluxos de trabalho do aprendizado da máquina. Você pode arrastar e soltar centenas de bibliotecas [ de Aprendizado da Máquina](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) existentes para impulsionar suas soluções de análise de previsão e opcionalmente adicionar seus próprios scripts [R](machine-learning-r-quickstart.md) e [Python](machine-learning-execute-python-scripts.md) personalizados para estendê-las. O Studio funciona em qualquer navegador e permite que você desenvolva rapidamente e itere soluções. ![Experiências da análise de previsão na nuvem com o Studio de Aprendizado da Máquina do Azure](./media/machine-learning-what-is-machine-learning/AzureMLStudio.png)

Você pode descobrir e criar facilmente [serviços da Web](machine-learning-publish-a-machine-learning-web-service.md), [treinar e treinar novamente seus modelos por meio de APIs](machine-learning-retrain-models-programmatically.md), [gerenciar pontos de extremidade](machine-learning-create-endpoint.md), [dimensionar serviços da Web](machine-learning-scaling-endpoints.md) por cliente e configurar o diagnóstico para o monitoramento e a depuração de serviços. Os recursos mais recentes incluem:

- A capacidade de criar um módulo R configurável e personalizado, incorporar seus próprios scripts R de treinamento/previsão e adicionar scripts Python usando um grande ecossistema de bibliotecas, como numpy, scipy, pandas ou scikit-learn. Agora, você pode treinar terabytes de dados usando o [Aprendizado com Contagens][learning-with-counts], usar o PCA ou uma SVM com uma classe para a detecção de anomalias, modificar, filtrar e limpar facilmente os dados usando o SQLite familiar. 
- A [Galeria da Comunidade do Aprendizado da Máquina](machine-learning-gallery-how-to-use-contribute-publish.md) permite descobrir e usar experiências interessantes criadas por outras pessoas. Você pode fazer perguntas ou enviar comentários sobre as experiências na [Galeria](http://gallery.azureml.net) ou publicar uma própria. Você pode compartilhar links para experiências interessantes por meio de canais de redes sociais, como o LinkedIn e o Twitter. A galeria é uma ótima maneira para os usuários começarem o Aprendizado da Máquina do Azure e aprenderem com outras pessoas na comunidade. ![Tente exemplos de experimento de previsão ou contribua com o seu próprio na Galeria de Aprendizado da Máquina do Azure](./media/machine-learning-what-is-machine-learning/AzureMLGallery.png)
- Você pode adquirir [aplicativos Marketplace](https://datamarket.azure.com/browse?query=machine+learning) por meio de uma assinatura do Azure e usar os serviços da web concluídos para obter Recomendações, Análise de Texto e Detecção de Anomalias diretamente do Azure Marketplace. 
- Um guia passo a passo para a jornada da Ciência de Dados de dados brutos para um serviço da Web utilizado para facilitar o caminho da ciência de dados com base na nuvem. Adicionamos a capacidade de usar ferramentas populares, como o iPython Notebook e Python Tools para Visual Studio com o Aprendizado da Máquina do Azure.

## Comece agora mesmo
Você pode aprender os fundamentos da análise de previsão e do aprendizado da máquina usando um [tutorial passo a passo](machine-learning-create-experiment.md) e [aproveitando os exemplos](machine-learning-sample-experiments.md). Nenhuma assinatura do Azure ou cartão de crédito é necessário para começar a usar o Aprendizado da Máquina quando você tenta experimentos no Studio.


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/

<!--HONumber=54-->