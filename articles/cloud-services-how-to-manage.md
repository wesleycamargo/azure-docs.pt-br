<properties urlDisplayName="How to manage" pageTitle="Como gerenciar um serviço de nuvem - Azure" metaKeywords="Azure manage cloud services, Azure Management Portal cloud services" description="Saiba como gerenciar serviços de nuvem no Portal de Gerenciamento do Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Manage Cloud Services" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="ryanwi" />





#Como gerenciar serviços de nuvem

Na área **Serviços de Nuvem** do Portal de Gerenciamento do Azure, você pode atualizar uma função de serviço ou uma implantação, promover uma implantação de preparo para produção, vincular recursos ao seu serviço de nuvem para que você possa ver as dependências de recursos e dimensionar os recursos em conjunto, além de excluir um serviço de nuvem ou uma implantação.

##Sumário##

* [Como: Atualizar uma função de serviço de nuvem ou uma implantação](#updaterole)
* [Como: Permutar implantações para promover uma implantação de preparo para produção](#swap)
* [Como: Vincular um recurso a um serviço de nuvem](#linkresources)
* [Como: Excluir implantações e um serviço de nuvem](#deletecloudservice)


<h2><a id="updaterole"></a>Como: Atualizar uma função de serviço de nuvem ou uma implantação</h2>

Se você precisar atualizar o código do aplicativo para o seu serviço de nuvem, use **Atualizar** no painel, na página **Serviços de Nuvem** ou na página **Instâncias**. Você pode atualizar única função ou todas as funções.Você precisará carregar um novo pacote de serviços e o arquivo de configuração de serviço.

1. No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/), no painel, na página **Serviços de Nuvem** ou na página **Instâncias**, clique em **Atualizar**.

	![UpdateDeployment](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2. Em **Rótulo de implantação**, insira um nome para identificar a implantação (por exemplo, mycloudservice4). Você encontrará o rótulo de implantação em **início rápido** no painel.

3. Em **Pacote**, use **Procurar** para carregar o arquivo do pacote de serviço (.cspkg).

4. Em **Configuração**, use **Procurar** para carregar o arquivo de configuração de serviço (.cscfg).

5. Em **Função**, selecione **Tudo** se desejar atualizar todas as funções no serviço de nuvem. Para executar uma única atualização de função, selecione a função que você deseja atualizar.Mesmo que você selecione uma função específica para atualizar, as atualizações no arquivo de configuração de serviço serão aplicadas a todas as funções.

6. Se a atualização alterar o número de funções ou o tamanho de qualquer função, marque a caixa de seleção **Permitir a atualização se os tamanhos ou o número de funções for alterado** para permitir que a atualização continue. 

	Lembre-se de que, se você alterar o tamanho de uma função (ou seja, o tamanho de uma máquina virtual que hospeda uma instância de função) ou o número de funções, cada instância de função (máquina virtual) deverá ter outra imagem criada e todos os dados locais serão perdidos.

7. Se alguma das funções de serviço tiver somente uma instância de função, marque a caixa de seleção **Atualizar mesmo se uma ou mais funções contiverem uma única instância** para permitir que a atualização continue. 

	O Azure pode garantir apenas 99,95 por cento de disponibilidade do serviço durante uma atualização do serviço de nuvem se cada função tiver pelo menos duas instâncias de função (máquinas **virtuais).Isso permite que uma máquina virtual processe as solicitações do cliente enquanto a outra é atualizada.

8. Clique em **OK** (marca de seleção) para iniciar a atualização do serviço.



<h2><a id="swap"></a>Como: Permutar implantações para promover uma implantação de preparo para produção</h2>

Use **Permutar** para promover uma implantação de preparo de um serviço de nuvem para produção. Ao optar por implantar uma nova versão do serviço de nuvem, você pode preparar e testar a nova versão em seu ambiente de preparo do serviço em nuvem enquanto os clientes estão usando a versão atual em produção.Quando estiver pronto para promover a nova versão para produção, você poderá usar **Permutar** para alternar as URLs pelas quais as duas implantações são endereçadas. 

É possível permutar implantações na página **Serviços de Nuvem** ou no painel.

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Nuvem**.

2. Na lista de serviços de nuvem, clique no serviço de nuvem para selecioná-lo.

3. Clique em **Permutar**.

	O seguinte aviso de confirmação é aberto.

	![Cloud Services Swap](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4. Após verificar as informações da implantação, clique em **Sim** para permutar as implantações.

	A permuta da implantação acontece rapidamente pois a única coisa alterada são os endereços IP virtuais (VIP) para as implantações.

	Para economizar custos de computação, você pode excluir a implantação no ambiente de preparo quando tiver certeza que a nova implantação de produção está funcionado como o esperado.

<h2><a id="linkresources"></a>Como: Vincular um recurso a um serviço de nuvem</h2>

Para mostrar as dependências do serviço de nuvem em outros recursos, é possível vincular uma instância do Banco de Dados SQL do Azure ou uma conta de armazenamento ao serviço de nuvem. Você pode vincular e desvincular recursos na página **Recursos Vinculados**, e monitorar seu uso no painel do serviço de nuvem.Se uma conta de armazenamento vinculada tiver o monitoramento ativado, você poderá monitorar Solicitações Totais no painel do serviço de nuvem.

Use **Vincular** para vincular uma instância nova ou existente do Banco de Dados SQL ou conta de armazenamento ao Serviço de Nuvem.  Em seguida, é possível dimensionar o banco de dados junto com a função do serviço de nuvem que o está usando na página **Escala**.(Uma conta de armazenamento é dimensionada automaticamente à medida que cresce o uso.)Para obter mais informações, consulte [Como dimensionar um Serviço de Nuvem e os recursos vinculados](../cloud-services-how-to-scale/). 

Você também pode monitorar, gerenciar e dimensionar o banco de dados no nó **Bancos de dados** do Portal de Gerenciamento. 

Neste sentido, a "vinculação" de um recurso não conecta seu aplicativo ao recurso. Se você criar um novo banco de dados usando **Vincular**, será necessário adicionar as cadeias de conexão ao código do seu aplicativo e atualizar o serviço de nuvem.Você também deverá adicionar cadeias de conexão se seu aplicativo usar recursos em uma conta de armazenamento vinculada.

O procedimento a seguir descreve como vincular uma nova instância do banco de dados SQL, implantada em um novo servidor de banco de dados SQL, a um serviço de nuvem.

###Para vincular uma instância do banco de dados SQL a um serviço de nuvem###

1. No [Portal de Gerenciamento](http://manage.windowsazure.com/), clique em **Serviços de Nuvem**.Em seguida, clique no nome do serviço de nuvem para abrir o painel.

2. Clique em **Recursos Vinculados**.

	A página **Recursos Vinculados** é aberta.

	![LinkedResourcesPage](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3. Clique em **Vincular um Recurso** ou em **Vincular**.

	O assistente de **Vincular Recurso** é iniciado.

	![Link Page1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4. Clique em **Criar um novo recurso** ou em **Vincular um recurso existente**.

5. Escolha o tipo de recurso a ser vinculado. No [Portal de Gerenciamento](http://manage.windowsazure.com/)Clique em **Banco de Dados SQL**.(A Visualização do Portal de Gerenciamento não dá suporte à vinculação de uma conta de armazenamento a um Serviço de Nuvem.)

6. Para concluir a configuração do banco de dados, siga instruções da ajuda para a área **Bancos de Dados SQL** do Portal de Gerenciamento.

	Você pode acompanhar o progresso da operação de vínculo na área de mensagem.

	![Link Progress](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkProgress.png)

	Quando o vínculo estiver concluído, você poderá monitorar o status do recurso vinculado no painel do serviço de nuvem. Para obter informações sobre o dimensionamento de um Banco de Dados SQL vinculado, consulte [Como dimensionar um Serviço de Nuvem e os recursos vinculados](../cloud-services-how-to-scale/).

###Para desvincular um recurso vinculado###

1. No [Portal de Gerenciamento](http://manage.windowsazure.com/), clique em **Serviços de Nuvem**.Em seguida, clique no nome do serviço de nuvem para abrir o painel.

2. Clique em **Recursos Vinculados** e selecione o recurso.

3. Clique em **Desvincular**. Em seguida, clique em **Sim** no aviso de confirmação.

	Desvincular o Banco de Dados SQL não afeta o banco de dados ou as conexões do aplicativo ao banco de dados. Ainda é possível monitorar o banco de dados na área **Bancos de Dados SQL** do Portal de Gerenciamento.



<h2><a id="deletecloudservice"></a>Como: Excluir implantações e um serviço de nuvem</h2>

Antes de poder excluir um serviço de nuvem, você deve excluir cada implantação existente.

Para economizar custos de computação, você pode excluir a implantação de preparo após verificar que sua implantação de produção está funcionando conforme o esperado. Você é cobrado pelos custos de computação das instâncias de função mesmo que um serviço de nuvem não esteja em execução.

Use o procedimento a seguir para excluir uma implantação ou seu serviço de nuvem. 

1. No [Portal de Gerenciamento](http://manage.windowsazure.com/), clique em **Serviços de Nuvem**.

2. Selecione o serviço de nuvem e clique em **Excluir**.(Para selecionar um serviço de nuvem sem abrir o painel, clique em qualquer lugar exceto no nome na entrada do serviço de nuvem.)

	Se tiver uma implantação em preparo ou em produção, você verá um menu de opções semelhante ao menu a seguir na parte inferior da janela. Para poder excluir o serviço de nuvem, você deve excluir todas as implantações existentes.

	![Delete Menu](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)


3. Para excluir uma implantação, clique em **Excluir implantação de produção** ou em **Excluir implantação de preparo**. Em seguida, clique em **Sim** no prompt de confirmação. 

4. Se você planejar excluir o serviço de nuvem, repita a etapa??3, se necessário, para excluir sua outra implantação.

5. Para excluir o serviço de nuvem, clique em **Excluir o serviço de nuvem**. Em seguida, clique em **Sim** no prompt de confirmação.

> [WACOM.NOTE]
> Se o monitoramento detalhado estiver configurado para seu serviço de nuvem, o Azure não exclui os dados de monitoramento de sua conta de armazenamento quando você exclui o serviço de nuvem. Você precisará excluir manualmente os dados. Para obter informações sobre onde encontrar as tabelas de métricas, consulte "Como: Acessar dados do monitoramento detalhado fora do Portal de Gerenciamento" em <a href="http://azure.microsoft.com/pt-br/documentation/articles/cloud-services-how-to-monitor/">Como monitorar serviços de nuvem</a>.


<!--HONumber=35.1-->
