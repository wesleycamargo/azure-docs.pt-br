<properties urlDisplayName="" pageTitle="Usando grupos de recursos para gerenciar os recursos do Azure" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Using Resource groups to manage your Azure resources" authors="Nafisa Bhojawala"  solutions="" writer="" manager="timlt" editor=""  />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="12/02/2014" ms.author="davidmu" />


# Usando grupos de recursos para gerenciar os recursos do Azure

### Introdução

Historicamente, para gerenciar um recurso (uma entidade gerenciada pelo usuário, como um servidor de banco de dados, um banco de dados ou um site) no Microsoft Azure era necessário executar operações em um recurso de cada vez. Se você tivesse um aplicativo complexo composto de vários recursos, o gerenciamento desse aplicativo tornava-se uma tarefa complexa. No Portal de Visualização do Microsoft Azure, você pode criar grupos de recursos para gerenciar todos os seus recursos em um aplicativo coletivamente. O Grupo de recursos é um novo conceito no Azure que funciona como o limite do ciclo de vida de todos os recursos contidos nele. 
<br><br />

Os grupos de recursos permitem que você gerencie todos os seus recursos em um aplicativo, coletivamente. Os grupos de recursos são habilitados pela nova funcionalidade de gerenciamento, o Gerenciador de Recursos do Azure. O Gerenciador de Recursos do Azure permite que você agrupe vários recursos como um grupo lógico que funciona como o limite do ciclo de vida de todos os recursos contidos nele. Normalmente, um grupo contém recursos relacionados a um aplicativo específico. Por exemplo, um grupo pode conter um recurso de Site que hospeda seu site público, um Banco de dados SQL que armazena dados relacionais usados pelo site e uma Conta de armazenamento que armazena ativos não relacionais. 

Aqui está uma visão geral concisa de como usar grupos de recursos no Portal de Visualização do Microsoft Azure. 
<br><br />

### Criando grupos de recursos

Sempre que um recurso é criado no portal de visualização, ele sempre é criado dentro de um grupo de recursos. Você pode optar por criar um novo grupo de recursos ou usar um grupo de recursos existente no fluxo de criação. <br><br />

![](http://i.imgur.com/USKkQdW.png)

<br><br />
Quando você cria um aplicativo que consiste em alguns recursos que funcinam em conjunto (por exemplo, site + banco de dados) ele sempre é criado no seu próprio grupo de recursos, para que você possa gerenciar o ciclo de vida de todos os ativos relacionados ao grupo de recursos. Você pode adicionar ou remover mais recursos do grupo de recursos à medida que seu aplicativo evolui. 

![](http://i.imgur.com/Me0jbio.png)


<br><br />

### Navegando por grupos de recursos

Você pode navegar por todos os grupos de recursos clicando na barra de navegação à esquerda da tela. Um grupo de recursos tem uma lâmina que oferece a você todas as informações sobre um determinado grupo de recursos. A lâmina do Grupo de recursos também fornece uma visão unificada de suas informações de cobrança e monitoramento de todos os recursos do Grupo de recursos.

A seção de resumo mostra um mapa visual de todos os recursos do grupo de recursos. Também mostra recursos em outros grupos de recursos que estão vinculados ao grupo de recursos. O mapa de recursos também mostra o status de cada recurso. 
![](http://i.imgur.com/PhJeLZQ.png)

<br><br />

A parte do mapa de recursos pode ser personalizada para ser exibida em um tamanho maior, que exibirá todos os recursos contidos no grupo de recursos e os recursos em outros grupos de recursos que estão vinculados. Essa parte pode ser fixada no quadro inicial, que copiará a parte para o quadro inicial.

![](http://i.imgur.com/5Wqv2XR.png)

<br><br />

  Um clique no mapa de recursos inicia o modo de exibição de lista de todos os recursos do mapa de recursos. Esse modo de exibição lista todos os recursos dentro de um grupo de recursos ou vinculados a ele. Clicar sobre esses recursos iniciará suas lâminas. 

![](http://i.imgur.com/COPjNng.png)




<br><br />

### Adicionando recursos a grupos de recursos

Você pode adicionar recursos a um grupo de recursos usando o comando "Adicionar" na lâmina do grupo de recursos. Seguindo as etapas do fluxo, você pode adicionar outros recursos ao grupo de recursos.

![](http://i.imgur.com/G79kayH.png)

Observação: não é recomendável colocar um projeto de equipe no mesmo grupo de recursos que outros recursos do Azure. Se você criar um projeto da equipe em uma nova conta e grupo e em seguida criar um site, o grupo de sites será padronizado para o último grupo usado (grupo VSO) e você acabará com recursos de tempo de execução/desenvolvedor no mesmo grupo. 



<br><br />

### Excluindo grupos de recursos

Como os grupos de recursos permitem que você gerencie o ciclo de vida de todos os recursos contidos, a exclusão de um grupo de recursos resultará na exclusão de todos os recursos contidos no grupo. Você também pode excluir recursos individuais de um grupo de recursos. Tenha cuidado ao excluir um grupo de recursos pois pode haver outros recursos vinculados a ele. Você pode ver os recursos vinculados no mapa de recursos e tomar as medidas necessárias para evitar consequências não intencionais ao excluir grupos de recursos. 

![](http://i.imgur.com/ZTXoISb.png)

<!--HONumber=35.2-->
