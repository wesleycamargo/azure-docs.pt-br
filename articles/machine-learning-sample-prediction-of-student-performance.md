<properties title="Azure Machine Learning Sample: Prediction of student performance" pageTitle="Amostra de Aprendizado de Máquina: Prever o desempenho dos alunos | Azure" description="Uma experiência de aprendizado de máquina do Azure de exemplo para desenvolver um modelo que prevê o desempenho do aluno em testes." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Amostra de aprendizado de máquina do Azure: Previsão do desempenho do aluno

>[AZURE.NOTE]
>A [amostra de experimento] e a [amostra de conjunto de dados] associados a este modelo estão disponíveis no Estúdio AM. Consulte a seguir para obter mais detalhes.
[Amostra de experimento]: #sample-experiment
[Amostra de conjunto de dados]: #sample-dataset


##Descrição do problema e do conjunto de dados
Neste teste, nosso conjunto de dados é o conjunto de treinamento Algebra_2008_2009 do KDD Cup 2010. Esse conjunto de dados pode ser baixado do [https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp](https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp). O conjunto de dados contém arquivos de log do sistema de tutoria do aluno. Os recursos fornecidos incluem a ID do problema e uma breve descrição dele, a ID do aluno, o carimbo de data e hora e quantas tentativas o aluno fez antes de solucionar o problema da maneira correta. O problema de aprendizado é prever se um aluno resolverá um dado problema na primeira tentativa. Seguindo as regras da KDD Cup, medimos a precisão dos algoritmos de aprendizado usando o [Erro de Raiz Quadrada Média](http://en.wikipedia.org/wiki/Root-mean-square_deviation) (RMSE). O conjunto de dados original possui 8,9 milhões de registros. Para acelerar o teste, reduzimos a amostra do conjunto de dados para as primeiras 100 mil linhas. O conjunto de dados possui 23 colunas de vários tipos: numérico, categórico e carimbo de data e hora. As colunas são separadas por guias.
 
##Fluxo de trabalho de desenvolvimento
O conjunto de dados possui caracteres não ASCII que não podem ser manipulados pelo módulo **Aplicar operação R**. Antes de usar o conjunto de dados no Passau, removemos os caracteres não ASCII usando os seguintes comandos do Powershell:  

	gc algebra_2008_2009_train.txt -head 100000 | sc algebra_train_small.txt
	sc tmp.txt -Encoding ASCII -Value (gc algebra_train_small.txt)
	cat tmp.txt | %{$_ -replace "\?\?sqrt","+sqrt"} | sc algebra_train_small.txt_ascii  

O arquivo resultante algebra_train_small.txt_ascii ainda é grande, ocupa 36 M. Nós colocamos esse arquivo no armazenamento do Blob do Azure e, em seguida, carregamos o arquivo no teste usando o módulo **Leitor**. Os comandos do Powershell para colocar o arquivo no armazenamento de Blob são:  

	Add-AzureAccount
	$key = Get-AzureStorageKey -StorageAccountName <your storage account name>
	$ctxt = New-AzureStorageContext -StorageAccountName $key.StorageAccountName -StorageAccountKey $key.Primary
	Set-AzureStorageBlobContent -Container <container name in your storage account> -File "algebra_train_small.txt_ascii" -Context $ctxt

![][1]
 
Os parâmetros do módulo **Leitor** são mostrados acima. Neste exemplo, o nome da conta de armazenamento é "datascience", o arquivo do conjunto de dados algebra_train_small.txt_ascii é colocado no contêiner "sampleexperiments". A chave de conta é uma chave de acesso da conta de armazenamento do Azure. Essa chave pode ser recuperada de sua conta no portal de gerenciamento do Azure ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
 
![][2]
 
Nas etapas mostradas acima, realizamos algumas transformações para encaixar o conjunto de dados no formato que se adequará aos nossos algoritmos de aprendizado. Usamos o **Editor de Metadados** para converter a coluna de carimbo de data e hora "Horário da Primeira Transação" no formato da cadeia de caracteres. Isso é necessário para a geração de divisão de teste/treinamento. Em seguida, usando as **Colunas de Projeto**, removemos várias colunas que não serão usadas no teste. Usamos o Programa de Limpeza do Valor Ausente para substituir todos os valores ausentes por 0. Na próxima etapa, dividimos a coluna "Nome da Unidade, Nome da Seção" em duas colunas, "Nome da Unidade" e "Nome da Seção". Isso é feito usando o seguinte código R no módulo **Executar R**:  

	dataset <- maml.mapInputPort(1)
	b <- data.frame(do.call(rbind,strsplit(as.vector(dataset[,3]),",")))
	names(b) <- c("Unit Name","Section Name")
	data.set <- cbind(dataset[,1:2],b,dataset[,4:15])
	maml.mapOutputPort("data.set")  

 
As primeiras etapas do teste que incluem o carregamento de dados e as transformações iniciais, conforme mostrado acima. Após enviar os dados por mensagem, dividimos o teste em 4 ramificações. Em cada ramificação, nós testamos um conjunto diferente de recursos. Alguns dos nossos conjuntos de recursos foram previamente usados por [1]. Na primeira ramificação, representada abaixo, nosso conjunto de recursos é StudentID, Unidade  

![][3]  
![][4]
 
 
Nome da Seção, Nome do Problema, Exibição do Problema e recursos de Oportunidade, bem como a Descrição Textual do problema. Os recursos não inteiros são representados como recursos categóricos. Nessa ramificação, nós começamos removendo a coluna Dicas usando as **Colunas do Projeto**, já que a coluna Dicas não está disponível ao prever o sucesso do novo aluno. Em seguida, usamos o módulo **Dividir** para dividir o conjunto de dados no conjunto de teste e de treinamento. Como todos os exemplos possuem carimbos de data e hora, nossa divisão será baseada em tempo.  Todas as linhas com o Horário da Primeira Transação de 2008 estão no conjunto de treinamento, os de 2009 estão no conjunto de teste. Os parâmetros do módulo **Dividir** são mostrados à direita. Após a divisão, nós geramos um modelo de classificação binária usando a Árvore de Decisão Aumentada e pontuamos os dados de teste. A coluna de rótulo no módulo **Modelo de treinamento** é Corrigir a Primeira Tentativa.  

![][5]  

 
Na sequência seguinte dos módulos **Aplicar Operação Matemática** e **Estatísticas Elementares**, mostrados acima, calculamos o RMSE do modelo usando pontuações de linha e rótulos verdadeiros. Observe que para os módulos de regressão, essa métrica é calculada pelo módulo **Avaliador**. Porém, para os outros modelos, precisamos calcular essa métrica manualmente. No primeiro módulo, **Aplicar Operação Matemática**, nós calculamos a diferença entre a coluna de rótulo e a coluna de pontuação que foi gerada pelo módulo **Modelo de Pontuação**. No segundo módulo, **Aplicar Operação Matemática**, nós calculamos o quadrado dessa diferença. Em seguida, o módulo **Estatísticas Elementares** calculou o significado das diferenças quadráticas. Por fim, o último módulo, **Aplicar Operação Matemática**, é usado para calcular a raiz quadrada. Os parâmetros desses quatro módulos são mostrados abaixo.  

![][6] 
![][7]  
![][8]  
![][9] 
 
 
Na segunda ramificação, mostrada acima, também aumentamos a dimensão de tempo. Além desses recursos da primeira ramificação, usamos os nomes das últimas duas etapas do problema que é atualmente resolvido pelo usuário juntamente com sua descrição. No conjunto de dados fornecido, todas as atividades do usuário são armazenadas em ordem crescente do carimbo de data e hora. Também não são intercaladas nas atividades dos usuários do conjunto de dados fornecido; estão nomeadas inicialmente todas as linhas do primeiro usuário, em seguida, estão todas as linhas do segundo usuário e assim por diante. Dessa maneira, para localizar as últimas etapas do usuário, nós aumentamos a coluna RowID. Para um usuário fixo, essa coluna funciona como um eixo de tempo. Ao adicionar 1 nessa coluna, usando Aplicar Operação Matemática****, nós deslocamos cada linha de uma unidade de tempo para frente. Em seguida, usamos o módulo **Juntar** para juntar o conjunto de dados original ao deslocado usando RowID, StudentID e ProblemName como chaves. Como resultado, obtemos um conjunto de dados expandido onde cada linha possui um registro de t vezes e t-1 para os mesmos StudentID e ProblemName. Usamos a *Junção Externa Esquerda* para manter as linhas que não possuem etapas anteriores com os mesmos StudentID e ProblemID. Aplicamos a combinação de **Aplicar Operação Matemática** e **Juntar** mais uma vez para avançar os recursos em duas etapas. Os parâmetros exatos de **Aplicar Operação Matemática** e **Unir** são mostrados abaixo.  

![][10]  
![][11]  
![][12]  

 
Após executar dois, obtemos um número de colunas que são idênticas. Por exemplo, devido ao nosso uso do módulo Juntar, a coluna ProblemName é replicada 3 vezes, para as etapas t, t-1 e t-2. Usamos o módulo **Colunas de Projeto** para remover colunas redundantes. Finalmente, como usamos a *Junção Externa Esquerda*, algumas das linhas geradas pelo operador Juntar podem ter valores ausentes. Usamos o **Programa de Limpeza de Valores Ausentes** para substituir todos os valores ausentes pela cadeira de caracteres "0". Os parâmetros do **Programa de Limpeza de Valores Ausentes** são mostrados acima.  

Após calcular o novo conjunto de recursos, o fluxo de trabalho da segunda ramificação é idêntico ao da primeira ramificação.  

![][13]
 
Na terceira ramificação, além dos recursos usados na segunda ramificação, também usamos recursos quadráticos e cúbicos que são concatenações dos recursos originais da primeira ramificação. Os recursos quadráticos e cúbicos são calculados usando o módulo **Executar Operação R** com o código R mostrado acima. Após calcular o novo conjunto de recursos, continuamos com o treinamento, pontuação e avaliação das mesmas maneiras como nas duas primeiras ramificações.  

Na quarta ramificação, usamos recursos que são completamente diferentes daqueles presentes nas três primeiras ramificações. Para cada StudentID, calculamos o valor médio de Corrigir a Primeira Tentativa até (mas não incluindo) o tempo t. Denotamos esse valor como CFA(StudentID,t). Analogamente, denotamos por Hints(StudentID,t) o valor médio das Dicas para um StudentID fixo até (mas não incluindo) o tempo t. Para acelerar a computação dessas médias, em vez de considerar todo o conjunto de dados, apenas observamos os 10 registros mais recentes antes do tempo t. As definições de CFA(Nome do Problema,t), CFA(Nome da Etapa,t), CFA(Descrição,t), Dicas(Nome do Problema,t), Dicas(Nome da Etapa,t) e Dicas(Descrição,t) são semelhantes. Dando um exemplo x com o Horário da Primeira Transação=t(x) e os valores da coluna StudentID(x), Nome do Problema(x), Nome da Etapa(x) e Descrição(x), nós geramos os recursos de Dicas e os 8 CFA acima:  

	CFA(StudentID(x),t(x)), CFA(Problem Name(x),t(x)), CFA(Step Name(x),t(x)), CFA(Description(x),t(x)), 
	Hints(StudentID(x),t(x)), Hints(Problem Name(x),t(x)), Hints(Step Name(x),t(x)), Hints(Description(x),t(x))  

![][14]  

 
Analogamente, também calculamos 8 recursos adicionais de CFA e Dicas usando as concatenações de StudentID e Nome do Problema, Nome do Problema e Nome da Etapa, StudentId e UnitName, StudentID e Descrição do Problema. No geral, obtemos 16 recursos que são usados para prever o valor da coluna Corrigir a Primeira Tentativa. O cálculo desses 16 recursos é feito usando o código R dentro do módulo **Executar Operação R**. Esse código é longo e tedioso, mas altamente otimizado. Após calcular esses recursos, nós removemos algumas colunas auxiliares que foram incluídas pelo código R. Para essa finalidade, usamos o módulo **Colunas de Projeto**. O fluxo de trabalho concluído para o cálculo dos recursos na quarta ramificação é mostrado acima. Após calcular o novo conjunto de recursos, nós continuamos com o treinamento, pontuação e avaliação da mesma maneira feita nas três primeiras ramificações.  

Após calcular os valores de RMSE em todas as quatro ramificações, coletamos os resultados usando o módulo **Adicionar Linha**. Além disso, geramos anotações usando o módulo **Executar R**. O fluxo de trabalho dessa parte final do teste é ilustrado abaixo.  

![][15]  
 
 
A saída final do teste é a tabela a seguir, a primeira coluna é o nome do conjunto de recursos e a segunda linha é o RSME, conforme medido nos exemplos de teste:  

![][16]
 

Concluímos que quarto conjunto de recursos forneceu o menor RMSE.  

##Referências
H.-F. Yu et al. Feature Engineering and Classifier Ensemble for KDD Cup 2010. KDD Cup 2010 Workshop, 2010.



## Amostra de teste

A seguinte amostra de teste associada a esse modelo está disponível no Estúdio AM na seção **TESTES** sob a guia **AMOSTRAS**.

> **Amostra de teste - Desempenho do aluno - Desenvolvimento**


## Conjunto de dados de amostra

O seguinte conjunto de dados de exemplo usado por essa experiência está disponível no Estúdio AM na paleta de módulo em **Conjuntos de dados salvos**.

###student_performance.txt
[AZURE.INCLUDE [machine-learning-sample-dataset-student-performance](../includes/machine-learning-sample-dataset-student-performance.md)]



[1]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-1.jpg
[2]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-2.jpg
[3]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-3.jpg
[4]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-4.jpg
[5]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-5.jpg
[6]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-6.jpg
[7]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-7.jpg
[8]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-8.jpg
[9]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-9.jpg
[10]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-10.jpg
[11]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-11.jpg
[12]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-12.jpg
[13]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-13.jpg
[14]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-14.jpg
[15]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-15.jpg
[16]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-16.jpg
