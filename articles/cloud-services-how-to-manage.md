<properties linkid="manage-services-how-to-manage-a-cloud-service" urlDisplayName="How to manage" pageTitle="How to manage a cloud service - Azure" metaKeywords="Azure manage cloud services, Azure Management Portal cloud services" description="Learn how to manage cloud services in the Azure Management Portal." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Manage Cloud Services" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Como gerenciar serviços de nuvem

[WACOM.INCLUDE [isenção de responsabilidade][isenção de responsabilidade]]

Na área **Serviços de Nuvem** do Portal de Gerenciamento do Azure, você pode atualizar uma função de serviço ou uma implantação, promover uma implantação de preparo para produção, vincular recursos ao Serviço de Nuvem para que você possa ver as dependências de recursos e dimensionar os recursos em conjunto, além de excluir um Serviço de Nuvem ou uma implantação.

## Sumário

-   [Como: atualizar uma função de serviço de nuvem ou uma implantação][Como: atualizar uma função de serviço de nuvem ou uma implantação]
-   [Como: permutar implantações de preparo para produção][Como: permutar implantações de preparo para produção]
-   [Como: vincular um recurso a um serviço de nuvem][Como: vincular um recurso a um serviço de nuvem]
-   [Como: excluir implantações e um serviço de nuvem][Como: excluir implantações e um serviço de nuvem]

## <span id="updaterole"></span></a>Como: Atualizar uma função de serviço de nuvem ou uma implantação

Se você precisar atualizar o código do aplicativo para o seu Serviço de Nuvem, use **Atualizar** no painel, página **Serviços de Nuvem** ou página **Instâncias**. Você pode atualizar única função ou todas as funções. Você precisará carregar um novo pacote de serviços e o arquivo de configuração do serviço.

1.  No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], no painel, na página **Serviços de Nuvem** ou na página **Instâncias**, clique em **Atualizar**.

    **Atualizar implantação** é exibido.

    ![UpdateDeployment][UpdateDeployment]

2.  Em **Rótulo da Implantação**, insira um nome para identificar a implantação (por exemplo, mycloudservicev2). Você encontrará o nome da implantação em **início rápido** no painel.

3.  Em **Arquivo do pacote**, use **Procurar** para carregar o arquivo do pacote de serviço (.cspkg).

4.  Em **Arquivo de configuração**, use **Procurar** para carregar o arquivo de configuração do serviço (.cscfg).

5.  Em **Função**, selecione **Tudo** se desejar atualizar todas as funções no Serviço de Nuvem. Para executar uma atualização de função, selecione a função que você deseja atualizar. Mesmo que você selecione uma função específica para atualizar, as atualizações no arquivo de configuração do serviço serão aplicadas a todas as funções.

6.  Se a atualização for alterar o número de funções ou o tamanho de qualquer função, marque a caixa de seleção **Permitir a atualização se os tamanhos ou o número de funções for alterado** para permitir que a atualização continue.

    Lembre-se que, se você alterar o tamanho de uma função (u seja, o tamanho de uma máquina virtual que hospede uma instância de função) ou o número de funções, cada instância de função (máquina virtual) deverá ter outra imagem criada e todos os dados locais serão perdidos.

7.  Se alguma das funções de serviço tiver uma instância de função, marque a caixa de seleção **Atualizar mesmo se uma ou mais funções contiverem uma única instância** para permitir que a atualização continue.

    O Azure pode garantir apenas 99,95 por cento de disponibilidade do Serviço de Nuvem durante uma atualização do Serviço de Nuvem se cada função tiver pelo menos duas instâncias de função (máquinas virtuais). Isso permite que uma máquina virtual processe as solicitações do cliente enquanto a outra é atualizada.

8.  Clique em OK (marca de seleção) para iniciar a atualização do serviço.

## <span id="swap"></span></a>Como: Permutar implantações de preparo para produção

