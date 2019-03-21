---
title: Gerenciar contas de armazenamento do Azure Stack | Microsoft Docs
description: Saiba como localizar, gerenciar, recuperar e recuperar as contas de armazenamento do Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 34e49060a28869015f067f058b81239ec13eff47
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226387"
---
# <a name="manage-azure-stack-storage-accounts"></a>Gerenciar contas de armazenamento do Azure Stack

Saiba como gerenciar contas de armazenamento do Azure Stack. Encontrar, recuperar e recuperar a capacidade de armazenamento com base nas necessidades de negócios.

## <a name="find-a-storage-account"></a>Localizar uma conta de armazenamento

A lista de contas de armazenamento na região pode ser exibida no Azure Stack por:

1. Entrar para o [portal de administração](https://adminportal.local.azurestack.external).

2. Selecione **todos os serviços** > **armazenamento** > **contas de armazenamento**.

   ![Contas de armazenamento do Azure Stack](media/azure-stack-manage-storage-accounts/image4.png)

Por padrão, as contas de 10 primeiros são exibidas. Você pode optar por buscar mais clicando o **carregar mais** link na parte inferior da lista.

OU

Se você estiver interessado em uma determinada conta de armazenamento – você pode **filtrar e buscar as contas relevantes** apenas.


**Para filtrar as contas:**

1. Selecione **filtro** na parte superior do painel.
2. O painel de filtro, ele permite que você especifique **nome da conta**, **ID da assinatura**, ou **status** para ajustar a lista de contas de armazenamento a ser exibido. Use-os conforme apropriado.
3. Conforme você digita, a lista automaticamente aplicará o filtro.  .
   
    ![Filtrar as contas de armazenamento do Azure Stack](media/azure-stack-manage-storage-accounts/image5.png)

4. Para redefinir o filtro: selecione **filtro**, limpar as seleções e atualizar.

Caixa de texto de pesquisa (na parte superior de painel de lista de contas de armazenamento) permite que você realce o texto selecionado na lista de contas. Você pode usar isso quando o nome completo ou a ID não está disponível com facilidade.

Você pode usar texto livre aqui para ajudar a encontrar a conta que você está interessado.

![Localizar contas de armazenamento do Azure Stack](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Examine os detalhes da conta
Depois de localizar as contas que você está interessado em Exibir, você pode selecionar a conta específica para exibir determinados detalhes. Um novo painel é aberto com os detalhes da conta, como: o tipo de conta, hora de criação, localização, etc.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Recuperar uma conta excluída
Você pode estar em uma situação em que você precisa recuperar uma conta excluída.

No Azure Stack, há uma maneira simples de fazer isso:

1. Navegue até a lista de contas de armazenamento. Consulte localizar uma conta de armazenamento neste artigo para obter mais informações.
2. Localize essa conta específica na lista. Talvez você precise filtrar.
3. Verifique as *estado* da conta. Este campo deve dizer **Deleted**.
4. Selecione a conta, que abre o painel de detalhes da conta.
5. Na parte superior deste painel, localize o **recuperar** botão e selecione-o.
6. Clique em **Sim** para confirmar.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. Agora, a recuperação estiver em *processo... espera* para uma indicação de que ela foi bem-sucedida.
   Você também pode selecionar o ícone de "sino" na parte superior do portal para exibir indicações de progresso.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Depois que a conta recuperada é sincronizada com êxito, ele pode ser usado novamente.

### <a name="some-gotchas"></a>Algumas pegadinhas
* Sua conta excluída mostra estado como **fora de retenção**.
  
  Fora de retenção significa que a conta excluída ultrapassou o período de retenção e pode não ser recuperável.
* Sua conta excluída não mostra na lista de contas.
  
  Sua conta poderá não aparecer na lista conta quando a conta excluída já foi coletado como lixo. Nesse caso, ele não pode ser recuperado. Ver [recuperar capacidade](#reclaim) neste artigo.

## <a name="set-the-retention-period"></a>Definir o período de retenção
A configuração do período de retenção permite que um operador de nuvem especificar um período de tempo em dias (entre 0 e 9999 dias) durante o qual qualquer conta excluída potencialmente pode ser recuperada. O período de retenção padrão é definido como 0 dias. Definir o valor como "0" significa que qualquer conta excluída imediatamente está fora de retenção e marcado para coleta de lixo periódica.

**Para alterar o período de retenção:**

1. Entrar para o [portal de administração](https://adminportal.local.azurestack.external).
2. Selecione **todos os serviços** > **gerenciamento de região** sob **administração**.
3. Selecione **provedores de recursos** > **armazenamento** > **configurações**. O caminho de início for > *região* -provedores de recurso > armazenamento.
4. Selecione **configuração** , em seguida, edite o valor do período de retenção.

   Definir o número de dias e, em seguida, salvá-lo.
   
   Esse valor está em vigor imediatamente e é definido para toda a sua região.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Recuperar a capacidade
Um dos efeitos colaterais de ter um período de retenção é que uma conta excluída continua a consumir capacidade até que se trata de fora do período de retenção. Como um operador de nuvem, talvez seja necessário uma maneira de recuperar o espaço de conta excluída mesmo que o período de retenção ainda não expirou.

Você pode recuperar a capacidade usando o portal ou o PowerShell.

**Para recuperar a capacidade usando o portal:**
1. Navegue até o painel de contas de armazenamento. Consulte localizar uma conta de armazenamento.
2. Selecione **recuperar espaço** na parte superior do painel.
3. Ler a mensagem e, em seguida, selecione **Okey**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Aguarde até ver o ícone de sino no portal de notificação de êxito.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Atualize a página de contas de armazenamento. As contas excluídas não são mostradas na lista porque eles foram removidos.

Você pode também usar o PowerShell para substituir explicitamente o período de retenção e recuperar imediatamente a capacidade.

**Para recuperar a capacidade usando o PowerShell:**   

1. Confirme se você tem o Azure PowerShell instalado e configurado. Caso contrário, use as instruções a seguir: 
   * Para instalar a versão mais recente do PowerShell do Azure e associá-la com sua assinatura do Azure, consulte [como instalar e configurar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Para obter mais informações sobre os cmdlets do Azure Resource Manager, consulte [usando o Azure PowerShell com o Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkId=394767)
2. Execute os seguintes cmdlets:

> [!NOTE]  
> Se você executar esses cmdlets, você excluir permanentemente a conta e seu conteúdo. Ele não é recuperável. Use isso com cuidado.

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

Para obter mais informações, consulte [documentação do Azure Stack PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
 

## <a name="next-steps"></a>Próximas etapas

 - Para obter informações sobre como gerenciar permissões, consulte [controle de acesso baseado em função](azure-stack-manage-permissions.md).
 - Para informações sobre como gerenciar a capacidade de armazenamento para o Azure Stack, consulte [gerenciar a capacidade de armazenamento para o Azure Stack](azure-stack-manage-storage-shares.md).
