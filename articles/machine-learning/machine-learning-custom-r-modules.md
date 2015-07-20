<properties 
	pageTitle="Criar módulos R personalizados no Aprendizado de Máquina do Azure | Microsoft Azure" 
	description="Início rápido para a criação de módulos R personalizados no Aprendizado de Máquina do Azure." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"  
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="05/07/2015" 
	ms.author="bradsev;ankarloff" />


# Criar módulos R personalizados no Aprendizado de Máquina do Azure

Este tópico descreve como criar e implantar um módulo R personalizado no Aprendizado de Máquina do Azure. Ele explica o que são módulos R personalizados e arquivos que são usados para defini-los. Ilustra como criar os arquivos que definem um módulo e como registrar o módulo para implantação em um espaço de trabalho de Aprendizado de Máquina. Os elementos e atributos usados na definição de módulo personalizado, em seguida, são descritos mais detalhadamente. Também é discutido como usar a funcionalidades e arquivos auxiliares, bem como diversas saídas.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## O que é um módulo R personalizado?
Um **módulo personalizado** é um módulo definido pelo usuário que pode ser carregado no seu espaço de trabalho e executado como parte de um experimento de Aprendizado de Máquina do Azure. Um **módulo R personalizado** é um módulo personalizado que executa uma função R definida pelo usuário. R é uma linguagem de programação para a computação estatística e gráficos que é amplamente usada por cientistas estatísticos e para implementar algoritmos estatísticos. Atualmente, R é a única linguagem com suporte em módulos personalizados, mas o suporte para idiomas adicionais será adicionado em futuras versões.

Módulos personalizados têm o **status de primeira classe** no Aprendizado de Máquina do Azure no sentido de que podem ser usados como qualquer outro módulo. Eles podem ser executados com outros módulos, incluídos em visualizações ou testes publicados. Os usuários têm controle sobre o algoritmo implementado pelo módulo, as portas de entrada e saída a ser usadas, os parâmetros de modelagem e outros vários comportamentos de tempo de execução. Um módulo personalizado só está disponível no espaço de trabalho no qual ele foi criado e não é possível publicar testes da comunidade.

## Arquivos em um módulo R personalizado
Um módulo R personalizado é definido por um arquivo .zip que contém, no mínimo, dois arquivos:

* Um **arquivo fonte** que implementa a função R exposta pelo módulo
* Um **arquivo de definição XML** que descreve a interface do módulo personalizado

Arquivos auxiliares adicionais também podem ser incluídos no arquivo .zip que fornecem funcionalidades que podem ser acessadas por meio do módulo personalizado. Essa opção é discutida abaixo.

