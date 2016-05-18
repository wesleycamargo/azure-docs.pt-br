<properties
	pageTitle="Implantar um serviço Web de Aprendizado de Máquina | Microsoft Azure"
	description="Como converter um teste de treinamento em um teste preditivo, prepará-lo para implantação e implantá-lo como um serviço Web de Aprendizado de Máquina do Azure."
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
	ms.date="05/02/2016"
	ms.author="garye"/>

# Implantar um serviço Web de Aprendizado de Máquina do Azure

O Aprendizado de Máquina do Azure permite compilar, testar e implantar soluções de análise preditiva.

Em um ponto de exibição de alto nível, isso é feito em três etapas:

- **[Criar uma experiência de treinamento]** - o Estúdio de Aprendizado de Máquina do Azure é um ambiente de desenvolvimento visual colaborativo usado para treinar e testar um modelo de análise de previsão usando os dados de treinamento que você fornecer.
- **[Convertê-lo em um teste preditivo]**: depois que o modelo tiver sido treinado com dados existentes e você estiver pronto para usá-lo para pontuar novos dados, prepare e simplifique seu teste de pontuação.
- **[Implantá-lo como um serviço Web]**: com apenas um clique, você pode implantar seu teste de pontuação como um serviço Web do Azure. Os usuários podem enviar dados ao seu modelo e receber as previsões do modelo.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Depois de implantar o serviço Web, é possível:

- **[Acessá-lo]** por meio da API do serviço Web
- **[Gerenciá-lo]** por meio do Portal Clássico do Azure e
- **[Atualizá-lo]** se o seu modelo for alterado

[Criar uma experiência de treinamento]: #create-a-training-experiment
[Convertê-lo em um teste preditivo]: #convert-the-training-experiment-to-a-predictive-experiment
[Implantá-lo como um serviço Web]: #deploy-the-predictive-experiment-as-a-web-service
[Acessá-lo]: #access-the-web-service
[Gerenciá-lo]: #manage-the-web-service-in-the-azure-management-portal
[Atualizá-lo]: #update-the-web-service


## Criar um teste de treinamento

Para treinar um modelo de análise de previsão, você usa o Estúdio de Aprendizado de Máquina do Azure para criar uma experiência de treinamento na qual você pode incluir vários módulos para carregar dados de treinamento, preparar os dados conforme necessário, aplicar algoritmos de aprendizagem de máquina e avaliar os resultados. Você pode iterar em um teste e testar algoritmos de aprendizado de máquina diferentes para comparar e avaliar os resultados.

O processo de criar e gerenciar testes de treinamento é abordado mais detalhadamente em outro ponto - consulte esses artigos para obter mais informações e exemplos:

- [Criar um experimento simples no Estúdio de Aprendizado de Máquina do Azure](machine-learning-create-experiment.md)
- [Desenvolver uma solução preditiva com o Aprendizado de Máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md)
- [Importar seus dados de treinamento para o Estúdio de Aprendizado de Máquina do Azure](machine-learning-data-science-import-data.md)
- [Gerenciar iterações de teste no Estúdio de Aprendizado de Máquina do Microsoft Azure](machine-learning-manage-experiment-iterations.md)

## Converter o teste de treinamento em um teste preditivo

Após treinar o seu modelo, você estará pronto para usá-lo para pontuar novos dados. Para fazer isso, converta o teste de treinamento em um teste preditivo. Ao converter em um teste preditivo, você estará preparando seu modelo para ser implantado como um serviço Web de pontuação. Os usuários do serviço Web enviarão dados de entrada para seu modelo e seu modelo enviará de volta os resultados da previsão. Assim, à medida que converte em um teste preditivo, é recomendável ter em mente como você espera que seu modelo seja usado por outras pessoas.

Para converter o teste de treinamento em um teste preditivo, clique em **Executar** na parte inferior da tela do teste, clique em **Configurar Serviço Web** e selecione **Serviço Web Preditivo**.

