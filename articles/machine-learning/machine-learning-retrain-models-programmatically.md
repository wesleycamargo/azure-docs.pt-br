<properties
	pageTitle="Readaptar os modelos de Aprendizado de Máquina de forma programática | Microsoft Azure"
	description="Aprenda como readaptar um modelo de forma programática e atualizar o serviço Web para usar o modelo treinado recentemente no Aprendizado de Máquina do Azure."
	services="machine-learning"
	documentationCenter=""
	authors="raymondlaghaeian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="raymondl;garye;v-donglo"/>


#Readaptar os modelos de Aprendizado de Máquina de forma programática  

Como parte do processo de operacionalização de modelos de aprendizado de máquina no Aprendizado de Máquina do Azure, um modelo deve ser treinado e salvo. Em seguida, use-o para criar um serviço Web predicativo. O serviço Web pode ser consumido em sites, painéis e aplicativos móveis.

Os modelos que você cria usando o Aprendizado de Máquina geralmente não são estáticos. Conforme novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados, o modelo precisa ser readaptado. Ou talvez seja necessário aplicar filtros para obter um subconjunto dos dados e readaptar o modelo.

A readaptação pode ocorrer com frequência. Com o recurso de API de Readaptação Programática, você pode programaticamente recuperar o modelo usando as APIs de readaptação e atualizar o Serviço Web com o modelo treinado recentemente.

Este documento descreve o processo de readaptação e mostra como usar as APIs de Readaptação.

## Por que readaptar: definindo o problema  

Como parte do processo de treinamento ML, um modelo é treinado usando um conjunto de dados. Os modelos que você cria usando o Aprendizado de Máquina geralmente não são estáticos. Conforme novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados, o modelo precisa ser readaptado. Os cenários adicionais podem exigir a aplicação de filtros de obter um subconjunto dos dados e readaptar o modelo.

Nesses cenários, uma API de programação fornece uma maneira conveniente de permitir que você ou o consumidor de suas APIs criem um cliente que pode, em uma base única ou regular, readaptar o modelo usando seus próprios dados. Em seguida, eles podem avaliar os resultados da readaptação e atualizar a API do serviço Web para usar o modelo readaptado recentemente.

##Como readaptar: o processo ponta a ponta  

Para começar, o processo envolve os seguintes componentes: um Teste de Treinamento e um Experimento Preditivo publicado como um serviço Web. Para habilitar a readaptação de um modelo treinado, o Teste de Treinamento também deve ser publicado como um serviço Web com a saída de um modelo treinado. Isso permite o acesso de API para o modelo de readaptação.

O processo para configurar a readaptação envolve as seguintes etapas:

![Visão geral do processo readaptação][1]

Diagrama 1: visão geral do processo readaptação

## Criar um teste de treinamento
 
Para este exemplo, você usará "Amostra 5: Treinar, testar, avaliar para classificação binária: conjunto de dados adulto" das amostras do Aprendizado de Máquina do Microsoft Azure.
	
Para criar o experimento:

1.	Entre no Estúdio de Aprendizado de Máquina do Microsoft Azure.
2.	No canto inferior direito do painel, clique em **Novo**.
3.	Nas Amostras da Microsoft, selecione a Amostra 5.
4.	Para renomear o teste, na parte superior da tela do experimento, selecione o nome do teste "Amostra 5: Treinar, testar, avaliar para classificação binária: conjunto de dados adulto".
5.	Tipo de modelo de censo.
6.	Na parte inferior da tela do experimento, clique em **Executar**.
7.	Clique em **Configurar o Serviço Web** e selecione **Readaptação do Serviço Web**.

 	![Experimento inicial.][2]

Diagrama 2: Experimento inicial.

## Criar um Experimento de Pontuação e publicar como um Serviço Web  

Em seguida, crie um Experimento Predicativo.

1.	Na parte inferior da tela do experimento, clique em **Configurar o Serviço Web** e selecione **Serviço Web Preditivo**. Isso salva o modelo como um Modelo Treinado e adiciona os módulos de Entrada e Saída do serviço Web.
2.	Clique em **Executar**.
3.	Após a conclusão da execução do experimento, clique em **Implantar o Serviço Web [Clássico]**. Implanta o Experimento Predicativo como um serviço Web clássico.

## Implantar o Teste de Treinamento como um serviço Web de treinamento

Para haver a readaptação do modelo treinado, você deve implantar o Teste de Treinamento que você criou como um Serviço Web de Readaptação. Este serviço Web precisará de um módulo de Saída do Serviço Web conectado ao [Modelo de Treino][train-model] para poder produzir novos modelos treinados.