## Exemplo de início rápido: definir, empacotar e registrar um módulo R personalizado
Este exemplo ilustra como construir os arquivos necessários para um módulo R personalizado, empacotá-los em um arquivo zip e, em seguida, registrar o módulo no espaço de trabalho de Aprendizado de Máquina. Os arquivos de exemplo e pacote de zip exemplo podem ser baixados [aqui](http://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

Considere o exemplo de um módulo **Adicionar Linhas personalizado** que modifica a implementação padrão do módulo Adicionar Linhas usado para concatenar linhas (observações) de dois conjuntos de dados (quadros de dados). O módulo Adicionar Linhas padrão acrescenta as linhas do segundo conjunto de dados de entrada ao final do primeiro usando o algoritmo rbind. A função `CustomAddRows` personalizada similarmente aceita dois conjuntos de dados, mas também aceita um parâmetro booliano de troca adicional como entrada. Se o parâmetro de troca for **FALSE**, ele retorna o mesmo conjunto de dados que a implementação padrão. Porém, se o parâmetro de troca for **TRUE**, ele acrescenta linhas do primeiro conjunto de dados de entrada ao final do segundo conjunto de dados. O arquivo que implementa a função `CustomAddRows` R exposta pelo módulo **Adicionar Linhas personalizado** contém o seguinte código R.

	CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
	{
		if (swap)
		{
			return (rbind(dataset2, dataset1));
		}
		else
		{
			return (rbind(dataset1, dataset2));
		} 
	} 

Expor essa função `CustomAddRows` como um módulo de Aprendizado de Máquina do Azure, um arquivo de definição XML deve ser criado para especificar qual deve ser a aparência e comportamento do módulo **Adicionar Linhas personalizado**.

	<!-- Defined a module using an R Script -->
	<Module name="Custom Add Rows">
	    <Owner>Microsoft Corporation</Owner>
	    <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is false, and vice versa when Swap is true.</Description>
	
	<!-- Specify the base language, script file and R function to use for this module. -->		
	    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />  
		
	<!-- Define module input and output ports -->
	<!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
	    <Ports>
			<Input id="dataset1" name="Dataset 1" type="DataTable">
				<Description>First input dataset</Description>
			</Input>
			<Input id="dataset2" name="Dataset 2" type="DataTable">
				<Description>Second input dataset</Description>
			</Input>
			<Output id="dataset" name="Dataset" type="DataTable">
				<Description>Combined dataset</Description>
			</Output>
	    </Ports>
		
	<!-- Define module parameters -->
	    <Arguments>
			<Arg id="swap" name="Swap" type="bool" >
				<Description>Swap input datasets.</Description>
			</Arg>
	    </Arguments>
	</Module>

 
Observe que o valor da **id** atributos dos elementos **Input** e **Arg** no arquivo XML devem corresponder exatamente aos nomes de parâmetro de função do código R (*dataset1*, *dataset2* e *troca* no exemplo). Da mesma forma, o valor do atributo **entryPoint** do elemento **idioma** deve corresponder exatamente ao nome da função no script R (*CustomAddRows* no exemplo). Por outro lado, o atributo **id** para os elementos de **Saída** não correspondem a todas as variáveis no script R. Quando mais de uma saída for necessária, basta retornar uma lista da função R com resultados colocados na mesma ordem em que as saídas são declaradas no arquivo XML.

Salve esses dois arquivos como *CustomAddRows.R* e *CustomAddRows.xml* e compacte-os em um arquivo *CustomAddRows.zip*.

Para registrá-los no seu espaço de trabalho de Aprendizado de Máquina, vá para seu espaço de trabalho no Estúdio de Aprendizado de Máquina, clique no botão **+NOVO** na parte inferior e escolha **MÓDULO -> DO PACOTE ZIP** para carregar o novo módulo Adicionar Linhas personalizado.

![](http://i.imgur.com/RFJhCls.png)

O módulo **Adicionar Linhas personalizado** agora está pronto para ser acessado pelos seus testes de Aprendizado de Máquina.

## Elementos no arquivo de definição XML

### Elementos de módulo
O elemento **Módulo** é usado para definir um módulo personalizado no arquivo XML. Vários módulos podem ser definidos em um arquivo XML usando vários elementos de **módulo**. Cada módulo no espaço de trabalho deve ter um nome exclusivo. Registre um módulo personalizado com o mesmo nome de um módulo personalizado existente e o módulo existente será substituído pelo novo. Módulos personalizados, no entanto, podem ser registrados com o mesmo nome que um módulo existente do Aprendizado de Máquina do Azure e ele aparecerá na categoria Personalizada da paleta de módulo.

	<Module name="Custom Add Rows" isDeterministic="false"> 
		<Owner>Microsoft Corporation</Owner>
		<Description>Appends one dataset to another...</Description>/> 


Dentro do elemento **Módulo**, você pode especificar um elemento **Proprietário** opcional incorporado no módulo, bem como um elemento de **Descrição** que é o texto exibido na ajuda rápida para o módulo e quando você passa o mouse sobre o módulo na interface de usuário de Aprendizado de Máquina.

**Regras para limites de caracteres nos elementos de Módulo**:

* O valor do atributo **nome** no elemento **Módulo** não deve exceder 64 caracteres de comprimento. 
* O conteúdo do elemento **Descrição** não deve exceder 128 caracteres de comprimento.
* O conteúdo do elemento **Proprietário** não deve exceder 32 caracteres de comprimento.

** Indicando se os resultados do módulo são deterministas ou não deterministas

Por padrão, todos os módulos são considerados deterministas. Ou seja, dado um conjunto inalterável de parâmetros, o módulo deve retornar os mesmos resultados sempre que for executado. Devido a esse comportamento, o Studio de Aprendizado de Máquina do Azure não executa novamente os módulos marcados como deterministas, a menos que um parâmetro ou os dados de entrada tenham mudado. Resultados em cache são retornados, resultando na execução mais rápida do teste.

No entanto, se o módulo usar uma função que retorne resultados diferentes cada vez que ele é executado – por exemplo, RAND ou uma função que retorne a data ou hora atual – você pode especificar o módulo como sendo não determinista definindo o atributo **isDeterministic** opcional para **false**. O módulo será executado novamente sempre que o teste for executado, mesmo que o módulo de entrada e os parâmetros não tenham mudado.

### Definição de linguagem
O elemento **Idioma** em seu arquivo de definição XML é usado para especificar o idioma do módulo personalizado. Atualmente, R é a única linguagem com suporte. O valor do atributo **sourceFilede** deve ser o nome do arquivo R que contém a função a ser chamada quando o módulo é executado. Esse arquivo deve fazer parte do pacote de zip. O valor do atributo **entryPoint** é o nome da função que está sendo chamada e deve corresponder a uma função válida definida no arquivo de origem.

	<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### Portas
As portas de entrada e saída de um módulo personalizado são especificadas nos elementos filho da seção **Portas** do arquivo de definição XML. A ordem desses elementos determina o layout visualizado (UX) pelos usuários. A primeira **entrada** ou **saída** filha listada no elemento **portas** do arquivo XML será a porta de entrada mais à esquerda no UX do Aprendizado de Máquina. Cada porta de entrada e saída pode ter um elemento filho de **Descrição** opcional que especifica o texto mostrado quando um usuário passar o cursor do mouse sobre a porta na interface de usuário de Aprendizado de Máquina.

**Regras de portas**:

* O número máximo de **portas de entrada e saída** é de 8 para cada.

### Elementos de entrada
Portas de entrada permitem aos usuários passar dados para a função R e o espaço de trabalho. Os **tipos de dados** com suporte para portas de entrada são os seguintes:

**DataTable:** esse tipo é passado para a função R como um data.frame. Na verdade, qualquer tipo (por exemplo, arquivos CSV ou arquivos ARFF) têm suporte no Aprendizado de Máquina e que é compatível com **DataTable** é convertido para um data.frame automaticamente.

		<Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
        	<Description>Input Dataset 1</Description>
       	</Input>

O atributo de **id** associado a cada porta de entrada **DataTable** deve ter um valor exclusivo e esse valor deve ser igual combinar com o seu parâmetro de nome correspondente na função R. As portas **DataTable** opcionais não são passadas como entrada em um teste terão o valor **NULL** passados para a função de R, e as portas zip opcionais serão ignoradas se a entrada não estiver conectada. O atributo **IsOptional** é opcional para os tipos **DataTable** e **Zip** e *false* por padrão.
	   
**ZIP:** módulos personalizados podem aceitar um arquivo zip como entrada. Essa entrada é descompactada para o diretório de trabalho R da sua função

		<Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
        	<Description>Zip files will be extracted to the R working directory.</Description>
       	</Input>

Para módulos personalizados de R, a ID de uma porta Zip não precisa corresponder a nenhum parâmetro da função R, uma ve que o arquivo zip é extraído automaticamente para o diretório de trabalho R.

**Regras de entrada:**

* O valor do atributo **id** do elemento **Entrada** deve ser um nome de variável R válido.
* O valor do atributo **id** do elemento **Entrada** não deve ter mais de 64 caracteres de comprimento.
* O valor do atributo **nome** do elemento **Entrada** não deve ter mais de 64 caracteres de comprimento.
* O conteúdo do elemento **Descrição** não deve ter mais de 128 caracteres de comprimento
* O valor do atributo **tipo** do elemento **Entrada** deve ser *Zip* ou *DataTable*.
* O valor do atributo **isOptional** do elemento **Entrada** não é necessário (e é *false* por padrão quando não especificado); mas, se for especificado, deve ser *true* ou *false*.

### Elementos de saída

**Portas de saída padrão:** portas de saída são mapeadas para os valores de retorno da função R, que pode ser usada por módulos subsequentes. *DataTable* é o único tipo de porta de saída padrão com suporte no momento. (Suporte para *Aprendizes* e *Transformações* estará disponível em breve.) Uma saída *DataTable* é definida como:

	<Output id="dataset" name="Dataset" type="DataTable">
		<Description>Combined dataset</Description>
	</Output>

Para saídas em módulos R personalizados, o valor do atributo **id** não precisa corresponder a nada no script R, mas deve ser exclusivo. Para uma única saída de módulo, o valor retornado da função R deve ser um *data.frame*. Para mais de um objeto de um tipo de dados com suporte de saída, as portas de saída apropriada precisam ser especificadas no arquivo de definição XML e os objetos precisam ser retornados como uma lista. Os objetos de saída serão atribuídos a portas da esquerda para a direita, refletindo a ordem na qual os objetos são colocados na lista retornada de saída.
 
Por exemplo, se você quiser resultados de dataset, Dataset1 e Dataset2 como saída, dataset, dataset1 e dataset2 da esquerda para a direita, respectivamente, então defina as portas de saída no arquivo “CustomAddRows.xml” da seguinte maneira:

Por exemplo, se você deseja modificar o módulo **Adicionar linhas personalizado** para produzir os dois conjuntos de dados originais, *dataset1* e *dataset2*, além do conjunto de dados *dataset* recém-incorporado (em ordem, da esquerda para a direita, como: *dataset*, *dataset1*, *dataset2*), então defina as portas de saída no arquivo CustomAddRows.xml da seguinte maneira:

	<Ports> 
		<Output id="dataset" name="Dataset Out" type="DataTable"> 
			<Description>New Dataset</Description> 
		</Output> 
		<Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
			<Description>First Dataset</Description> 
		</Output> 
		<Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
			<Description>Second Dataset</Description> 
		</Output> 
		<Input id="dataset1" name="Dataset 1" type="DataTable"> 
			<Description>First Input Table</Description>
		</Input> 
		<Input id="dataset2" name="Dataset 2" type="DataTable"> 
			<Description>Second Input Table</Description> 
		</Input> 
	</Ports> 


E retorne a lista de objetos em uma lista na ordem correta em “CustomAddRows.R”:

	CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
		if (swap) { dataset <- rbind(dataset2, dataset1)) } 
		else { dataset <- rbind(dataset1, dataset2)) 
		} 
	return (list(dataset, dataset1, dataset2)) 
	} 
	
