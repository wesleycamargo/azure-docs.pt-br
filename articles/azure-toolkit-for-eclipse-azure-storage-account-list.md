---
title: Lista de contas do Armazenamento do Azure
description: "Gerenciar as configurações de conta de armazenamento usando o Kit de Ferramentas do Azure para o Eclipse"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: bbacfcd8-dbf5-4265-a930-59f508de5325
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 9ef430dfaa43f9bc4294593c8abbdaf457ef07c8


---
# <a name="azure-storage-account-list"></a>Lista de contas do Armazenamento do Azure
As contas de armazenamento do Azure permitem que os locais de download sejam usados para o seu JDK, servidor de aplicativos e componentes arbitrários, bem como para armazenar o estado ao usar o caching. O Eclipse mantém uma lista de contas de armazenamento conhecidas que estão disponíveis para seus projetos no espaço de trabalho do Eclipse. Para abrir a caixa de diálogo **Contas de Armazenamento**, que é usada para gerenciar essa lista, no Eclipse, clique em **Janela**, **Preferências**, expanda o **Azure**, em seguida, clique em **Contas de Armazenamento**.

Veja a seguir o diálogo **Contas de Armazenamento** .

![][ic719496]

Essa caixa de diálogo também pode ser aberta a partir de um link **Contas** nas caixas de diálogo que usam contas de armazenamento, como as seguintes:

* Guia **JDK** da caixa de diálogo **Configuração do Servidor**.
* Guia **Servidor** da caixa de diálogo **Configuração do Servidor**.
* O diálogo **Adicionar Componente** .
* O diálogo de propriedades **Caching** .

## <a name="to-import-your-storage-accounts-using-a-publish-settings-file"></a>Para importar suas contas de armazenamento usando um arquivo de configurações de publicação
1. Na caixa de diálogo **Contas de Armazenamento**, clique em **Importar do arquivo PUBLISH-SETTINGS**.
2. (Ignore esta etapa se você já tiver salvo um arquivo de configurações de publicação em seu computador local.) Na caixa de diálogo **Importar Informações da Assinatura**, clique em **Baixar Arquivo PUBLISH-SETTINGS**. Se você ainda não estiver conectado à sua conta do Azure, receberá uma solicitação para fazer logon. Em seguida, você receberá uma solicitação para salvar um arquivo de configurações de publicação do Azure. (Você pode ignorar as instruções resultantes mostradas nas páginas de logon - elas são fornecidas pelo portal do Azure e são destinadas aos usuários do Visual Studio.) Salve-o em seu computador local.
3. Ainda na caixa de diálogo **Importar Informações da Assinatura**, clique no botão **Procurar**, selecione o arquivo de configurações da publicação salvo anteriormente, em seguida, clique em **Abrir**.
4. Clique em **OK** para fechar a caixa de diálogo **Importar Informações da Assinatura**.

## <a name="to-create-a-new-storage-account"></a>Para criar uma nova conta de armazenamento
1. Na caixa de diálogo **Contas de Armazenamento**, clique em **Adicionar**.
2. Na caixa de diálogo **Adicionar Conta de Armazenamento**, clique em **Nova**.
3. No diálogo **Nova Conta de Armazenamento** , especifique valores para os seguintes itens:
   * Nome da conta de armazenamento.
   * Local da conta de armazenamento.
   * Descrição da conta de armazenamento.
   * A assinatura à qual pertence a conta de armazenamento.
4. Clique em **OK** para fechar a caixa de diálogo **Nova Conta de Armazenamento**.

Pode levar vários minutos para que a sua conta de armazenamento seja criada. Depois de criada, clique em **OK** para fechar a caixa de diálogo **Adicionar Conta de Armazenamento** e sua nova conta de armazenamento será adicionado à lista de contas de armazenamento disponíveis.

## <a name="to-add-an-existing-storage-account-to-the-list"></a>Para adicionar uma conta de armazenamento existente à lista
1. Se você ainda não tem uma conta de armazenamento do Azure, crie uma seguindo as etapas listadas na seção **Para criar uma nova conta de armazenamento** acima. (Como alternativa, você pode criar uma nova conta de armazenamento no [Portal de Gerenciamento do Azure][Azure Management Portal].)
2. Na caixa de diálogo **Contas de Armazenamento**, clique em **Adicionar**.
3. Na caixa de diálogo **Adicionar Conta de Armazenamento**, insira valores para o **Nome** e a **Chave de Acesso**. O nome da conta e a chave de acesso devem ser de uma conta existente do armazenamento do Azure. Use a seção **Armazenamento** do [Portal de Gerenciamento do Azure][Azure Management Portal] para exibir seus nomes e chaves de conta de armazenamento. O diálogo **Adicionar Conta de Armazenamento** será semelhante ao seguinte:
   
    ![][ic719497]
4. Clique em **OK** para fechar a caixa de diálogo **Adicionar Conta de Armazenamento**.

## <a name="to-modify-a-storage-account-to-use-a-new-access-key"></a>Para modificar uma conta de armazenamento para usar uma nova chave de acesso
1. Na caixa de diálogo **Contas de Armazenamento**, clique na conta de armazenamento que você deseja editar e clique em **Editar**.
2. Na caixa de diálogo **Editar Chave de Acesso da Conta do Armazenamento**, modifique o valor **Chave de Acesso**.
3. Clique em **OK** para fechar a caixa de diálogo **Editar Chave de Acesso da Conta de Armazenamento**.

## <a name="to-remove-a-storage-account-from-the-list-maintained-in-eclipse"></a>Para remover uma conta de armazenamento da lista mantida no Eclipse
1. Na caixa de diálogo **Contas de Armazenamento**, clique na conta de armazenamento que você deseja editar e clique em **Remover**.
2. Clique em **OK** quando for solicitado a remover a conta de armazenamento.

> [!NOTE]
> A remoção da conta de armazenamento por meio do diálogo **Contas de Armazenamento** apenas a remove da lista de contas de armazenamento que podem ser exibidas no Eclipse. Ela não remove a conta de armazenamento de sua assinatura do Azure. Além disso, a conta de armazenamento pode reaparecer em sua lista, depois que o Eclipse carregar novamente os detalhes de sua assinatura.
> 
> 

## <a name="see-also"></a>Consulte também
[Kit de Ferramentas do Azure para Eclipse][Azure Toolkit for Eclipse]

[Instalar o Kit de Ferramentas do Azure para Eclipse][Installing the Azure Toolkit for Eclipse] 

[Criar um aplicativo Hello World para Azure no Eclipse][Creating a Hello World Application for Azure in Eclipse]

Para saber mais sobre como usar o Azure com o Java, confira o [Centro de Desenvolvedores Java do Azure][Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->



<!--HONumber=Jan17_HO1-->


