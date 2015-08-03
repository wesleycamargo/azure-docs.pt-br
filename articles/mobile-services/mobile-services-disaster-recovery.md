<properties
	pageTitle="Recuperar o serviço móvel em caso de desastre - Serviços Móveis do Azure"
	description="Saiba como recuperar o serviço móvel em caso de desastre."
	services="mobile-services"
	documentationCenter=""
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="christopheranderson"/>

# Recuperar o serviço móvel em caso de desastre

Quando usa os Serviços Móveis do Azure para implantar um aplicativo, você pode usar seus recursos internos para garantir a continuidade dos negócios em caso de problemas de disponibilidade, como falhas no servidor, interrupções de rede, perda de dados e perda generalizadas das instalações. Ao implantar seu aplicativo usando os Serviços Móveis do Azure, você tirando proveito de muitos recursos de tolerância e de infraestrutura que você precisaria criar, implementar e gerenciar ao implantar uma solução local tradicional. O Azure atenua grande parte das falhas potenciais a uma fração do custo.

## <a name="prepare"></a>Preparação para possíveis desastres

Para facilitar a recuperação em caso de um problema de disponibilidade, você pode se preparar para o problema com antecedência:

+ **Faça o backup de seus dados no Banco de Dados SQL do serviço móvel do Azure**  
Os dados do aplicativo de seu serviço móvel são armazenados em um Banco de Dados SQL do Azure. Recomendamos fazer o backup conforme indicado em [Diretrizes para a continuidade dos negócios para o Banco de Dados SQL].
+ **Fazer backup de seus scripts do serviço móvel**  
É recomendável armazenar seus scripts do serviço móvel em um sistema de controle de código-fonte, como o [Team Foundation Service] ou o [GitHub] e não depender somente das cópias no próprio serviço móvel. Você pode baixar os scripts por meio do portal do Azure usando o [recurso de controle do código-fonte] dos Serviços Móveis ou [usando o CLI do Azure]. Preste atenção aos recursos rotulados como "visualização" no portal, pois a recuperação desses scripts não é garantida, e você pode precisar recuperá-los de seu próprio controle de código-fonte original.
+ **Reservar um serviço móvel secundário**  
No caso se um problema de disponibilidade com o serviço móvel, você poderá precisar reimplantá-lo em uma região alternativa do Azure. Para garantir que a capacidade esteja disponível (por exemplo, em raras circunstâncias, como a perda de toda a região), é recomendável criar um serviço móvel secundário em sua região alternativa e definir seu modo igual como maior do que o modo do serviço primário. (Se o serviço primário estiver no modo Básico, você poderá tornar o serviço secundário Básico ou Padrão. Porém, se o primário for Padrão, o secundário também deverá ser Padrão.)

## <a name="watch"></a>Procurar por sinais de um problema

Estas circunstâncias indicam um problema que pode exigir uma operação de recuperação:

+ Os aplicativos conectados ao serviço móvel não podem se comunicar com ele por um longo período de tempo.
+ O status do serviço móvel é exibido como **Não íntegro** no [portal do Azure].
+ Uma faixa **Não íntegro** aparece na parte superior de cada guia do serviço móvel no portal do Azure e as operações de gerenciamento produzem mensagens de erro.
+ O [Painel de Serviços do Azure] indica que há um problema de disponibilidade.

## <a name="recover"></a>Recuperação de um desastre

Quando ocorre um problema, use o Painel de Serviços para obter orientações e atualizações.

Se for solicitado pelo Painel de Serviços, execute as seguintes etapas para restaurar o serviço móvel para um estado de execução em uma região alternativa do Azure. Se você tiver criado um serviço secundário com antecedência, essa capacidade será usada para restaurar o serviço primário. Como a URL e a chave do aplicativo do serviço primário não são alterados após a recuperação, você não precisa atualizar nenhum aplicativo que faça referência a ele.

Para recuperar o serviço móvel após uma interrupção:

1. No portal do Azure, certifique-se de que o status do serviço esteja relatado como **Não íntegro**.

2. Se você já reservou um serviço móvel secundário, poderá ignorar esta etapa.

   Se você ainda não já reservou um serviço móvel secundário, crie um agora em outra região do Azure. Defina seu modo de escala da mesma forma como o modo de seu serviço primário.

3. Configure a Interface de linha de comando do Azure (Azure CLI) para trabalhar com a sua assinatura, conforme descrito em [Automatizar serviços móveis com a CLI do Azure].

