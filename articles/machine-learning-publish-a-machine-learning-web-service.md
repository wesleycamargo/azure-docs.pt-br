<properties 
	pageTitle="Publicar um serviço Web de Aprendizado de Máquina | Azure" 
	description="Como converter um teste de treinamento em um teste de pontuação, prepará-lo para a publicação e publicá-lo como um serviço Web de Aprendizado de Máquina do Azure." 
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
	ms.date="03/04/2015" 
	ms.author="garye"/>

#Publicar um serviço Web de Aprendizado de Máquina do Azure

O Aprendizado de Máquina do Azure permite compilar, testar e implantar soluções de análise preditiva.

Em um ponto de exibição de alto nível, isso é feito em três etapas:

- **[Criar uma experiência de treinamento]** - o Estúdio de Aprendizado de Máquina do Azure é um ambiente de desenvolvimento visual colaborativo usado para treinar e testar um modelo de análise de previsão usando os dados de treinamento que você fornecer.
- **[Convertê-lo em um experimento de pontuação]** - quando o modelo tiver sido treinado com dados existentes e você estiver pronto para usá-lo para pontuar novos dados, preparar e simplificar seu teste de pontuação.
- **[Publicá-lo como um serviço da web]** - com apenas um clique, você pode publicar seu teste de pontuação como um serviço Web do Azure. Os usuários podem enviar dados ao seu modelo e receber as previsões do modelo.

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)]

Depois de publicar o serviço Web, você pode:

- **[Acessá-lo]** por meio da API do serviço Web
- **[Gerenciá-lo]** por meio do portal de gerenciamento do Azure, e
- **[Atualizá-lo]** se o seu modelo for alterado

[Criar uma experiência de treinamento]: #create-a-training-experiment
[Convertê-lo em um experimento de pontuação]: #convert-the-training-experiment-to-a-scoring-experiment
[Publicá-lo como um serviço da web]: #publish-the-scoring-experiment-as-a-web-service
[Acessá-lo]: #access-the-web-service
[Gerenciá-lo]: #manage-the-web-service-in-the-azure-management-portal
[Atualizá-lo]: #update-the-web-service


##Criar um teste de treinamento

Para treinar um modelo de análise de previsão, você usa o Estúdio de Aprendizado de Máquina do Azure para criar uma experiência de treinamento na qual você pode incluir vários módulos para carregar dados de treinamento, preparar os dados conforme necessário, aplicar algoritmos de aprendizagem de máquina e avaliar os resultados. Você pode iterar em um teste e testar algoritmos de aprendizado de máquina diferentes para comparar e avaliar os resultados.

O processo de criar e gerenciar testes de treinamento é abordado mais detalhadamente em outro ponto - consulte esses artigos para obter mais informações e exemplos:

- [Criar um experimento simples no Estúdio de Aprendizado de Máquina do Azure](machine-learning-create-experiment.md)
- [Desenvolver uma solução preditiva com o Aprendizado de Máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md)
- [Importar seus dados de treinamento para o Estúdio de Aprendizado de Máquina do Azure](machine-learning-import-data.md)
- [Gerenciar iterações de teste no Estúdio de Aprendizado de Máquina do Microsoft Azure](machine-learning-manage-experiment-iterations.md)

##Converter o teste de treinamento para um teste de pontuação

Após treinar o seu modelo, você estará pronto para usá-lo para pontuar novos dados. Para fazer isso, você pode converter seu teste de treinamento em um teste de pontuação. Convertendo um experimento pontuação, você estará preparando seu modelo para ser publicado como um serviço Web de pontuação. Os usuários do serviço Web enviarão dados de entrada para seu modelo e seu modelo enviará de volta os resultados da previsão. Assim, ao mudar para um experimento de pontuação, é recomendável ter em mente como você espera que seu modelo seja usado por outras pessoas.

![Converter para o teste de pontuação](./media/machine-learning-publish-web-service/figure-1.png)

Para obter mais detalhes sobre como fazer essa conversão, consulte [Converter um teste de treinamento de Aprendizado de Máquina em um teste de pontuação](machine-learning-convert-training-experiment-to-scoring-experiment.md)


##Publicar o experimento de pontuação como um serviço Web

