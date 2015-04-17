<properties 
	pageTitle="Criar módulos R personalizados no Aprendizado de Máquina do Azure | Azure" 
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
	ms.date="02/04/2015" 
	ms.author="bradsev" />


# Criar módulos R personalizados no Aprendizado de Máquina do Azure

Este tópico descreve como criar um módulo R personalizado no Aprendizado de Máquina do Azure.  Ele descreve o que um módulo R personalizados é e quais arquivos que são usados para defini-lo.  Ele ilustra como construir esses arquivos e registrar o módulo para implantação em um espaço de trabalho de Aprendizado de Máquina.  Os elementos e atributos usados na definição de módulo personalizado, em seguida, são descritos mais detalhadamente.  Também é descrito como usar a funcionalidades e arquivos auxiliares, bem como diversas saídas. 

## O que é um módulo R personalizado?
Um módulo personalizado é um módulo definido pelo usuário que pode ser carregado no espaço de trabalho de um usuário e executado como parte de um experimento de Aprendizado de Máquina do Azure.  Um módulo R personalizado é um módulo personalizado que executa uma função R definida pelo usuário no Aprendizado de Máquina.  R é uma linguagem de programação para a computação estatística e gráficos que é amplamente usada por cientistas estatísticos e para implementar algoritmos estatísticos.  Suporte à linguagem do Aprendizado de Máquina é definido por padrão para usar a linguagem R em módulos personalizados.

Módulos personalizados têm o status de primeira classe no Aprendizado de Máquina do Azure no sentido que eles podem ser usados como qualquer outro módulo.  Eles podem ser executados com outros módulos, incluídos em experimentos publicados ou visualizados.  Os usuários têm controle sobre o algoritmo implementado pelo módulo, as portas de entrada e saída a ser usadas, os parâmetros de modelagem e outros vários comportamentos de tempo de execução.


## Arquivos em um módulo R personalizado
Um módulo R personalizado é definido por um arquivo .zip que contém, no mínimo, dois arquivos:

* Um arquivo que implementa a função R exposta pelo módulo
* Um arquivo de definição XML que descreve o módulo personalizado

Arquivos auxiliares adicionais também podem ser incluídos no arquivo .zip que fornecem funcionalidades que podem ser acessadas por meio do módulo personalizado.  Essa opção é discutida abaixo.

## Exemplo de início rápido
Este exemplo ilustra como construir os arquivos necessários para um módulo R personalizado, empacotá-los em um arquivo zip e, em seguida, registrar o módulo no seu espaço de trabalho de Aprendizado de Máquina.

Considere o exemplo de um módulo de Meu Adicionar Linhas personalizado que modifica a implementação padrão do módulo Adicionar Linhas usado para concatenar linhas (observações) de dois conjuntos de dados (quadros de dados).  O módulo Adicionar Linhas acrescenta as linhas do segundo conjunto de dados de entrada ao final do primeiro usando o algoritmo rbind.  A função `myAddRows` personalizada da mesma forma aceita dois conjuntos de dados, mas também aceita um parâmetro booliano de troca adicional como entrada.  Se o parâmetro de troca for **FALSE**, ele retorna o mesmo conjunto de dados que a implementação padrão.  Porém, se o parâmetro de troca for **TRUE**, ele acrescenta linhas do primeiro conjunto de dados de entrada ao final do segundo conjunto de dados.  O arquivo que implementa a função R de myAddRows exposta pelo módulo Meu Adicionar Linhas contém o seguinte código R.

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (dataset)
	}

Expor essa função `myAddRows` como um módulo de Aprendizado de Máquina do Azure, um arquivo de definição XML deve ser criado para especificar qual deve ser a aparência e comportamento do módulo Meu Adicionar Linhas. 

	<Module Version="v0.00.1" type="Public" insync="false" Owner="myName">
	  <GUID>{1CE529D1-B9D2-496F-AB42-8DBA60DE8279}</GUID>
	  <ID>myAddRows</ID>
	  <Name>My Add Rows</Name>	
	  <State>Custom</State>
	  <Description>This is my module description. </Description>
	  <Language Name="R" EntryPointFile="myAddRows.R" EntryPoint="myAddRows" />  
	    <Ports>
	      <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	      </output>
	      <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	      </input>
	      <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	      </input>
	    </Ports>
	    <Arguments>
	      <arg id="swap" display="swap" type="bool" >
	        <Description>Swaps inputs</Description>
	      </arg>
	    </Arguments>
	  <Category>My Category</Category>
	</Module>

 
Observe que é importante que o conteúdo do elemento ID no arquivo XML corresponda exatamente ao nome da função. 