**Saída de visualização:** também é possível especificar uma porta de saída do tipo *Visualização* que exibe a saída do dispositivo gráfico R e a saída do console. Essa porta não faz parte da saída da função R e não interfere na ordem de outros tipos de porta de saída. Para adicionar uma porta de visualização aos módulos personalizados, adicione um elemento de **saída** com um valor de *Visualização* para seu atributo **tipo**:

	<Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>
	
**Regras de saída:**

* O valor do atributo **id** do elemento **Saída** deve ser um nome de variável R válido.
* O valor do atributo **id** do elemento **Saída** não deve ter mais de 32 caracteres de comprimento.
* O valor do atributo **nome** do elemento **Saída** não deve ter mais de 64 caracteres de comprimento.
* O valor do atributo **tipo** do elemento **Saída** deve ser *Visualização*.

### Argumentos
Dados adicionais podem ser passados para a função R por meio de parâmetros do módulo definidos no elemento **Argumentos**. Esses parâmetros são exibidos no canto direito do painel mais à direita da interface do usuário do Aprendizado de Máquina quando o módulo está selecionado. Os argumentos podem ser qualquer um dos tipos com suporte, ou você pode criar uma enumeração personalizada quando necessário. De modo similar aos elementos **Portas**, os elementos **Argumentos** podem ter um elemento **Descrição** opcional que especifica o texto que aparece quando você passa o cursor do mouse sobre o nome do parâmetro. Propriedades opcionais de um módulo, como o defaultValue, minValue e maxValue, podem ser adicionadas a qualquer argumento como atributos a um elemento **Propriedades**. Propriedades válidas para o elemento **Propriedades** dependem do tipo de argumento e são descritas com os tipos de argumento com suporte a seguir. Assim como acontece com entradas e saídas, é essencial que cada um dos parâmetros tenha valores de ID exclusivos associados. Além disso, os valores de ID devem corresponder aos parâmetros nomeados em sua função R. Em nosso exemplo de início rápido, a ID/parâmetro associado era *troca*.