![Converter para o teste de pontuação](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

Para obter mais detalhes sobre como fazer essa conversão, veja [Converter um teste de treinamento do Aprendizado de Máquina em um teste preditivo](machine-learning-convert-training-experiment-to-scoring-experiment.md)


## Implantar o teste preditivo como um serviço Web

Agora que o teste preditivo foi devidamente preparado, você pode implantá-lo como um serviço Web do Azure. Usando o serviço Web, os usuários podem enviar dados para seu modelo e o modelo retornará suas previsões.

Para implantar o teste preditivo, clique em **Executar** na parte inferior da tela do teste e clique em **Implantar Serviço Web**. O serviço Web é configurado e colocado no painel de serviço Web.

![Implantar o serviço Web](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

Para testar o serviço Web, clique no link **Teste** no painel de serviço Web. Uma caixa de diálogo é exibida para solicitar os dados de entrada para o serviço. Essas são as colunas esperadas pelo teste de pontuação. Insira um conjunto de dados e clique em **OK**. Os resultados gerados pelo serviço Web são exibidos na parte inferior do painel.

![Teste do serviço Web](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

Na guia **CONFIGURAÇÃO**, você pode alterar o nome de exibição do serviço e fornecer uma descrição. O nome e a descrição são exibidos no [Portal Clássico do Azure](http://manage.windowsazure.com/), no qual você gerencia seus serviços Web. Você pode fornecer uma descrição dos dados de entrada, dados de saída e parâmetros de serviço Web inserindo uma cadeia de caracteres para cada coluna em **INPUT SCHEMA**, **OUTPUT SCHEMA** e **WEB SERVICE PARAMETER**. Essas descrições são usadas na documentação do código de exemplo fornecida para o serviço Web. Você também pode habilitar o log para diagnosticar quaisquer falhas que esteja vendo quando o serviço Web for acessado.

Para obter mais informações, consulte [Habilitar o log de serviços Web de Aprendizado de Máquina](machine-learning-web-services-logging.md).

![Configurar o serviço Web](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)


## Acessar o serviço Web

Depois de implantar o serviço Web por meio do Estúdio de Aprendizado de Máquina, você pode enviar dados para o serviço e receber respostas de forma programática.

O painel fornece todas as informações necessárias para acessar o serviço Web. Por exemplo, a chave de API é fornecida para permitir acesso autorizado ao serviço e as páginas de ajuda de API são fornecidas para ajudar você a começar a escrever seu código.

Para obter mais informações sobre como acessar um serviço Web do Aprendizado de Máquina, veja [Como consumir um serviço Web implantado do Aprendizado de Máquina do Azure](machine-learning-consume-web-services.md).


## Gerenciar o serviço Web no Portal Clássico do Azure

No [Portal Clássico do Azure](http://manage.windowsazure.com/), você pode gerenciar seus serviços Web clicando no serviço **Aprendizado de Máquina**, abrindo o espaço de trabalho do Aprendizado de Máquina e, então, o serviço Web na guia **SERVIÇOS WEB**. Nesta página, você pode monitorar o serviço Web, atualizá-lo e excluí-lo. Você também pode adicionar um segundo ponto de extremidade ao serviço Web, além do ponto de extremidade padrão que é criado durante sua implantação.

Para obter mais informações, consulte [Gerenciar um espaço de trabalho de Aprendizado de Máquina do Azure](machine-learning-manage-workspace.md).
<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->


## Atualizar o serviço Web

Você pode fazer alterações ao serviço Web, como atualizar o modelo com dados de treinamento adicionais e implantá-lo novamente, substituindo o serviço Web original.

Para atualizar o serviço Web, abra o teste preditivo original que você usou para implantar o serviço Web original e faça uma cópia editável clicando em **SALVAR COMO**. Faça as alterações e clique em **Implantar Serviço Web**. Como você já implantou esse teste antes, o Estúdio de Aprendizado de Máquina pergunta se você deseja substituir o serviço existente. Se você clicar em **SIM**, o serviço Web existente será interrompido e o novo teste preditivo será implantado em seu lugar.

> [AZURE.NOTE] Se você fez alterações na configuração do serviço Web original, por exemplo, inserindo um novo nome de exibição ou uma descrição, você precisará inserir esses valores novamente.

Uma opção para atualizar seu serviço Web é treinar novamente o modelo de forma programática. Para obter mais informações, consulte [Treinar novamente os modelos de Aprendizado de Máquina de forma programática](machine-learning-retrain-models-programmatically.md).

<!---HONumber=AcomDC_0504_2016-->