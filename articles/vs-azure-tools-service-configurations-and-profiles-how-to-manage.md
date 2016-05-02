<properties
   pageTitle="Como gerenciar perfis e configurações de serviço | Microsoft Azure"
   description="Saiba como trabalhar com arquivos de configuração de perfis e configurações de serviço| que armazenam as configurações para os ambientes de implantação e configurações de publicação para os serviços de nuvem."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="04/18/2016"
   ms.author="tarcher" />

# Como gerenciar perfis e configurações de serviço

## Visão geral

Quando você publica um serviço de nuvem, o Visual Studio armazena informações de configuração em dois tipos de arquivos de configuração: perfis e configurações de serviço. Configurações de serviço (arquivos .cscfg) armazenam configurações para os ambientes de implantação para um serviço de nuvem do Azure. O Azure usa esses arquivos de configuração ao gerenciar seus serviços de nuvem. Por outro lado, perfis (arquivos .azurePubxml) armazenam configurações de publicação para os serviços de nuvem. Essas configurações são um registro do que você escolhe quando utiliza o assistente de publicação e são usadas localmente pelo Visual Studio. Este tópico explica como trabalhar com ambos os tipos de arquivos de configuração.

## Configurações de Serviço

Você pode criar várias configurações de serviço a usar para cada um dos seus ambientes de implantação. Por exemplo, você pode criar uma configuração de serviço para o ambiente local que você usa para executar e testar um aplicativo do Azure e outra configuração de serviço para o seu ambiente de produção.

Você pode adicionar, excluir, renomear e modificar essas configurações de serviço com base em suas necessidades. Você pode gerenciar essas configurações de serviço do Visual Studio, conforme mostrado na ilustração a seguir.