### Elemento arg
Um parâmetro de módulo é definido usando o elemento filho **Arg** da seção **Argumentos** do arquivo de definição XML. Assim como acontece com os elementos filho na seção **Portas**, a ordem dos parâmetros na seção **Argumentos** define o layout encontrado na UX. Os parâmetros aparecem de cima para baixo na interface do usuário na mesma ordem em que são definidos no arquivo XML. Os tipos com suporte no Aprendizado de Máquina para parâmetros estão listados abaixo.

**int** – um parâmetro de tipo de número inteiro (32 bits).

		<Arg id="intValue1" name="Int Param" type="int">
			<Properties min="0" max="100" default="0" />
			<Description>Integer Parameter</Description>
       </Arg>



* *Propriedades opcionais*: **mín.**, **máx.** e **padrão**

**double** – um parâmetro de tipo duplo.

       <Arg id="doubleValue1" name="Double Param" type="double">
           <Properties min="0.000" max="0.999" default="0.3" />
		   <Description>Double Parameter</Description>
       </Arg>


* *Propriedades opcionais*: **mín.**, **máx.** e **padrão**

**bool** – um parâmetro booliano que é representado por uma caixa de seleção no UX.

		<Arg id="boolValue1" name="Boolean Param" type="bool">
			<Properties default="true" />
			<Description>Boolean Parameter</Description>
		</Arg>



* *Propriedades opcionais*: **padrão** -falso se não definido