1. Para voltar para o teste de treinamento, clique no ícone de Experimentos no painel esquerdo, em seguida, clique no experimento chamado Modelo de Censo.
2. Na caixa de pesquisa Pesquisar Itens do Experimento, digite Serviço da Web.
3. Arraste um módulo Entrada do Serviço Web para a tela do experimento e conecte sua saída ao módulo Limpar Dados Ausentes.
4. Arraste dois módulos *Saída do Serviço Web* para a telas do experimento. Conecte a saída do módulo *Modelo de Treinamento* a um e a saída do módulo *Modelo de Avaliação* ao outro. A saída do serviço Web para Modelo de Treinamento nos dará o novo modelo treinado. A saída anexada a Modelo de Avaliação retornará a saída do Modelo de Avaliação do módulo.
5. Clique em **Executar**.
6. Na parte inferior da tela do experimento, clique em **Configurar o Serviço Web** e selecione **Serviço Web Preditivo**. Essa ação implantará o Teste de Treinamento como um Serviço Web que produz um modelo treinado e resultados de avaliação do modelo. O **Painel** do Serviço Web é exibido com a Chave de API e a página de ajuda da API para a Execução em Lotes. Apenas o método de Execução em Lotes pode ser usado para criar Modelos Treinados.
  
Depois de concluir a execução do experimento, o fluxo de trabalho resultante deve ser conforme mostrado abaixo:

![Fluxo de trabalho resultante após a execução.][4]

Diagrama de 3: Fluxo de trabalho resultante após a execução.

## Adicionar um novo ponto de extremidade
 
O Serviço Web Preditivo implantado é o ponto de extremidade de pontuação padrão. Os pontos de extremidade padrão são mantidos em sincronização com o experimento de origem e os experimentos de pontuação; portanto, o modelo treinado para o ponto de extremidade padrão não pode ser substituído.

Para criar um novo ponto de extremidade de pontuação, no Serviço da Web Preditivo que possa ser atualizado com o modelo treinado:

>[AZURE.NOTE] Certifique-se de que você está adicionando o ponto de extremidade ao serviço Web de previsão e não ao serviço da Web de treinamento. Se você tiver implantado corretamente um serviço Web de previsão e um serviço da Web de treinamento, você verá dois serviços Web separados listados. O serviço Web de previsão deve terminar com "[exp. preditivo]".

1. Faça logon no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. No menu esquerdo, clique em **Aprendizado de Máquina**.
3. Em Nome, clique em seu espaço de Em Nome, clique em seu espaço de trabalho e, em seguida, clique em **Serviços Web**.
4. Em Nome, clique em **Modelo de Censo [exp. preditivo]**.
5. Na parte inferior da página, clique em **Adicionar Ponto de Extremidade**. Para saber mais sobre a adição de pontos de extremidade, veja [Criação de pontos de extremidade](machine-learning-create-endpoint.md).

