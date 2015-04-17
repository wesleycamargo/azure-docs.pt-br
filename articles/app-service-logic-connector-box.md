<properties 
   pageTitle="Usando o Conector do Box em seu aplicativo lógico" 
   description="Usando o Conector do Box em seu aplicativo lógico" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# **Usando o Conector do Box em seu aplicativo lógico**

 

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo.  Você pode ter situações em que talvez precise trabalhar com o Box, que permite compartilhar dados com qualquer pessoa de maneira segura - mesmo que a pessoa esteja fora do seu firewall.

 

O aplicativo de galeria do Box fornece Gatilho e Ações como mecanismos para interagir com o Box:

 

1. **Gatilho****quando há um Arquivo Disponível**:  Use esta opção se você quiser iniciar um aplicativo lógico quando um arquivo é adicionado a uma pasta do Box.  O gatilho verifica a pasta do Box especificada com a frequência configurada e dispara o fluxo quando há um arquivo disponível na pasta.  Ele retorna o conteúdo e as propriedades do arquivo e, após passá-lo para a etapa seguinte do aplicativo lógico, ele é excluído.


	<table>
	  <tr>
	    <td><b>Nome do Parâmetro</b></td>
	    <td><b>Descrição</b></td>
	    <td><b>Obrigatório</b></td>
	  </tr>
	  <tr>
	    <td>Caminho do Arquivo</td>
	    <td>Caminho da pasta na qual o arquivo está.</td>
	    <td>Sim</td>
	  </tr>
	  <tr>
	    <td>Tipo de Arquivo</td>
	    <td>Especifica se o arquivo é de Texto ou Binário.</td>
	    <td>Não</td>
	  </tr>
	  <tr>
	    <td>Frequência</td>
	    <td>Especifica o tipo de frequência, deve ser selecionado entre os tipos listados.  Ele pode ser um dos seguintes:  Anos, meses, semanas, dias, horas, minutos ou segundos</td>
	    <td>Sim</td>
	  </tr>
	  <tr>
	    <td>Intervalo</td>
	    <td>Especifica a unidade da frequência.</td>
	    <td>Sim</td>
	  </tr>
	</table>


 

2. **Ações**:  As ações permitem que você execute ações predefinidas na conta do Box configurada com o aplicativo lógico.  A seguir estão as ações que podem ser executadas na conta do Box com seu conector:

	a. *List Files:* Esta operação retorna as informações de todos os arquivos em uma pasta.  A seguir está a lista de parâmetros necessários para a ação:

	<table>
	  <tr>
	    <td><b>Nome do Parâmetro</b></td>
	    <td><b>Descrição</b></td>
	    <td><b>Obrigatório</b></td>
	  </tr>
	  <tr>
	    <td>Caminho da Pasta</td>
	    <td>O caminho da pasta na qual a lista deve ser feita.</td>
	    <td>Sim</td>
	  </tr>
	</table>

	*Observação:  Ela não retorna nenhum conteúdo de arquivo.*

 

    b. *Get File:* Esta operação recupera um arquivo, incluindo seu conteúdo e propriedades.  A seguir está a lista de parâmetros necessários para a ação:

	<table>
	  <tr>
	    <td><b>Nome do Parâmetro</b></td>
	    <td><b>Descrição</b></td>
	    <td><b>Obrigatório</b></td>
	  </tr>
	  <tr>
	    <td>Caminho do Arquivo</td>
	    <td>Caminho da pasta na qual o arquivo está.</td>
	    <td>Sim</td>
	  </tr>
	  <tr>
	    <td>Tipo de Arquivo</td>
	    <td>Especifica se o arquivo é de Texto ou Binário.</td>
	    <td>Não</td>
	  </tr>
	</table>
	* Observação: esta operação não exclui o arquivo depois de lê-lo.*

 

    c. Carregar Arquivo:  Como o nome sugere, a ação carrega o arquivo para a conta do Box.  Se o arquivo já existir, ele não será substituído e um erro aparecerá.  A seguir está a lista de parâmetros necessários para a ação:

	<table>
	  <tr>
	    <td><b>Nome do Parâmetro</b></td>
	    <td><b>Descrição</b></td>
	    <td><b>Obrigatório</b></td>
	  </tr>
	  <tr>
	    <td>Caminho do Arquivo</td>
	    <td>Caminho para o arquivo.</td>
	    <td>Sim</td>
	  </tr>
	  <tr>
	    <td>Conteúdo do Arquivo</td>
	    <td>Conteúdo do arquivo a ser carregado.</td>
	    <td>Sim</td>
	  </tr>
	  <tr>
	    <td>Codificação de Transferência de Conteúdo</td>
	    <td>Tipo de codificação do conteúdo, pode ser Base64 ou Nenhum.</td>
	    <td> </td>
	  </tr>
	</table>


    d. Excluir Arquivo:  A ação exclui o arquivo especificado de uma pasta.  Se o arquivo/pasta não for encontrado, uma exceção aparecerá.  A seguir está a lista de parâmetros necessários para a ação:

 	<table>
	  <tr>
	    <td><b>Nome do Parâmetro</b></td>
	    <td><b>Descrição</b></td>
	    <td><b>Obrigatório</b></td>
	  </tr>
	  <tr>
	    <td>Caminho do Arquivo</td>
	    <td>Caminho da pasta na qual o arquivo está.</td>
	    <td>Sim</td>
	  </tr>
	</table>


 

## **Criando um Conector do Box para seu Aplicativo Lógico** ##

Para usar o Conector do Box, você precisa primeiro criar uma instância do aplicativo de API para o Conector do Box.  Isso pode ser feito da seguinte maneira:

1. Abra o Azure Marketplace usando a opção + NOVO na parte inferior direita do Portal do Azure.

2. Navegue até "Web e Dispositivos móveis > Aplicativos de API" e pesquise "Conector do Box".

3. Configurar o Conector do Box e clique em  Criar:

	![][1]

4. Depois disso, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o Conector do Box.


## **Usando o Conector do Box em seu Aplicativo Lógico** ##

Depois de criar seu aplicativo de API, você pode usar o Conector do Box como um gatilho ou ação para seu aplicativo lógico.  Para fazer isso, você precisa:


1. Criar um novo aplicativo lógico e escolher o mesmo grupo de recursos que tem o Conector do Box.

2. Abra "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo.  O Conector do Box apareceria na seção "Usados Recentemente" na galeria à direita.  Selecione-o.

3. Se o Conector do Box tiver sido selecionado no início do aplicativo lógico, ele age como gatilho. Caso contrário, ações poderiam ser executadas na conta do Box usando o conector.

4. A primeira etapa seria autenticar e autorizar aplicativos lógicos a executarem operações em seu nome.  Para iniciar a autorização, clique em Autorizar no Conector do Box.

	![][2]

5. Clicar em Autorizar abriria a caixa de diálogo de autenticação do Box.  Forneça os detalhes de logon da conta do Box na qual você deseja executar as operações.

	![][3]

6. Conceda aos aplicativos lógicos acesso à sua conta para executar operações em seu nome. 

	![][4]

7. Se o Conector do Box estiver configurado como Gatilho, os gatilhos são mostrados. Caso contrário, uma lista de ações é exibida e você pode escolher a operação que deseja executar.  

	![][5]


<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg


<!--HONumber=49-->