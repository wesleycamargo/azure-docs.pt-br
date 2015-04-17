<properties 
	pageTitle="Converter um experimento de treinamento do Aprendizado de Máquina em um experimento de pontuação | Azure" 
	description="Como converter um experimento de treinamento do Aprendizado de Máquina, usado para treinar o modelo de análise preditiva, em um experimento de pontuação que pode ser publicado como um serviço Web." 
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
	ms.date="02/10/2015" 
	ms.author="garye"/>

#Converter um experimento de treinamento do Aprendizado de Máquina em um experimento de pontuação

O Aprendizado de Máquina do Azure permite compilar, testar e implantar soluções de análise preditiva. 

Depois de criá-las e iterá-las em um *training experiment* para treinar seu modelo de análise preditivo e estar pronto para usá-lo para pontuar novos dados, será necessário preparar e otimizar sua experiência de pontuação.  Você pode publicar esse *scoring experiment* como um serviço Web do Azure para que os usuários possam enviar dados ao seu modelo e receber as previsões do modelo.

Convertendo um experimento pontuação, você estará preparando seu modelo para ser publicado como um serviço Web de pontuação.  Os usuários do serviço Web enviarão dados de entrada para seu modelo e seu modelo enviará de volta os resultados da previsão.  Assim, ao mudar para um experimento de pontuação, é recomendável ter em mente como você espera que seu modelo seja usado por outras pessoas.

O processo de conversão de um experimento de treinamento em um experimento de pontuação envolve três etapas:

1.	Salvar o modelo de aprendizado de máquina que você treinou e substituir o algoritmo de aprendizado de máquina e os módulos do **Modelo de Treinamento** salvos com seu modelo treinado.
2.	Restrinja o experimento a somente os módulos necessários para pontuação.  Um experimento de treinamento inclui vários módulos que são necessárias para treinamento, mas não são necessários quando o modelo está treinado e pronto para ser usado para pontuação.
3.	Defina onde em seu experimento você aceitará dados do usuário do serviço Web e quais dados serão retornados.

##Criar o botão Experimento de Pontuação

O botão **Criar Experimento de Pontuação** na parte inferior da tela do experimento executará as três etapas da conversão do seu experimento de treinamento em um experimento de pontuação:

1.	Ele salva o modelo treinado como um módulo na seção **Modelos Treinados** da paleta do módulo (à esquerda da tela do experimento) e, em seguida, substitui o algoritmo de aprendizado de máquina e os módulos de **Modelo de Treinamento** pelo o modelo treinado salvo. 
2.	Ele remove módulos que claramente não são mais necessários.  No nosso exemplo, isso inclui os módulos **Divisão**, Segundo **Modelo de Pontuação** e **Avaliar Modelo**.
3.	Ele cria a entrada do serviço Web e módulos de saída e adiciona-os nos locais padrão no seu experimento.

Por exemplo, o seguinte experimento treina um modelo de árvore de decisão de duas classes aumentada usando dados de censo de exemplo:

![Training experiment][figure1]

Os módulos nesse experimento executam basicamente quatro funções diferentes:

![Module functions][figure2]

Ao converter esse experimento de treinamento em um experimento pontuação, alguns desses módulos não são mais necessários ou têm uma finalidade diferente:

- **Dados** - Os dados deste conjunto de dados de exemplo não são usados durante a pontuação - o usuário do serviço Web fornecerá os dados a serem pontuados.  No entanto, os metadados desse conjunto de dados, tais como tipos de dados, são usados pelo modelo treinado.  Portanto, é necessário manter o conjunto de dados no experimento da pontuação para que ele pode fornecer esses metadados.

- **Preparação** - Dependendo dos dados que serão enviados para pontuação, esses módulos podem ou não ser necessários para processar os dados de entrada. 

	Por exemplo, neste exemplo, o conjunto de dados de exemplo pode ter valores ausentes e incluir colunas que não são necessárias para treinar o modelo.  Por isso, um módulo **Limpar Dados Ausentes** foi incluído para lidar com valores ausentes e um módulo **Colunas do Projeto** foi incluído para excluir essas colunas extras do fluxo de dados.  Se você souber que os dados que serão enviados para pontuação através do serviço Web não terão valores ausentes, poderá remover o módulo **Limpar Dados Ausentes**.  No entanto, como o módulo **Colunas do Projeto** ajuda a definir o conjunto de recursos sendo marcados, este módulo deve permanecer.

- **Treinar** - Depois de o modelo ter sido treinado com êxito, você salvá-lo como um módulo de modelo único treinado.  Substitua esses módulos individuais pelo modelo treinado salvo.