4. Agora você pode usar o serviço secundário para recuperar o primário.

	> [AZURE.IMPORTANT]Além de migrar seus arquivos, o comando Migrar também atualiza o nome do host de seu serviço primário para apontar para o serviço secundário, assim, os aplicativos cliente não precisam ser atualizados. No entanto, levará até 30 minutos para o nome de host ser resolvido para o novo serviço. Por esse motivo, é recomendável que o comando Migrar seja usado apenas em cenários de recuperação de desastre.

	> [AZURE.IMPORTANT]Quando você executar o comando desta etapa, o serviço secundário será excluído para que sua capacidade possa ser usada para recuperar o serviço primário. É recomendável que você faça backup de seus scripts e configurações antes de executar o comando, se desejar mantê-los.

	Quando estiver pronto, execute o comando:

		azure mobile migrate PrimaryService SecondaryService
		info:    Executing command mobile migrate
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it and the host name for 'PrimaryService' may not resolve for up to 30 minutes. Do you want to migrate the mobile service 'PrimaryService'? [y/n]: y
		+ Performing migration
		+ Migration with id '0123456789abcdef0123456789abcdef' started. The migration may take several minutes to complete.
		+ Cleaning up
		info:    Migration complete. It may take 30 minutes for DNS to resolve to the migrated site.
		info:    mobile migrate command OK

    > [AZURE.NOTE]Pode levar alguns minutos para ver as alterações no portal depois que o comando é concluído.

5. Verifique se todos os scripts foram recuperados corretamente comparando-os com seus originais no controle do código-fonte. Na maioria dos casos, os scripts são recuperados automaticamente sem perda de dados, mas, se encontrar uma discrepância, você poderá recuperar esse script manualmente.

6. Verifique se o serviço recuperado está se comunicando com o Banco de Dados SQL do Azure. O comando recover recupera o serviço móvel, mas mantém a conexão com o banco de dados original. Se o problema na região primária do Azure também afetar o banco de dados, o serviço recuperado ainda poderá não ser executado corretamente. Você pode usar o Painel de Serviços do Azure para examinar o status do banco de dados de uma determinada região. Se o banco de dados original não estiver funcionando, você poderá recuperá-lo:
	+ Recupere o Banco de Dados SQL do Azure para a região do Azure onde você recuperou o serviço móvel, conforme descrito em [Diretrizes para a continuidade dos negócios com o Banco de Dados SQL].
	+ No portal do Azure, na guia **"Configurar"** do serviço móvel, escolha "Alterar banco de dados" e, em seguida, selecione o banco de dados recém-recuperado.

7. O serviço móvel agora está hospedado em um local físico diferente. Você precisará atualizar suas credenciais de publicação e/ou git para permitir a atualização de seu site em execução.
	+ Se você estiver usando um **back-end .NET**, defina o perfil de publicação novamente, conforme descrito em [Publicar seu serviço móvel](mobile-services-dotnet-backend-windows-store-dotnet-get-started/#publish-your-mobile-service). Isso atualizará seus detalhes de publicação para apontar para o novo local de serviço.
	+ Se estiver usando um **back-end Javascript** e gerenciando o serviço com o Portal, você não precisará realizar ações adicionais.
	+ Se você estiver usando um **back-end Javascript** e gerenciando o serviço de nó, atualize seu git remoto para apontar para o novo repositório. Para fazer isso, remova o caminho do arquivo .git de seu git remoto:

		1. Localize o remoto de origem atual: git remote -v origin https://myservice.scm.azure-mobile.net/myservice.git (fetch) origin https://myservice.scm.azure-mobile.net/myservice.git (push)
		3. Atualize o remoto usando a mesma url, mas sem o caminho do arquivo .git final: git remote set-url origin https://myservice.scm.azure-mobile.net
		4. Retire da origem para verificar se ele está funcionando corretamente.

Agora você deve estar em um estado onde o serviço móvel foi recuperado para uma nova região do Azure e está aceitando tráfego de seus aplicativos de armazenamento usando sua URL original.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Diretrizes para a continuidade dos negócios com o Banco de Dados SQL]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Diretrizes para a continuidade dos negócios para o Banco de Dados SQL]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/

[recurso de controle do código-fonte]: http://www.windowsazure.com/develop/mobile/tutorials/store-scripts-in-source-control/
[usando o CLI do Azure]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
[portal do Azure]: http://manage.windowsazure.com/
[Painel de Serviços do Azure]: http://www.windowsazure.com/support/service-dashboard/
[Automatizar serviços móveis com a CLI do Azure]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
 

<!---HONumber=July15_HO4-->