Agora que o experimento de pontuação foi devidamente preparado, você pode publicá-lo como um serviço Web do Azure. Usando o serviço Web, os usuários podem enviar dados para seu modelo e o modelo retornará suas previsões.

Para publicar seu teste de pontuação, clique em **Executar** na parte inferior da tela do teste e, em seguida, clique em **PUBLICAR SERVIÇO WEB**. O serviço Web é configurado e colocado no painel de serviço Web.

![Publicar o serviço Web](./media/machine-learning-publish-web-service/figure-2.png)

Para testar o serviço Web, clique no link **Teste** na guia **PAINEL**. Uma caixa de diálogo é exibida para solicitar os dados de entrada para o serviço. Essas são as colunas esperadas pelo teste de pontuação. Insira um conjunto de dados e clique em **OK**. Os resultados gerados pelo serviço Web são exibidos na parte inferior do painel.

![Teste do serviço Web](./media/machine-learning-publish-web-service/figure-3.png)

Na guia **CONFIGURAÇÃO**, você pode alterar o nome de exibição do serviço e fornecer uma descrição. O nome e a descrição são exibidos no Portal de Gerenciamento do Azure onde você gerencia seus serviços Web. Você também pode habilitar o log para diagnosticar quaisquer falhas que esteja vendo quando chegar ao serviço Web. Para obter mais informações, consulte [Habilitar o log de serviços Web de Aprendizado de Máquina](machine-learning-web-services-logging.md).

![Configurar o serviço Web](./media/machine-learning-publish-web-service/figure-4.png)

Você também pode fornecer uma descrição de dados de entrada, dados de saída e parâmetros de serviço Web, fornecendo a inserção de uma cadeia de caracteres para cada coluna em **ESQUEMA DE ENTRADA**, **ESQUEMA DE SAÍDA** e **PARÂMETRO DE SERVIÇO WEB**. Essas descrições são usadas na documentação do código de exemplo fornecida para o serviço Web.

##Acessar o serviço Web

Após publicar seu serviço Web por meio do Estúdio de Aprendizado de Máquina, você pode enviar dados para o serviço e receber respostas de forma programática.

O painel fornece todas as informações necessárias para acessar o serviço Web. Por exemplo, a chave de API é fornecida para permitir acesso autorizado ao serviço e as páginas de ajuda de API são fornecidas para ajudar você a começar a escrever seu código.

Para obter mais informações sobre como acessar um serviço Web de Aprendizado de Máquina, consulte [Como consumir um serviço Web de Aprendizado de Máquina do Azure publicado](machine-learning-consume-web-services.md).


##Gerenciar o serviço Web no Portal de Gerenciamento do Azure

No Portal de Gerenciamento do Azure, você pode gerenciar seus serviços Web clicando no serviço **Aprendizado de Máquina**, abrindo o espaço de trabalho de Aprendizado de Máquina e abrindo o serviço Web na guia **SERVIÇOS WEB**. Nesta página, você pode monitorar o serviço Web, atualizá-lo e excluí-lo. Você também pode adicionar um segundo ponto de extremidade ao serviço Web, além do ponto de extremidade padrão que é criado quando você o publica.

Para obter mais informações, consulte [Gerenciar um espaço de trabalho de Aprendizado de Máquina do Azure](machine-learning-manage-workspace.md). <!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**. 
-->


##Atualizar o serviço Web

Você pode fazer alterações ao serviço Web, como atualizar o modelo com dados de treinamento adicional e publicá-lo novamente, substituindo o serviço Web original.

Para atualizar o serviço Web, abra o teste de pontuação original usado para publicar o serviço Web original e faça uma cópia editável clicando em **SALVAR COMO**. Faça as alterações e, em seguida, clique em **PUBLICAR SERVIÇO WEB**. Como você já publicou esse teste antes, o Estúdio de Aprendizado de Máquina pergunta se você deseja substituir o serviço existente. Se você clicar em **SIM**, o serviço Web existente será interrompido e o novo teste de pontuação será publicado em seu lugar.

> [AZURE.NOTE]Se você fez alterações na configuração do serviço Web original, por exemplo, inserindo um novo nome de exibição ou uma descrição, você precisará inserir esses valores novamente.


<!--HONumber=54-->