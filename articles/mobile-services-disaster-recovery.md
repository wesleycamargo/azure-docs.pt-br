<properties 
	pageTitle="Recuperar o serviço móvel em caso de desastre - Serviços Móveis do Azure" 
	description="Saiba como recuperar o serviço móvel em caso de desastre." 
	services="mobile-services" 
	documentationCenter="" 
	authors="brettsam" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="brettsam"/>

# Recuperar o serviço móvel em caso de desastre

Quando usa os Serviços Móveis do Azure para implantar um aplicativo, você pode usar seus recursos internos para garantir a continuidade dos negócios em caso de problemas de disponibilidade, como falhas no servidor, interrupções de rede, perda de dados e perda generalizadas das instalações. Ao implantar seu aplicativo usando os Serviços Móveis do Azure, você tirando proveito de muitos recursos de tolerância e de infraestrutura que você precisaria criar, implementar e gerenciar ao implantar uma solução local tradicional. O Azure atenua grande parte das falhas potenciais a uma fração do custo.

<h2><a name="prepare"></a>Preparar-se para possíveis desastres</h2>

Para facilitar a recuperação em caso de um problema de disponibilidade, você pode se preparar para o problema com antecedência: 

+ **Fazer backup dos dados no Banco de Dados SQL do serviço móvel do Azure**
	<br/>Os dados do aplicativo de seu serviço móvel são armazenados em um Banco de Dados SQL do Azure. Recomendamos fazer o backup conforme indicado em [Diretrizes para a continuidade dos negócios para o Banco de Dados SQL].
+ **Fazer backup de seus scripts do serviço móvel**
	<br/>É recomendável armazenar armazene seus scripts do serviço móvel em um sistema de controle de código-fonte, como o [Team Foundation Service] ou o [GitHub] e não depender somente das cópias no próprio serviço móvel. Você pode baixar os scripts por meio do portal do Azure usando o [recurso de controle de código-fonte]dos Serviços Móveis ou [usando a ferramenta de linha de comando do Azure]. Preste atenção aos recursos rotulados como "visualização" no portal, pois a recuperação desses scripts não é garantida, e você pode precisar recuperá-los de seu próprio controle do código-fonte original.
+ **Reservar um serviço móvel secundário**
	<br/>No caso se um problema de disponibilidade com o serviço móvel, você poderá precisar reimplantá-lo em uma região alternativa do Azure. Para garantir que a capacidade esteja disponível (por exemplo, em raras circunstâncias, como a perda de toda a região), é recomendável criar um serviço móvel secundário em sua região alternativa e definir seu modo igual como maior do que o modo do serviço primário. (Se o serviço primário estiver em modo de compartilhamento, você poderá tornar o serviço secundário compartilhado ou reservado. Mas se o primário for reservado, o secundário também deverá ser reservado.)


<h2><a name="watch"></a>Procurar por sinais de um problema</h2>

Estas circunstâncias indicam um problema que pode exigir uma operação de recuperação:

+ Os aplicativos conectados ao serviço móvel não podem se comunicar com ele por um longo período de tempo.
+ O status do serviço móvel é exibido como **Não Íntegro** no [portal do Azure].
+ Uma faixa com o texto **Não Íntegro** aparece na parte superior de cada guia do serviço móvel no portal do Azure e operações de gerenciamento produzem mensagens de erro.
+ O [Painel de Serviços do Azure] indica que há um problema de disponibilidade.

<h2><a name="recover"></a>Recuperar-se de um desastre</h2>

Quando ocorre um problema, use o Painel de Serviços para obter orientações e atualizações.
 
Se for solicitado pelo Painel de Serviços, execute as seguintes etapas para restaurar o serviço móvel para um estado de execução em uma região alternativa do Azure. Se você tiver criado um serviço secundário com antecedência, essa capacidade será usada para restaurar o serviço primário. Como a URL e a chave do aplicativo do serviço primário não são alterados após a recuperação, você não precisa atualizar nenhum aplicativo que faça referência a ele. 

Para recuperar o serviço móvel após uma interrupção:

1. No portal do Azure, certifique-se de que o status do serviço esteja relatado como **Não Íntegro**.

2. Se você já reservou um serviço móvel secundário, poderá ignorar esta etapa.

   Se você ainda não já reservou um serviço móvel secundário, crie um agora em outra região do Azure. Defina o modo como igual ou maior que o modo do serviço primário. (Se o serviço primário estiver em modo de compartilhamento, você poderá tornar o serviço secundário compartilhado ou reservado. Mas se o primário for reservado, o secundário também deverá ser reservado.)

3. Configure as ferramentas de linha de comando do Azure para trabalhar com sua assinatura, conforme descrito em [Automatizar serviços móveis com ferramentas de linha de comando].

4. Agora você pode usar o serviço secundário para recuperar o primário.

    > [AZURE.IMPORTANT] Quando você executar o comando desta etapa, o serviço secundário será excluído para que sua capacidade possa ser usada para recuperar o serviço primário. É recomendável que você faça backup de seus scripts e configurações antes de executar o comando, se desejar mantê-los.
    
   Quando estiver pronto, execute o comando:

		azure mobile recover PrimaryService SecondaryService
		info:    Executing command mobile recover
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it. Do you want to recover the mobile service 'PrimaryService'? (y/n): y
		+ Performing recovery
		+ Cleaning up
		info:    Recovery complete
		info:    mobile recover command OK


	> [AZURE.NOTE] Pode levar alguns minutos para ver as alterações no portal depois que o comando é concluído.

5. Verifique se todos os scripts foram recuperados corretamente comparando-os com seus originais no controle do código-fonte. Na maioria dos casos, os scripts são recuperados automaticamente sem perda de dados, mas, se encontrar uma discrepância, você poderá recuperar esse script manualmente.

6. Verifique se o serviço recuperado está se comunicando com o Banco de Dados SQL do Azure. O comando recover recupera o serviço móvel, mas mantém a conexão com o banco de dados original. Se o problema na região primária do Azure também afetar o banco de dados, o serviço recuperado ainda poderá não ser executado corretamente. Você pode usar o Painel de Serviços do Azure para examinar o status do banco de dados de uma determinada região. Se o banco de dados original não estiver funcionando, você poderá recuperá-lo:
	+ Recupere o Banco de Dados SQL do Azure para a região do Azure onde você recuperou o serviço móvel, conforme descrito em [Diretrizes para a continuidade dos negócios com o Banco de Dados SQL].
	+ No portal do Azure, na guia **"Configurar"** do serviço móvel, escolha "Alterar banco de dados" e, em seguida, selecione o banco de dados recém-recuperado.

Agora você deve estar em um estado onde o serviço móvel foi recuperado para uma nova região do Azure e está aceitando tráfego de seus aplicativos de armazenamento usando sua URL original.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Diretrizes para a continuidade dos negócios com o Banco de Dados SQL]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/

[recurso de controle de código-fonte]: http://www.windowsazure.com/develop/mobile/tutorials/store-scripts-in-source-control/
[usando a ferramenta de linha de comando do Azure]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
[Portal do Azure]: http://manage.windowsazure.com/
[Painel de Serviços do Azure]: http://www.windowsazure.com/support/service-dashboard/
[Automatizar os serviços móveis com ferramentas de linha de comando]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/

<!--HONumber=47-->