- **Pontuação** - Neste exemplo, o módulo de Divisão é usado para dividir o fluxo de dados em um conjunto de dados de teste e dados de treinamento.  No experimento de pontuação isso não é necessário e pode ser removido.  Da mesma forma, os módulos Segundo **Modelo de Pontuação** e o **Avaliar Modelo** são usados para comparar os resultados de dados de teste, por isso esses módulos também não são necessárias para o experimento de pontuação.  O módulo **Modelo de Pontuação** restante, contudo, é necessário para retornar um resultado de pontuação por meio do serviço Web.

Depois de clicar em **Criar Experimento de Pontuação**, nosso exemplo fica assim:	

![Converted scoring experiment][figure3]

Isso pode ser suficiente para preparar seu experimento para publicação como serviço Web.  No entanto, convém trabalhar nos detalhes específicos do seu experimento.

###Ajustar os módulos de entrada e saída

No seu experimento de treinamento, você usou um conjunto de dados de treinamento, que foram processados para deixar os dados em um formato que o algoritmo de aprendizado de máquina necessitava.  Se os dados que você espera receber por meio do serviço Web não necessitarão deste processamento, você pode mover o **módulo de entrada de serviço Web** para um nó diferente no seu experimento.

Por exemplo, **Criar Experimento de Pontuação** coloca o módulo **Entrada do serviço Web** na parte superior do fluxo de dados por padrão, como mostrado na figura acima.  No entanto, se os dados de entrada não necessitarem tal processamento, então você pode posicionar manualmente a **Entrada do serviço Web** após os módulos de processamento de dados:

![Moving the web service input][figure4]

Os dados de entrada fornecidos pelo serviço Web agora passarão diretamente para o módulo do Modelo de Pontuação sem qualquer pré-processamento.

Da mesma forma, **Criar Experimento de Pontuação** coloca o módulo de saída do serviço Web na parte inferior do seu fluxo de dados por padrão.  Neste exemplo, o serviço Web retornará ao usuário a saída do módulo do **Modelo de Pontuação**, que inclui o vetor de dados de entrada completo, além dos resultados de pontuação.

No entanto, se desejar retornar algo diferente, por exemplo, apenas os resultados da pontuação e não todo o vetor de dados de entrada, você pode então inserir um módulo **Colunas do Projeto** para excluir todas as colunas, exceto os resultados de pontuação.  Mova então o módulo **Saída do serviço Web** para a saída do módulo **Colunas do Projeto**:

![Moving the web service output][figure5]

###Adicionar ou remover módulos de processamento de dados adicionais

Se houver mais módulos no seu experimento que você sabe que não será necessário durante a pontuação, eles podem ser removidos.  Por exemplo, como mudamos o módulo de **Entrada do serviço Web** para um ponto após os módulos de processamento de dados, podemos remover o módulo **Limpar Dados Ausentes** do experimento de pontuação. 

Agora, nosso experimento de pontuação ficou assim:

![Removing additional module][figure6]

###Adicionar parâmetros de serviço Web opcionais

Em alguns casos, talvez você queira permitir que o usuário do serviço Web altere o comportamento dos módulos quando o serviço é acessado. *Web Service Parameters* permitem fazer isso.

Um exemplo comum é configurar o módulo **Leitor** para que o usuário do serviço Web publicado possa especificar outra fonte de dados quando o serviço Web é acessado.  Ou então, configurar o módulo **Gravador** para que um destino diferente possa ser especificado. 

Você pode definir os Parâmetros do Serviço Web e associá-los a um ou mais parâmetros de módulo, podendo também especificar se eles são obrigatórios ou opcionais.  O usuário do serviço Web pode então fornecer valores para esses parâmetros quando o serviço é acessado e as ações de módulo serão modificadas de acordo.

Para obter mais informações sobre Parâmetros de Serviço Web, consulte [Usando parâmetros de serviço Web do Aprendizado de Máquina do Azure
][webserviceparameters].

[webserviceparameters]: machine-learning-web-service-parameters.md


##Publicar o experimento de pontuação como um serviço Web

Agora que o experimento de pontuação foi devidamente preparado, você pode publicá-lo como um serviço Web do Azure.  Usando o serviço Web, os usuários podem enviar dados para seu modelo e o modelo retornará suas previsões.

Para obter mais informações sobre o processo completo de publicação, consulte [Publicar um serviço Web de Aprendizado de Máquina do Azure][publicar]

[publicar]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!--HONumber=49-->