Salve esses dois arquivos como *myAddRows.R* e *myAddRows.xml* e compacte-os em zip em um arquivo  *myAddRows.zip*.

Para registrá-los no seu espaço de trabalho de Aprendizado de Máquina, vá para seu espaço de trabalho no Estúdio de Aprendizado de Máquina, clique no botão **+NOVO** na parte inferior e escolha **MÓDULO -> DO PACOTE ZIP** para carregar o novo módulo personalizado Meu Adicionar Linhas.

![](http://i.imgur.com/RFJhCls.png)

O módulo Meu Adicionar Linhas agora está pronto para ser acessado pelos seus experimentos de Aprendizado de Máquina.

## Elementos no arquivo de definição XML

### Entradas e saídas
As entradas e saídas de um módulo personalizado são especificadas nos elementos filho da seção Portas do arquivo de definição XML.  A ordem desses elementos de entrada e saída determina o layout visto (UX) pelos usuários.  O primeiro filho listado no elemento de portas  do arquivo XML será a porta de entrada mais à esquerda no UX do Aprendizado de Máquina.  Os tipos de dados com suporte para portas de entrada e saídas são os seguintes: 

**DataTable**:  Esse tipo é passado para a função R como um data.frame.  Na verdade, qualquer tipo (por exemplo, arquivos CSV ou arquivos ARFF) com suporte  no Aprendizado de Máquina e que são compatíveis com o DataTable são convertidos em um data.frame automaticamente. 

       <input id="dataset1" display="Input 1" type="DataTable" IsOptional="false">
           <Description>Input Dataset 1</Description>
       </input>

**ZIP**: módulos personalizados podem aceitar um arquivo zip como entrada.  Essa entrada é descompactada para o diretório de execução de sua função

       <input id="zippedData" display="Zip Input" type="Zip" IsOptional="false">
           <Description>Zip Input for port</Description>
       </input>

É necessário que os atributos de identificação associados a cada uma das portas de entrada e saída tenham valores exclusivos e que esses valores correspondam aos parâmetros nomeados na sua função R.  Além disso, um valor padrão deve ser especificado para os atributos cujas entradas são opcionais.  O atributo IsOptional é opcional para ambos os tipos DataTable e Zip e é falso por padrão.  Esse valor padrão indica que o tipo de entrada não é opcional.


### Parâmetros
Os parâmetros para um módulo personalizado são especificados nos elementos filho da seção de argumentos do arquivo de definição XML.  Assim como acontece com os elementos filho na seção Portas, a ordem dos parâmetros na seção Argumentos define o layout encontrado na UX.  O primeiro parâmetro listado será o primeiro parâmetro de função.  Os tipos com suporte no Aprendizado de Máquina para parâmetros estão listados abaixo.  Os atributos opcionais devem ter seus valores padrão especificados na definição de XML.  Estes são os valores usados se o valor do parâmetro não for especificado ao usar a função.  Os atributos são opcionais são indicado para cada tipo.


**int** - um parâmetro de tipo inteiro (32 bits).

       <arg id="intValue1" display="My int Param" type="int" IsOptional="false" MinValue="0" MaxValue="100">
           <Description>Integer Parameter 1</Description>
       </arg>

IsOptional, MinValue e MaxValue são opcionais para int.

**double** - um parâmetro de tipo duplo.

       <arg id="doubleValue1" display="My double Param" type="double" IsOptional="false" min="0.000" max="0.999" default="0.3">
           <Description>Double Parameter 1</Description>
       </arg>
IsOptional, min, max e default são opcionais para double.

**bool** - um parâmetro booliano é representado por uma caixa de seleção no UX.

       <arg id="boolValue1" display="My boolean Param" type="bool" default="true">
           <Description>Boolean Parameter 1 </Description>
       </arg>

default é opcional para bool.

**string**: uma cadeia de caracteres padrão

        <arg id="stringValue1" display="My string Param" type="string" default="default value" IsOptional="true">
           <Description>String Parameter 1</Description>
        </arg>

default e IsOptional são opcionais para string.

**ColumnPickerFor**: um parâmetro de seleção de coluna.  Esse tipo é renderizado como um seletor de coluna no UX.  A ID do DataTable do qual você está selecionando colunas deve substituir a parte de tabela no valor do atributo de tipo.  A variável será passada para a função como uma lista de cadeias de caracteres. 

        <arg id="columnSelection1" display="My Column Param" type="ColumnPickerFor:table">
           <Description>My column selector Param 1</Description>		
        </arg>

Se, por exemplo, tivermos um DataTable com uma ID de dataset1, o tipo teria 

		type="ColumnPickerFor:dataset1" 
                            
**enum:<DropDown Type ID>**: uma lista (suspensa) enumerada.  O valor escolhido é passado como uma cadeia de caracteres para a função R.  Esse tipo requer que os valores enumerados válidos primeiro sejam definido na seção Argumentos.

       <DropDownType id="myDropDown1">
           <o id="red" display="Red"/>
           <o id="yellow" display="Yellow"/>
           <o id="blue" display="Blue"/>
       </DropDownType>
       <arg id="enum1" display="My Enum Param" default="red" type="enum:myDropDown1">
           <Description>My Enum Param 1</Description>
       </arg>

Assim como acontece com entradas e saídas, é essencial que cada um dos parâmetros tenha valores de ID exclusivos associados.  Além disso, os valores de ID devem corresponder aos parâmetros nomeados em sua função R.  Em nosso exemplo de início rápido, a id/parâmetro associado foi *swap*.

### Definição de linguagem
O elemento Language no seu arquivo de definição XML define a funcionalidade específica do idioma.  Para módulos R em geral, temos 

	<Language Name="R" EntryPointFile="myFunc.R" EntryPoint="myFunc" AddDisplayOutputPort="false"/>

Isso indica o idioma específico, o arquivo no qual a função é definida e o ponto de entrada na definição.  O atributo AddDisplayOutputPort é opcional para o elemento Language.  Assim como no modulo Executar Script R (link a ser definido), se você quiser adicionar uma porta de saída que pode ser usada para visualizar plotagens/gráficos, escolha *true* para a marca AddDisplayOutputPort e mais portas de saída serão exibida. 

### Funcionalidade auxiliar

Há diversos atributos que não são expostos por este exemplo, mas que podem ser usados por autores de módulo personalizado.  Por exemplo, um módulo pode ter um comportamento determinístico ou não determinístico.  Um módulo determinístico não será executado uma segunda vez ao receber os mesmos dados de entrada e a mesma configuração de parâmetro.  Resultados em cache serão então usados e propagados para todos os módulos abaixo.  Um exemplo de um módulo determinístico no Aprendizado de Máquina do Azure é o módulo Adicionar Linhas.  Um exemplo de um módulo não determinístico é um Leitor.  Para tornar o módulo personalizado não determinístico, altere a configuração padrão, adicionando o seguinte atributo à sua definição:

	<IsDeterministic>false</IsDeterministic>

### Arquivos auxiliares

Qualquer arquivo que é colocado no arquivo ZIP do módulo personalizado estará disponível para uso durante o momento de execução.  Se houver uma estrutura de diretório presente, esta será preservada.  Isso significa que o fornecimento de arquivos funciona da mesma forma localmente e na execução do Aprendizado de Máquina do Azure. 

Por exemplo, digamos que você deseja remover linhas NA e linhas duplicadas no conjunto de dados antes de gerá-lo em myAddRows e já escreveu uma função R que faz isso em um arquivo removeDupNARows.R:

	removeDupNARows <- function(dataFrame) {
		#Remove Duplicate Rows:
		dataFrame <- unique(dataFrame)
		#Remove Rows with NAs:
		finalDataFrame <- dataFrame[complete.cases(dataFrame),]
		return(finalDataFrame)
	}
Você pode usar o arquivo auxiliar removeDupNARows.R na função myAddRows como fonte:

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
		source("removeDupNARows.R")
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	dataset <- removeDupNARows(dataset)
	return (dataset)
	}

E, em seguida, carregar um arquivo zip contendo 'myAddRows.R', 'myAddRows.xml' e 'removeDupNARows.R' como um módulo R personalizado.

### Várias saídas

Para mais de um objeto de um tipo de dados com suporte de saída, as portas de saída apropriada precisam ser especificadas no arquivo de definição XML e os objetos precisam ser retornados como uma lista.  Os objetos de saída serão atribuídos a portas da esquerda para a direita, refletindo a ordem na qual os objetos são colocados na lista retornada de saída.
 
Por exemplo, se você quiser resultados de dataset, Dataset1 e Dataset2 como saída, dataset, dataset1 e dataset2 da esquerda para a direita, respectivamente, defina as portas de saída no arquivo 'myAddRows.xml' da seguinte maneira:

	<Ports>
	    <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset1" display="dataset1" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset2" display="dataset2" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
	    <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	    </input>
	    <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	    </input>
	</Ports>

E retorne a lista de objetos em uma lista na ordem correta em 'myAddRows.R':

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (list(dataset, Dataset1, Dataset2))
	}

## Ambiente de execução

TBD

## Próximas etapas

TBD

<!--HONumber=49-->