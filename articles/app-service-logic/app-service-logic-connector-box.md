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

# Usando o Conector do Box em seu aplicativo lógico



Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. Você pode ter situações em que talvez precise trabalhar com o Box, que permite compartilhar dados com qualquer pessoa de maneira segura – mesmo que a pessoa esteja fora do seu firewall.



O aplicativo de galeria do Box fornece a você Ações como mecanismos para interagir com o Box:



1. **Ações**: as ações permitem que você execute ações predefinidas na conta do Box, configurada com o aplicativo lógico. A seguir, ações que podem ser executadas na conta do Box usando o conector do Box:

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
</table>[AZURE.NOTE]Ela não retorna nenhum conteúdo de arquivo.



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
</table>[AZURE.NOTE]Esta operação não exclui o arquivo depois de lê-lo.



    c. Carregar Arquivo: como o nome sugere, a ação carrega o arquivo na conta do Box. Se o arquivo já existir, ele não será substituído e um erro aparecerá. A seguir, a lista de parâmetros necessários para a ação:

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
    <td>Caminho do Arquivo</td>
    <td>Caminho Completo do Arquivo, incluindo Pastas.</td>
    <td>Sim</td>
  </tr>
</table>




## Criando um Conector do Box para seu Aplicativo Lógico

Para usar o Conector do Box, você precisa primeiro criar uma instância do aplicativo de API para o Conector do Box. Você pode fazer isso de dentro do designer de Aplicativo lógico ou também fora dele. Criá-la fora do designer pode ser feito da seguinte maneira:

1. Abra o Azure Marketplace na home page do Portal do Azure.

2. Em "Tudo", pesquise "Conector do Box".

3. Configure o conector do Box e clique em Criar:

	![][1]

4. Depois disso, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o Conector do Box.


## Usando o Conector do Box em seu Aplicativo Lógico

Depois de criar seu aplicativo de API, você pode usar o Conector do Box como uma ação para seu Aplicativo Lógico. Para fazer isso, você precisa:


1. Criar um novo aplicativo lógico e escolher o mesmo grupo de recursos que tem o Conector do Box.

2. Abrir "Gatilhos e Ações" para abrir o Designer de aplicativos lógicos e configurar seu fluxo. O Conector do Box apareceria na seção "Usado Recentemente" na galeria do lado direito. Selecione-o.

3. Se o Conector do Box tiver sido selecionado no início do aplicativo lógico, ele age como gatilho. Caso contrário, ações poderiam ser executadas na conta do Box usando o conector. Observe que o conector do Box não tem nenhum gatilho na data de criação deste artigo.

4. A primeira etapa seria autenticar e autorizar aplicativos lógicos a executarem operações em seu nome. Para iniciar a autorização, clique em Autorizar no Conector do Box.

	![][2]

5. O clique em Autorizar abre a caixa de diálogo de autenticação do Box. Forneça os detalhes de logon da conta do Box na qual você deseja executar as operações.

	![][3]

6. Conceda aos aplicativos lógicos acesso à sua conta para executar operações em seu nome.

	![][4]

7. A lista de ações é exibida e você pode escolher a operação apropriada que deseja executar.

	![][5]


<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!---HONumber=62-->