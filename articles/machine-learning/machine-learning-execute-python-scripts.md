<properties 
	pageTitle="Executar scripts de aprendizado de máquina do Python | Microsoft Azure" 
	description="Descreve princípios de design subjacentes ao suporte a Python no Aprendizado de Máquina do Azure e seus cenários de uso básico, recursos e limitações." 
	keywords="aprendizado de máquina em python, pandas, python pandas, scripts python, executar scripts python"
	services="machine-learning"
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2015" 
	ms.author="bradsev" />


# Executar scripts Python de aprendizado de máquina no Estúdio de Aprendizado de Máquina do Azure

Este tópico descreve os princípios de design subjacentes ao suporte atual para scripts Python no Aprendizado de Máquina do Azure. Os principais recursos também são descritos, incluindo o suporte à importação de código existente, a exportação de visualizações e, por fim, algumas das limitações e trabalhos em andamento são discutidos.

O [Python](https://www.python.org/) é uma ferramenta indispensável no conjunto de ferramentas de muitos cientistas de dados. Ele tem uma sintaxe elegante e concisa, suporte a várias plataformas, uma ampla coleção de bibliotecas eficientes e ferramentas maduras de desenvolvimento. O Python está sendo usado em todas as fases do fluxo de trabalho que geralmente são usadas na modelagem do aprendizado de máquina, começando pela ingestão e processamento dados, passando pela construção de recursos e treinamento e chegando à validação e implantação de modelos.

O Estúdio de Aprendizado de Máquina do Azure dá suporte à inclusão de scripts de Python em várias partes de um experimento de aprendizado de máquina, bem como à sua publicação integrada como serviços Web dimensionáveis e operacionalizados no Microsoft Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## Princípios de design de scripts Python no Aprendizado de Máquina
A principal interface para Python no Estúdio de Aprendizado de Máquina do Azure é o módulo [Executar Script Python][execute-python-script] mostrado na Figura 1.

![image1](./media/machine-learning-execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/machine-learning-execute-python-scripts/embedded-machine-learning-python-script.png)

Figura 1. O módulo **Executar Script Python**.

O módulo [Executar Script Python][execute-python-script] aceita até três entradas e produz até duas saídas (discutidas abaixo), assim como seu módulo análogo para R, o [Executar Script R][execute-r-script]. O código Python a ser executado é inserido na caixa de parâmetro como uma função de ponto de entrada denominada especialmente chamada `azureml_main`. Estes são os principais princípios de design usados para implementar este módulo:

1.	*Devem ser expressões idiomáticas para usuários de Python.* A maioria dos usuários de Python fatora seu código como funções dentro de módulos, de modo que colocar muitas instruções executáveis em um módulo de nível superior é relativamente raro. Como resultado, a caixa de script também recebe uma função de Python especialmente nomeada em vez de apenas uma sequência de instruções. Os objetos expostos na função são tipos de biblioteca do Python padrão, como estruturas de dados [Panda](http://pandas.pydata.org/) e matrizes [NumPy](http://www.numpy.org/).
2.	*Deve ter alta fidelidade entre execuções locais e na nuvem.* O back-end usado para executar o código Python se baseia no [Anaconda](https://store.continuum.io/cshop/anaconda/) 2.1, uma distribuição científica multiplataforma do Python amplamente utilizada. Ele vem com quase 200 dos pacotes de Python mais comuns. Portanto, um cientista de dados pode depurar e qualificar seu código em seu ambiente Anaconda local compatível com o Aprendizado de Máquina do Azure usando ambientes de desenvolvimento existentes como [IPython](http://ipython.org/) notebook ou [Python Tools para Visual Studio] e executá-lo como parte de um experimento do Aprendizado de Máquina do Azure com confiança elevada. Além disso, o ponto de entrada `azureml_main` é uma função vanilla Python e pode ser criado sem um código específico ou o SDK do Aprendizado de Máquina do Azure instalado.
3.	*Deve ser totalmente combinável com outros módulos do Aprendizado de Máquina do Azure.* O módulo [Executar Script Python][execute-python-script] aceita, como entradas e saídas, conjuntos de dados padrão do Aprendizado de Máquina do Azure. A estrutura subjacente preenche do modo transparente e eficiente as lacunas entre os tempos de execução do Aprendizado de Máquina do Azure e do Python (dando suporte a recursos como valores ausentes). O Python, portanto, pode ser usado em conjunto com fluxos de trabalho existentes do Aprendizado de Máquina do Azure, incluindo aqueles que se chamam R e SQLite. Assim, é possível imaginar fluxos de trabalho que:
  * usam Python e Pandas para pré-processando e limpeza de dados, 
  * alimentam os dados para uma transformação de SQL, unindo vários conjuntos de dados para formar recursos, 
  * treinar modelos usando o amplo conjunto de algoritmos do Aprendizado de Máquina do Azure e 
  * avaliar e pós-processar os resultados usando R.


## Cenários de uso básico de Aprendizado de Máquina para scripts Python
Nesta seção, avaliamos alguns dos usos básicos do módulo [Executar Script Python][execute-python-script]. Como mencionado anteriormente, quaisquer entradas do módulo Python são expostas como estruturas de dados Pandas. Mais informações a Pandas do Python e como ela pode ser usada para manipular dados com eficiência e eficácia podem ser encontradas em *Python for Data Analysis* (Sebastopol, CA.: O'Reilly, 2012), de W. McKinney. A função deve retornar uma única estrutura de dados Pandas empacotada dentro de uma [sequência](https://docs.python.org/2/c-api/sequence.html) Python, como uma tupla, lista ou matriz NumPy. O primeiro elemento dessa sequência é retornado na primeira porta de saída do módulo. O esquema é mostrado na Figura 2.

![image3](./media/machine-learning-execute-python-scripts/map-of-python-script-inputs-outputs.png)

Figura 2. Mapeamento de portas de entrada para parâmetros e valor retornado à porta de saída.

Uma semântica mais detalhada de como as portas de entrada são mapeadas para parâmetros da função `azureml_main` é mostrada na Tabela 1:

![image1T](./media/machine-learning-execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabela 1. Mapeamento de portas de entrada para parâmetros de função.

Observe que o mapeamento entre as portas de entrada e os parâmetros da função é posicional, ou seja, a primeira porta de entrada conectada é mapeada para o primeiro parâmetro da função e a segunda entrada (se conectada) é mapeada para o segundo parâmetro da função.

## Conversão de tipos de entrada e saída
Conforme explicado anteriormente, conjuntos de dados de entrada no Aprendizado de Máquina do Azure são convertidos em estruturas de dados Pandas, e as estruturas de dados de saída são convertidas novamente para conjuntos de dados do Aprendizado de Máquina do Azure. As seguintes conversões são realizadas:

1.	Colunas numéricas e de cadeias de caracteres são convertidas da forma como estão e os valores ausentes em um conjunto de dados são convertidos para valores “NA” no Pandas. A mesma conversão ocorre na direção contrária (valores NA no Pandas são convertidos em valores ausentes no Aprendizado de Máquina do Azure).
2.	Vetores de índice no Pandas não têm suporte no Aprendizado de Máquina do Azure e todas as estruturas de dados de entrada na função Python sempre terão um índice numérico de 64 bits de 0 até o número de linhas menos 1. 
3.	Conjuntos de dados do Aprendizado de Máquina do Azure não podem ter nomes de coluna duplicados e nomes de coluna que não são cadeias de caracteres. Se uma estrutura de dados de saída contiver colunas não numéricas, a estrutura chama `str` nos nomes de coluna. Da mesma forma, quaisquer nomes de coluna duplicados são danificados automaticamente para assegurar que os nomes sejam exclusivos. O sufixo (2) é adicionado à primeira duplicata, (3) à segunda duplicata, etc.

## Operacionalizando scripts Python
Quaisquer módulos [Executar Script Python][execute-python-script] em um experimento de pontuação são chamados quando publicados como um serviço Web. Por exemplo, a Figura 3 mostra um experimento de pontuação que contém o código para avaliar uma única expressão de Python.

![image4](./media/machine-learning-execute-python-scripts/figure3a.png)

![image5](./media/machine-learning-execute-python-scripts/python-script-with-python-pandas.png)

Figura 3. Serviço Web para avaliar uma expressão de Python.

Um serviço Web criado por meio deste experimento utiliza como entrada uma expressão de Python (como uma cadeia de caracteres), a envia para o intérprete de Python e retorna uma tabela que contém a expressão e o resultado avaliado.

## Importando módulos de script Python existentes
Um caso de uso comum para muitos cientistas de dados é incorporar scripts Python existentes a experimentos do Aprendizado de Máquina do Azure. Em vez de concatenar e colar todo o código em uma única caixa de script, o módulo [Executar Script Python][execute-python-script] aceita uma terceira porta de entrada à qual um arquivo zip que contém os módulos de Python pode ser conectado. O arquivo é então descompactado pela estrutura de execução no tempo de execução e o conteúdo é adicionado ao caminho da biblioteca do interpretador de Python. A função do ponto de entrada `azureml_main` pode, então, importar esses módulos diretamente.

Por exemplo, considere o arquivo Hello.py que contém uma função simples "Hello, World".

![image6](./media/machine-learning-execute-python-scripts/figure4.png)

Figura 4. Função definida pelo usuário.

Em seguida, podemos criar um arquivo Hello.zip contendo o Hello.py:

![image7](./media/machine-learning-execute-python-scripts/figure5.png)

Figura 5. Arquivo zip que contém o código Python definido pelo usuário.

Em seguida, carregue-o como um conjunto de dados no Estúdio de Aprendizado de Máquina do Azure. Depois, podemos criar e executar um experimento simples que usa o módulo:

![image8](./media/machine-learning-execute-python-scripts/figure6a.png)

![image9](./media/machine-learning-execute-python-scripts/figure6b.png)

Figura 6. Exemplo de experimento com código Python definido pelo usuário carregado como um arquivo zip.

A saída do módulo mostra que o arquivo zip foi descompactado e a função `print_hello` realmente foi executada. ![image10](./media/machine-learning-execute-python-scripts/figure7.png)
 
Figura 7. Função definida pelo usuário em uso dentro do módulo [Executar Script Python][execute-python-script].

## Trabalhando com visualizações
Gráficos plotados criado usando o MatplotLib que podem ser visualizados no navegador podem ser retornados pelo módulo [Executar Script Python][execute-python-script]. Mas os gráficos não são redirecionados automaticamente para imagens como é feito quando R é usado. Assim, o usuário precisa salvar explicitamente qualquer gráfico em arquivos PNG se eles precisarem ser retornados para o Aprendizado de Máquina do Azure.

Para gerar imagens do MatplotLib, você precisa realizar o procedimento a seguir:

* alternar o back-end para "AGG" em vez do processador padrão com base em Qt 
* criar um novo objeto de figura 
* obter o eixo e gerar todos os gráficos nele 
* salvar a figura em um arquivo PNG 

Esse processo é ilustrado na Figura 8 abaixo, que cria uma matriz de plotagem de dispersão usando a função scatter\_matrix do Pandas.
 
![image1v](./media/machine-learning-execute-python-scripts/figure-v1-8.png)

Figura 8. Salvando figuras do MatplotLib em imagens.



A Figura 9 mostra um experimento que usa o script mostrado acima para retornar gráficos de plotagem através da segunda porta de saída.

![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9a.png)
	 
![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9b.png)

Figura 9. Visualizando gráficos de plotagem gerados por meio do código Python.

Observe que é possível retornar diferentes figuras salvando-as como diferentes imagens, o tempo de execução do Aprendizado de Máquina do Azure seleciona todas as imagens e as concatena para visualização.


## Exemplos avançados
O ambiente Anaconda instalado no Aprendizado de Máquina do Azure contém pacotes comuns, como NumPy, SciPy e Scikits-Learn, que podem ser usados efetivamente para várias tarefas de processamento de dados em um pipeline típico de aprendizado de máquina. Por exemplo, o experimento e o script a seguir ilustram o uso de mecanismos de ensemble learning em Scikits-Learn para calcular pontuações de importância de recursos para um conjunto de dados. As pontuações podem ser usadas posteriormente para executar uma seleção supervisionada de recursos antes de alimentar outro modelo de aprendizado de máquina.

A função de Python para calcular as pontuações de importância e ordenar os recursos com base nelas é mostrada abaixo:

![image11](./media/machine-learning-execute-python-scripts/figure8.png)

Figura 10. Função para classificar recursos por pontuação. Depois, o experimento seguinte calcula e retorna as pontuações de importância dos recursos do conjunto de dados "Pima Indian Diabetes" no Aprendizado de Máquina do Azure:

![image12](./media/machine-learning-execute-python-scripts/figure9a.png) ![image13](./media/machine-learning-execute-python-scripts/figure9b.png)
	
Figura 11. Experimento para classificar recursos no conjunto de dados Pima Indian Diabetes.

## Limitações 
O módulo [Executar Script Python][execute-python-script] atualmente tem as seguintes limitações:

1.	*Execução em modo seguro.* O tempo de execução do Python atualmente é feito no modo seguro e, consequentemente, não permite o acesso à rede ou ao sistema de arquivos local de maneira persistente. Todos os arquivos salvos localmente são isolados e excluídos após a conclusão do módulo. O código Python não pode acessar a maioria das pastas do computador em que é executado, com exceção do diretório atual e seus subdiretórios.
2.	*Falta de suporte para desenvolvimento e depuração sofisticados.* O módulo Python atualmente não dá suporte a recursos de IDE, como IntelliSense e depuração. Além disso, se o módulo falhar no tempo de execução, o rastreamento de pilha do Python completo está disponível, mas deve ser visualizado no log de saída do módulo. Atualmente, recomendamos que os usuários desenvolvem e depurem seus scripts Python em um ambiente como IPython e depois importem o código para o módulo.
3.	*Saída de estruturas de dados únicas.* O ponto de entrada do Python para retornar somente uma estrutura de dados como saída. Atualmente, não é possível retornar objetos arbitrários do Python como modelos treinados diretamente para o tempo de execução do Aprendizado de Máquina do Azure. No entanto, assim como o módulo [Executar Script R][execute-r-script], que tem a mesma limitação, é possível em muitos casos serializar objetos em uma matriz de bytes e retorná-la dentro de uma estrutura de dados.
4.	*Incapacidade de personalizar a instalação do Python*. Atualmente, a única maneira de adicionar módulos personalizados do Python é por meio do mecanismo de compactação de arquivo zip descrito anteriormente. Embora isso seja viável para pequenos módulos, é complicado para módulos grandes (especialmente aqueles com DLLs nativos) ou um grande número de módulos. 


##Conclusões
O módulo [Executar Script Python][execute-python-script] permite a um cientista de dados incorporar código Python existente a fluxos de trabalho de aprendizado de máquina hospedados na nuvem no Aprendizado de Máquina do Azure e operacionalizá-los como parte de um serviço Web. O módulo de script Python trabalha naturalmente com outros módulos do Aprendizado de Máquina do Azure e pode ser usado para uma série de tarefas, da exploração ao pré-processamento de dados, passando pela extração de recursos e chegando à avaliação e o pós-processamento dos resultados. O tempo de execução de back-end usado para a execução se baseia no Anaconda, uma distribuição do Python amplamente testada e utilizada. Isso simplifica para os usuários o processo de integrar ativos de código existentes à nuvem.

Nos próximos meses, esperamos fornecer funcionalidades adicionais para o módulo [Executar Script Python][execute-python-script], como a capacidade de treinar e operacionalizar modelos em Python e acrescentar um melhor suporte para o desenvolvimento e depuração de código no Estúdio de Aprendizado de Máquina do Azure.

## Próximas etapas

Para saber mais, consulte o [Centro de Desenvolvedores do Python](/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[Python Tools para Visual Studio]: http://aka.ms/ptvs

<!---HONumber=Oct15_HO4-->