**string**: uma cadeia de caracteres padrão

        <Arg id="stringValue1" name="My string Param" type="string">
		   <Properties default="Default string value." isOptional="true" />
           <Description>String Parameter 1</Description>
        </Arg>


* *Propriedades opcionais*: **padrão** e **isOptional** - Uma cadeia de caracteres opcional sem um valor padrão será passada como null para a função R caso um valor não seja fornecido de outra forma pelo usuário.

**ColumnPickerFor**: um parâmetro de seleção de coluna. Esse tipo é renderizado como um seletor de coluna no UX. O elemento **Propriedade** é usado aqui para especificar a ID da porta da qual as colunas serão selecionadas, em que o tipo de porta de destino deve ser *DataTable*. O resultado da seleção de coluna será passado para a função R como uma lista de cadeias de caracteres contendo os nomes da coluna selecionada.

		<Arg id="colset" name="Column set" type="ColumnPicker">	  
		  <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
		  <Description>Column set</Description>
		</Arg>


* *Propriedades obrigatórias*: **portId** – corresponde à ID de um elemento de entrada com o tipo *DataTable*.
* *Propriedades opcionais*:
	* **allowedTypes** – filtre os tipos de coluna entre os quais o usuário pode escolher. Os valores válidos incluem: 
		* 	Numérico
		* 	Booliano
		* 	Categóricos
		* 	Cadeia de caracteres
		* 	Rótulo
		* 	Recurso
		* 	Pontuação
		* 	Todos

	* **padrão** – seleções padrão válidas para o seletor de coluna incluem:
		* Nenhum
		* NumericFeature
		* NumericLabel
		* NumericScore
		* NumericAll
		* BooleanFeature
		* BooleanLabel
		* BooleanScore
		* BooleanAll
		* CategoricalFeature
		* CategoricalLabel
		* CategoricalScore
		* CategoricalAll
		* StringFeature
		* StringLabel
		* StringScore
		* StringAll
		* AllLabel
		* AllFeature
		* AllScore
		* Todos

                            							
**Suspensa**: uma lista (suspensa) enumerada especifica pelo usuário. Os itens de lista suspensa são especificados dentro do elemento **Propriedades** usando um elemento de **Item**. A **id** para cada **Item** deve ser exclusiva e uma variável de R válida e o nome do item é tanto o texto que aparece para os usuários quanto o valor que é passado para a função R.

	<Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>	

* *Propriedades opcionais*:
	* **padrão** – o valor da propriedade padrão deve corresponder a um valor de id de um dos elementos de **Item**.


### Arquivos auxiliares

Qualquer arquivo que é colocado no arquivo ZIP do módulo personalizado estará disponível para uso durante o momento de execução. Se houver uma estrutura de diretório presente, esta será preservada. Isso significa que o fornecimento de arquivos funciona da mesma forma localmente e na execução do Aprendizado de Máquina do Azure.

Por exemplo, digamos que você queira remover quaisquer linhas com NAs do conjunto de dados e também remover quaisquer linhas duplicadas antes de produzir CustomAddRows, e você já escreveu uma função R que faz isso em um arquivo RemoveDupNARows.R:

	RemoveDupNARows <- function(dataFrame) {
		#Remove Duplicate Rows:
		dataFrame <- unique(dataFrame)
		#Remove Rows with NAs:
		finalDataFrame <- dataFrame[complete.cases(dataFrame),]
		return(finalDataFrame)
	}
Você pode usar o arquivo auxiliar RemoveDupNARows.R na função CustomAddRows como fonte:

	CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
		source(“RemoveDupNARows.R”)
			if (swap) { 
				dataset <- rbind(dataset2, dataset1))
	 		} else { 
	  			dataset <- rbind(dataset1, dataset2)) 
	 		} 
		dataset <- removeDupNARows(dataset)
		return (dataset)
	}

Em seguida, carregue um arquivo zip contendo “CustomAddRows.R”, “CustomAddRows.xml” e “RemoveDupNARows.R” como um módulo R personalizado.

## Ambiente de execução ##
O ambiente de execução para o script R usa a mesma versão do R que o módulo **Executar Script R** e pode usar os mesmos pacotes padrão. Você pode adicionar pacotes R adicionais ao módulo personalizado incluindo-os no pacote zip do módulo personalizado e carregando-os para seu script R como faria em seu próprio ambiente R.

**Limitações do ambiente de execução** incluem:

* Sistema de arquivos não persistente: arquivos gravados quando o módulo personalizado é executado não persistirão entre várias execuções do mesmo módulo.
* Sem acesso à rede



 

<!---HONumber=July15_HO2-->