Use **Permutar** para promover uma implantação de preparo de um Serviço de Nuvem para produção. Ao optar por implantar uma nova versão do serviço de nuvem, você pode preparar e testar a nova versão em seu ambiente de preparo do serviço em nuvem enquanto os clientes estão usando a versão atual em produção. Quando estiver pronto para promover a nova versão para produção, você poderá usar **Permutar** para alternar as URLs pelas quais as duas implantações são endereçadas.

É possível permutar implantações na página **Serviços de Nuvem** ou no painel.

1.  No [Portal de Gerenciamento][Portal de Gerenciamento do Azure], clique em **Serviços de Nuvem**.

2.  Na lista de serviços de nuvem, clique no serviço de nuvem para selecioná-lo.

3.  Clique em **Permutar**.

    O seguinte aviso de confirmação é aberto.

    ![Permutação dos Serviços de Nuvem][Permutação dos Serviços de Nuvem]

4.  Após verificar as informações da implantação, clique em **Sim** para permutar as implantações.

    A permuta da implantação acontece rapidamente pois a única coisa alterada são os endereços IP virtuais (VIP) para as implantações.

    Para economizar custos de computação, você pode excluir a implantação no ambiente de preparo quando tiver certeza que a nova implantação de produção está funcionado como o esperado.

## <span id="linkresources"></span></a>Como: Vincular um recurso a um serviço de nuvem

Para mostrar as dependências do serviço de nuvem em outros recursos, é possível vincular uma instância de Banco de Dados SQL do Azure ou uma conta de armazenamento ao serviço de nuvem. Você vincula e desvincula recursos na página **Recursos Vinculados**. Em seguida, você monitora o uso no painel do serviços de nuvem. Se uma conta de armazenamento vinculada tiver o monitoramento ativado, você poderá monitorar Solicitações Totais no painel de serviços de nuvem.

Use **Vincular** para vincular uma instância nova ou existente do Banco de Dados SQL ou conta de armazenamento ao Serviço de Nuvem. Em seguida, é possível dimensionar um banco de dados junto com a função de Serviço de Nuvem que o está usando na página **Escala**. (Uma conta de armazenamento é dimensionada automaticamente à medida que cresce o uso.) Para obter mais informações, consulte [Como dimensionar um Serviço de Nuvem e os recursos vinculados (a página pode estar em inglês)][Como dimensionar um Serviço de Nuvem e os recursos vinculados (a página pode estar em inglês)].

Você também pode monitorar, gerenciar e dimensionar o banco de dados no nó **Bancos de dados** do Portal de Gerenciamento.

Neste sentido, a "vinculação" de um recurso não conecta seu aplicativo ao recurso. Se você criar um novo banco de dados usando **Vincular**, precisará adicionar as cadeias de conexão ao código do seu aplicativo e atualizar o Serviço de Nuvem. Você também deverá adicionar cadeias de conexão se seu aplicativo usar recursos em uma conta do armazenamento vinculada.

O procedimento a seguir descreve como vincular uma nova instância de banco de dados SQL, implantada em um novo servidor de banco de dados SQL, a um serviço de nuvem.

### Para vincular uma instância de banco de dados SQL a um serviço de nuvem

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços de Nuvem**. Em seguida, clique no nome do Serviço de Nuvem para abrir o painel.

2.  Clique em **Recursos Vinculados**.

    A página **Recursos Vinculados** é exibida.

    ![LinkedResourcesPage][LinkedResourcesPage]

3.  Clique em **Vincular um Recurso** ou em **Vincular**.

    O assistente de **Vincular Recurso** é iniciado.

    ![Vincular Página1][Vincular Página1]

4.  Clique em **Crie um novo recurso** ou em **Vincular um recurso existente**.

5.  Escolha o tipo de recurso a ser vinculado. No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Banco de Dados SQL**. (A Visualização do Portal de Gerenciamento não dá suporte à vinculação de uma conta do armazenamento a um Serviço de Nuvem.)