Você também pode adicionar pontos de extremidade de pontuação usando o código de exemplo fornecido neste [repositório github](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

## Você quer readaptar o modelo com novos dados

Para chamar as APIs de Readaptação:

1. Crie um Aplicativo de Console do C# no Visual Studio (Novo->Projeto->Área de Trabalho do Windows->Aplicativo de Console).
2. Copie o código C# de exemplo da página de ajuda da API do Serviço Web de Treinamento para a execução em lotes e o colamos no arquivo Program.cs, certificando de que o namespace permanece intacto.
3. O código de exemplo tem comentários que indicam as partes do código que você deve atualizar.
4. Ao especificar o local de saída na carga de solicitação, a extensão do arquivo especificado em *RelativeLocation* deve ser alterada do arquivo csv para ilearner. Veja os exemplos a seguir.

		Outputs = new Dictionary<string, AzureBlobDataReference>()
		{
			{
				"output1",
				new AzureBlobDataReference()
				{
					ConnectionString = "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==",
					RelativeLocation = "mycontainer/output1results.ilearner"
				}
			},
		},

>[AZURE.NOTE] Os nomes de seus locais de saída podem ser diferentes neste passo a passo com base na ordem em que você adicionou os módulos de saída do serviço Web. Como você configura esse Teste de Treinamento com duas saídas, os resultados incluem informações de local de armazenamento para ambos.

### Atualize as informações do Armazenamento do Azure

O código de exemplo de BES carrega um arquivo de uma unidade local (por exemplo "C:\\temp\\CensusIpnput.csv") para o armazenamento do Azure, processa e grava os resultados de volta para o armazenamento do Azure.

Para cumprir essa tarefa, você deve recuperar o nome da conta de Armazenamento, a chave e as informações de contêiner do Portal Clássico do Azure para a sua conta de Armazenamento e, em seguida, atualizar os valores correspondentes no código.

Você também deve garantir que o arquivo de entrada está disponível no local especificado no código.

![Saída da readaptação][6]

Diagrama 4:Saída da readaptação.

## Avaliar os resultados da readaptação
 
Quando você executa o aplicativo, a saída inclui o token SAS e a URL necessários para acessar os resultados da avaliação.

Você pode ver os resultados do desempenho do modelo readaptado ao combinar *BaseLocation*, *RelativeLocation* e *SasBlobToken* dos resultados de saída para *output2* (como mostrado na imagem de readaptação anterior) e colando a URL completa na barra de endereço do navegador.

Examine os resultados para determinar se o modelo treinado recentemente executa bem o suficiente para substituir o existente.

## Atualizar modelo treinado do ponto de extremidade adicionado

Para concluir o processo de novos treinamentos, você deve atualizar o modelo treinado do novo ponto de extremidade que você adicionou.

* Se tiver adicionado um novo ponto de extremidade usando o portal do Azure, você poderá clicar no nome do novo do ponto de extremidade no portal do Azure e no link **UpdateResource** para obter a URL de que precisa para atualizar o modelo do ponto de extremidade.
* Se você tiver adicionado o ponto de extremidade usando o código de exemplo, isso inclui o local da URL de Ajuda identificado pelo valor *HelpLocationURL* na saída.

Para recuperar a URL do caminho:

1. Copie e cole a URL no navegador.
2. Clique no link Atualizar Recurso.
3. Copie a URL POST da solicitação PATCH. Por exemplo:

		PATCH URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Agora você pode usar o modelo treinado para atualizar o ponto de extremidade de pontuação criado anteriormente.

O código de exemplo a seguir mostra como usar *BaseLocation*, *RelativeLocation*, *SasBlobToken* e a URL do PATCH para atualizar o ponto de extremidade.

	private async Task OverwriteModel()
	{
		var resourceLocations = new
		{
			Resources = new[]
			{
				new
				{
					Name = "Census Model [trained model]",
					Location = new AzureBlobDataReference()
					{
						BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
						RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
						SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
					}
				}
			}
		};

		using (var client = new HttpClient())
		{
			client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

			using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
			{
				request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
				HttpResponseMessage response = await client.SendAsync(request);

				if (!response.IsSuccessStatusCode)
				{
					await WriteFailedResponse(response);
				}

				// Do what you want with a successful response here.
			}
		}
	}

*apiKey* e *endpointUrl* podem ser obtidos do painel do ponto de extremidade para esta chamada.

O valor do parâmetro *Nome* em *Recursos* deve corresponder ao Nome do Recurso do Modelo Treinado Salvo no Experimento Preditivo. Para obter o Nome do Recurso:

1. Faça logon no [portal Clássico do Azure](https://manage.windowsazure.com).
2. No menu esquerdo, clique em **Aprendizado de Máquina**.
3. Em Nome, clique em seu espaço de Em Nome, clique em seu espaço de trabalho e, em seguida, clique em **Serviços Web**.
4. Em nome, clique em **Modelo de Censo [exp. preditivo]**.
5. Clique no novo ponto de extremidade adicionado.
6. No painel do ponto de extremidade, clique em *Atualizar Recurso*.
7. Na página Documentação da API de Atualizar Recurso para o serviço web, você pode encontrar o **Nome do Recurso** em **Recursos Atualizáveis**.

Se o token SAS expirar antes de você terminar de atualizar o ponto de extremidade, execute um GET com a Id do Trabalho para obter um novo token.

Quando o código é executado com êxito, o novo ponto de extremidade deve começar a usar o modelo recuperado em aproximadamente 30 segundos.

##Resumo  
Usando as APIs de Readaptação, você pode atualizar o modelo treinado de um Serviço Web preditivo ao habilitar cenários como:

* Readaptação de modelo periódico com novos dados.
* A distribuição de um modelo para os clientes com o objetivo de permitir que eles recuperem o modelo usando seus próprios dados.

## Próximas etapas
[Solução de problemas de readaptação de um serviço Web clássico de Aprendizado de Máquina do Azure](machine-learning-troubleshooting-retraining-models.md)

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0914_2016-->