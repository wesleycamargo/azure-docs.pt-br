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

 

Aplicativos lógicos podem ser disparados com base em uma variedade de fontes de dados e oferecem conectores para obter e processar dados como uma parte do fluxo. Pode haver situações em que talvez você tenha de trabalhar com o Box, que permite compartilhar dados com qualquer pessoa de maneira segura - mesmo que ela esteja fora do seu firewall.

 

O aplicativo de galeria do Box fornece Gatilho e Ações como mecanismos para interagir com o Box:

 

1. **Gatilho** **no Arquivo Disponível**: use esta opção para iniciar um aplicativo lógico quando um arquivo for adicionado a uma pasta do Box. O gatilho verifica a pasta do Box especificada com a frequência configurada e dispara o fluxo quando há um arquivo disponível na pasta. Ele retorna o conteúdo e as propriedades do arquivo e, após passá-lo com sucesso para a etapa seguinte do aplicativo lógico, ele é excluído.


	<table>
  <tr>
    <td><b>Nome do parâmetro</b></td>
    <td><b>Descrição</b></td>
    <td><b>Obrigatório</b></td>
  </tr>
  <tr>
    <td>Caminho do arquivo</td>
    <td>Caminho da pasta na qual o arquivo está presente.</td>
    <td>Sim</td>
  </tr>
  <tr>
    <td>Tipo de arquivo</td>
    <td>Especifica se o arquivo é de Texto ou Binário.</td>
    <td>Não</td>
  </tr>
  <tr>
    <td>Frequência</td>
    <td>Especifica o tipo de frequência, que deve ser selecionado entre os tipos listados. Pode ser um dos seguintes: anos, meses, semanas, dias, horas, minutos ou segundos</td>
    <td>Sim</td>
  </tr>
  <tr>
    <td>Intervalo</td>
    <td>Especifica a unidade da frequência.</td>
    <td>Sim</td>
  </tr>
</table>


 

2. **Ações**: as ações permitem que você execute ações predefinidas na conta do Box, configurada com o aplicativo lógico. A seguir, ações que podem ser executadas na conta do Box usando o conector do Box:

	a. *Listar Arquivos:* Esta operação retorna as informações de todos os arquivos em uma pasta. A seguir, a lista de parâmetros necessários para a ação:

	<table>
  <tr>
    <td><b>Nome do parâmetro</b></td>
    <td><b>Descrição</b></td>
    <td><b>Obrigatório</b></td>
  </tr>
  <tr>
    <td>Caminho da pasta</td>
    <td>Caminho da pasta na qual lista deve ser feita.</td>
    <td>Sim</td>
  </tr>
</table>*Observação: não retorna nenhum conteúdo de arquivo.*

 

    b. *Obter Arquivo:* essa operação recupera um arquivo, incluindo conteúdo e propriedades. A seguir, a lista de parâmetros necessários para a ação:

	<table>
  <tr>
    <td><b>Nome do parâmetro</b></td>
    <td><b>Descrição</b></td>
    <td><b>Obrigatório</b></td>
  </tr>
  <tr>
    <td>Caminho do arquivo</td>
    <td>Caminho da pasta na qual o arquivo está presente.</td>
    <td>Sim</td>
  </tr>
  <tr>
    <td>Tipo de arquivo</td>
    <td>Especifica se o arquivo é de Texto ou Binário.</td>
    <td>Não</td>
  </tr>
</table>*Observação: esta operação não excluirá o arquivo após a leitura.*

 

    c. Carregar Arquivo: como o nome sugere, a ação carrega o arquivo na conta do Box. Se o arquivo já existir, ele não será substituído e o erro será lançado. A seguir, a lista de parâmetros necessários para a ação:

	<table>
  <tr>
    <td><b>Nome do parâmetro</b></td>
    <td><b>Descrição</b></td>
    <td><b>Obrigatório</b></td>
  </tr>
  <tr>
    <td>Caminho do arquivo</td>
    <td>Caminho para o arquivo.</td>
    <td>Sim</td>
  </tr>
  <tr>
    <td>Conteúdo do arquivo</td>
    <td>Conteúdo do arquivo a ser carregado.</td>
    <td>Sim</td>
  </tr>
  <tr>
    <td>Codificação de transferência de conteúdo</td>
    <td>O tipo de codificação do conteúdo pode ser Base64 ou Nenhum.</td>
    <td> </td>
  </tr>
</table>d. Excluir Arquivo: a ação exclui o arquivo especificado de uma pasta. Se o arquivo/pasta não for encontrado uma exceção será lançada. A seguir, a lista de parâmetros necessários para a ação:

 	<table>
  <tr>
    <td><b>Nome do parâmetro</b></td>
    <td><b>Descrição</b></td>
    <td><b>Obrigatório</b></td>
  </tr>
  <tr>
    <td>Caminho do arquivo</td>
    <td>Caminho da pasta na qual o arquivo está presente.</td>
    <td>Sim</td>
  </tr>
</table>


 

## **Criando um conector do Box para seu aplicativo lógico** ##

Para usar o Conector do Box, você precisa primeiro criar uma instância de aplicativo de API do Conector do Cox. Isso pode ser feito da seguinte maneira:

1. Abra o Azure Marketplace usando a opção + NOVO na parte inferior direita do Portal do Azure.

2. Procure "Web e Dispositivos Móveis > aplicativos de API" e procure "Conector do Box".

3. Configure o conector do Box e clique em Criar:

	![][1]

4. Depois disso, agora você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o Conector do Box.


## **Usando o Conector do Box em seu aplicativo lógico** ##

Depois de criar seu aplicativo de API, agora você pode usar o Conector do Box como um gatilho ou uma ação para o seu aplicativo lógico. Para fazer isso, você precisa:


1. Criar um novo aplicativo lógico e escolher o mesmo grupo de recursos que tem o Conector do Box.

2. Abrir "Gatilhos e Ações" para abrir o Designer de aplicativos lógicos e configurar seu fluxo. O Conector do Box apareceria na seção "Usado Recentemente" na galeria do lado direito. Selecione-o.

3. Se o Conector do Box for selecionado no início do aplicativo lógico, agirá como gatilho. Caso contrário, as ações poderiam ser levadas para a conta do Box usando o conector.

4. A primeira etapa seria autenticar e autorizar a aplicativos de lógica para executar operações em seu nome. Para iniciar a autorização, clique em Autorizar no Conector do Box.

	![][2]

5. O clique em Autorizar abre a caixa de diálogo de autenticação do Box. Forneça os detalhes de logon da conta do Box na qual você deseja executar as operações.

	![][3]

6. Conceda aos aplicativos lógicos acesso à sua conta para executar operações em seu nome.

	![][4]

7. Se o Conector do Box estiver configurado como Gatilho, os gatilhos são mostrados. Caso contrário, uma lista de ações será exibida e você poderá escolher a operação que quer executar.

	![][5]


<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg


<!--HONumber=54-->