![Gerenciar Configurações de Serviço](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Você também pode abrir a caixa de diálogo **Gerenciar Configurações** das páginas de propriedades da função. Para abrir as propriedades de uma função em seu projeto do Azure, abra o menu de atalho para essa função e, em seguida, escolha **Propriedades**. Na guia **Configurações**, expanda a lista **Configuração de Serviço** e, em seguida, clique **Gerenciar** para abrir a caixa de diálogo **Gerenciar Configurações**.

### Para adicionar uma configuração de serviço

1. No Gerenciador de Soluções, abra o menu de atalho para o projeto do Azure e clique em **Gerenciar Configurações**.

    A caixa de diálogo **Gerenciar Configurações de Serviço** é exibida.

1. Para adicionar uma configuração de serviço, você deve criar uma cópia de uma configuração existente. Para fazer isso, escolha a configuração que você deseja copiar na lista Nome e, em seguida, clique em **Criar cópia**.

1. (Opcional) Para atribuir um nome diferente à configuração de serviço, escolha a nova configuração de serviço na lista Nome e clique em **Renomear**. Na caixa de texto **Nome**, digite o nome que você deseja usar para essa configuração de serviço e, em seguida, clique em **OK**.

    Um novo arquivo de configuração de serviço chamado ServiceConfiguration.[Novo nome].cscfg é adicionado ao projeto do Azure no Gerenciador de Soluções.


### Para excluir uma configuração de serviço

1. No Gerenciador de Soluções, abra o menu de atalho para o projeto do Azure e clique em **Gerenciar Configurações**.

    A caixa de diálogo **Gerenciar Configurações de Serviço** é exibida.

1. Para excluir uma configuração de serviço, escolha a configuração que você deseja excluir na lista **Nome** e, em seguida, clique em **Remover**. Aparece uma caixa de diálogo para confirmar que você deseja excluir esta configuração.

1. Clique em **Excluir**.

     O arquivo de configuração de serviço é removido do projeto do Azure no Gerenciador de Soluções.


### Para renomear uma configuração de serviço

1. No Gerenciador de Soluções, abra o menu de atalho para o projeto do Azure e clique em **Gerenciar Configurações**.

    A caixa de diálogo **Gerenciar Configurações de Serviço** é exibida.

1. Para renomear uma configuração de serviço, escolha a nova configuração de serviço na lista **Nome** e clique em **Renomear**. Na caixa de texto **Nome**, digite o nome que você deseja usar para essa configuração de serviço e, em seguida, clique em **OK**.

    O nome do arquivo de configuração de serviço é modificado no projeto do Azure no Gerenciador de Soluções.

### Para alterar uma configuração de serviço

- Se você quiser alterar uma configuração de serviço, abra o menu de atalho para a função específica que você deseja alterar no projeto do Azure e, em seguida, clique em Propriedades. Confira [Como configurar as funções para um Serviço de Nuvem do Azure com o Visual Studio](https://msdn.microsoft.com/library/azure/hh369931.aspx) para saber mais.

## Fazer diferentes combinações de configurações usando perfis

Usando um perfil, você pode preencher automaticamente o **Assistente de Publicação** com diferentes combinações de configurações para diferentes finalidades. Por exemplo, você pode ter um perfil para depuração e outro para compilações de versão. Nesse caso, o perfil **Depurar** teria a opção **IntelliTrace** habilitada e a configuração **Depurar** selecionada; seu perfil **Versão** teria a opção **IntelliTrace** desabilitada e a configuração **Versão** selecionada. Você também pode usar perfis diferentes para implantar um serviço usando uma conta de armazenamento diferente.

Quando você executa o assistente pela primeira vez, um perfil padrão é criado. O Visual Studio armazena o perfil em um arquivo que tem uma extensão .azurePubXml, que é adicionada ao seu projeto do Azure na pasta **Perfis**. Se você especificar diferentes opções manualmente quando você executa o assistente mais tarde, o arquivo é atualizado automaticamente. Antes de executar o procedimento a seguir, você já deve ter publicado seu serviço de nuvem pelo menos uma vez.

### Para adicionar um perfil

1. Abra o menu de atalho do seu projeto do Azure e clique em **Publicar**.

1. Ao lado da lista **Perfil de destino**, clique no botão **Salvar Perfil**, como mostra a ilustração a seguir. Isso cria um perfil para você.

    ![Criar um novo perfil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)

1. Depois que o perfil é criado, clique em **<Gerenciar...>** na lista **Perfil de destino**.

    A caixa de diálogo **Gerenciar perfis** é exibida, como mostra a ilustração a seguir.

    ![Caixa de Diálogo Gerenciar Perfis](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)

1. Na lista **Nome**, escolha um perfil e, em seguida, clique em **Criar Cópia**.

1. Escolha o botão **Fechar**.

    O novo perfil aparece na lista Perfil de destino.

1. Na lista **Perfil de destino**, clique no perfil que você acabou de criar. As configurações do Assistente de Publicação são preenchidas com as opções do perfil selecionado.

1. Clique nos botões **Anterior** e **Próximo** para exibir cada página do Assistente de Publicação e, em seguida, personalize as configurações para esse perfil. Consulte [Assistente Publicar aplicativo do Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) para obter mais informações.

1. Depois de você terminar de personalizar as configurações, clique em **Próximo** para voltar para a página de configurações. O perfil é salvo quando você publica o serviço usando essas configurações, ou se você clica em **Salvar** ao lado da lista de perfis.

### Para renomear ou excluir um perfil

1. Abra o menu de atalho do seu projeto do Azure e clique em **Publicar**.

1. Na lista **Perfil de destino**, clique em **Gerenciar**.

1. Na caixa de diálogo **Gerenciar Perfis**, clique no perfil que você deseja excluir e, em seguida, clique em **Remover**.

1. Na caixa de diálogo de confirmação que é exibida, clique em **OK**.

1. Clique em **fechar**

### Para alterar um perfil

1. Abra o menu de atalho do seu projeto do Azure e clique em **Publicar**.

1. Na lista **Perfil de destino**, clique no perfil que você deseja alterar.

1. Clique nos botões **Anterior** e **Próximo** para exibir cada página do Assistente de Publicação e, em seguida, altere as configurações que deseja. Confira [Assistente de Publicação de Aplicativo do Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085).

1. Após alterar as configurações, clique em **Próximo** para voltar para a página **Configurações**.

1. (Opcional) Clique em **Publicar** para publicar o serviço de nuvem usando as novas configurações. Se você não deseja publicar seu serviço de nuvem no momento e você fecha o Assistente de Publicação, o Visual Studio pergunta se você deseja salvar as alterações feitas ao perfil.

## Próximas etapas

Para obter informações sobre como configurar outras partes do seu projeto do Azure do Visual Studio, consulte [Configurando um projeto do Azure](http://go.microsoft.com/fwlink/p/?LinkID=623075)

<!---HONumber=AcomDC_0420_2016-->