6.  Para concluir a configuração do banco de dados, siga instruções da ajuda para a área **Bancos de Dados SQL** do Portal de Gerenciamento.

    Então, você pode acompanhar o progresso da operação de vínculo na área de mensagem.

    ![Progresso do vínculo][Progresso do vínculo]

    Quando o vínculo estiver concluído, você poderá monitorar o status do recurso vinculado no painel do Serviço de Nuvem. Para obter informações sobre o dimensionamento de um Banco de Dados SQL vinculado, consulte [Como dimensionar um Serviço de Nuvem e os recursos vinculados][Como dimensionar um Serviço de Nuvem e os recursos vinculados (a página pode estar em inglês)].

### Para desvincular um recurso vinculado

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços de Nuvem**. Em seguida, clique no nome do Serviço de Nuvem para abrir o painel.

2.  Clique em **Recursos Vinculados** e selecione o recurso.

3.  Clique em **Desvincular**. Em seguida, clique em **Sim** no aviso de confirmação.

    Desvincular o Banco de Dados SQL não afeta o banco de dados ou as conexões do aplicativo ao banco de dados. Ainda é possível monitorar o banco de dados na área **Bancos de Dados SQL** do Portal de Gerenciamento.

## <span id="deletecloudservice"></span></a>Como: Excluir implantações e um serviço de nuvem

Antes de poder excluir um serviço de nuvem, você deve excluir cada implantação existente.

Para economizar custos de computação, você pode excluir a implantação de preparo após verificar que sua implantação de produção está funcionando conforme o esperado. Você é cobrado pelos custos de computação das instâncias de função mesmo que um Serviço de Nuvem não esteja em execução.

Use o procedimento a seguir para excluir uma implantação ou seu serviço de nuvem.

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços de Nuvem**.

2.  Selecione o Serviço de Nuvem e clique em **Excluir**. (Para selecionar um Serviço de Nuvem sem abrir o painel, clique em qualquer lugar exceto no nome na entrada do Serviço de Nuvem.)

    Se você tiver uma implantação em preparo ou em produção, verá um menu de opções semelhante ao menu a seguir na parte inferior da janela. Para poder excluir o Serviço de Nuvem, você deve excluir todas as implantações existentes.

    ![Menu de exclusão][Menu de exclusão]

3.  Para excluir uma implantação, clique em **Excluir implantação de produção** ou em **Excluir implantação de preparo**. Em seguida, clique em **Sim** no prompt de confirmação.

4.  Se você planejar excluir o Serviço de Nuvem, repita a etapa??3, se necessário, para excluir sua outra implantação.

5.  Para excluir o Serviço de Nuvem, clique em **Excluir o Serviço de Nuvem**. Em seguida, clique em **Sim** no prompt de confirmação.

> [WACOM.NOTE]
> Se o monitoramento detalhado estiver configurado para seu serviço de nuvem, o Azure não exclui os dados de monitoramento de sua conta de armazenamento quando você exclui o serviço de nuvem. Você precisará excluir manualmente os dados. Para obter informações sobre onde encontrar as tabelas de métricas, consulte “Como: Acessar dados do monitoramento detalhado fora de Portal de Gerenciamento” em [Como monitorar serviços de nuvem][Como monitorar serviços de nuvem] (a página pode estar em inglês).

  [isenção de responsabilidade]: ../includes/disclaimer.md
  [Como: atualizar uma função de serviço de nuvem ou uma implantação]: #updaterole
  [Como: permutar implantações de preparo para produção]: #swap
  [Como: vincular um recurso a um serviço de nuvem]: #linkresources
  [Como: excluir implantações e um serviço de nuvem]: #deletecloudservice
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [UpdateDeployment]: ./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png
  [Permutação dos Serviços de Nuvem]: ./media/cloud-services-how-to-manage/CloudServices_Swap.png
  [Como dimensionar um Serviço de Nuvem e os recursos vinculados (a página pode estar em inglês)]: ../cloud-services-how-to-scale/
  [Portal de Gerenciamento]: http://manage.windowsazure.com/
  [LinkedResourcesPage]: ./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png
  [Vincular Página1]: ./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png
  [Progresso do vínculo]: ./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkProgress.png
  [Menu de exclusão]: ./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png
  [Como monitorar serviços de nuvem]: http://azure.microsoft.com/pt-br/documentation/articles/cloud-services-how-to-monitor/
