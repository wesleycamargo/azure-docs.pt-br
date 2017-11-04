---
title: Gerenciar contas de armazenamento do Azure pilha | Microsoft Docs
description: Saiba como localizar, gerenciar, recuperar e recuperar as contas de armazenamento do Azure pilha
services: azure-stack
documentationcenter: 
author: AniAnirudh
manager: darmour
editor: 
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/6/2017
ms.author: anirudha
ms.openlocfilehash: 6e14bd6312135b45984a82099e68a934ec2a4a70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Gerenciar contas de armazenamento na pilha do Azure
Saiba como gerenciar contas de armazenamento na pilha do Azure para localizar, recuperar e recuperar a capacidade de armazenamento com base nas necessidades de negócios.

## <a name="find"></a>Localizar uma conta de armazenamento
A lista de contas de armazenamento na região pode ser exibida na pilha do Azure por:

1. Em um navegador da Internet, navegue até https://adminportal.local.azurestack.external.
2. Entre no portal de administração do Azure pilha como um operador de nuvem (usando as credenciais fornecidas durante a implantação)
3. No painel padrão – localizar o **gerenciamento região** lista e clique na região que você deseja explorar. Por exemplo **(local**).
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Selecione **armazenamento** do **provedores de recursos** lista.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. Agora, na folha de administrador do provedor de recursos de armazenamento – Role para baixo até o **contas de armazenamento** guia e clique nele.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   A página resultante é a lista de contas de armazenamento nessa região.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

Por padrão, as contas de 10 primeiros são exibidas. Você pode escolher buscar mais clicando o **carregar mais** link na parte inferior da lista.

OU

Se você estiver interessado em uma determinada conta de armazenamento – você pode **filtrar e buscar as contas relevantes** somente.


**Para filtrar contas:**

1. Clique em **filtro** na parte superior da folha.
2. A folha de filtro, ele permite que você especifique **nome da conta**, **ID da assinatura** ou **status** para ajustar a lista de contas de armazenamento a ser exibido. Use-os conforme apropriado.
3. Clique em **Atualizar**. Atualize a lista adequadamente.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Para redefinir o filtro: clique em **filtro**, limpar as seleções e atualizar.

A caixa de texto de pesquisa (na parte superior a folha de lista de contas de armazenamento) permite que você realce o texto selecionado na lista de contas. Isso é realmente útil no caso de quando o nome completo ou a id não está facilmente disponível.

Você pode usar texto livre aqui para ajudar a localizar a conta que você está interessado.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Examine os detalhes da conta
Depois de localizar as contas que você está interessado em Visualizar, clique na conta específica para exibir alguns detalhes. Abre uma nova folha com os detalhes da conta, como: o tipo da conta, hora de criação, local, etc.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Recuperar uma conta excluída
Você pode estar em uma situação em que você precisa recuperar uma conta excluída.

Na pilha do Azure, há uma maneira muito simple de fazer isso:

1. Navegue até a lista de contas de armazenamento. Consulte [encontrar uma conta de armazenamento](#find) neste tópico para obter mais informações.
2. Localize a conta específica na lista. Você pode precisar filtrar.
3. Verifique o *estado* da conta. Ele deve indicar **excluído**.
4. Clique na conta que abre a folha de detalhes da conta.
5. Sobre esta folha, localize o **recuperar** botão e clique nele.
6. Clique em **Sim** para confirmar.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. A recuperação está agora no *processar... Aguarde* para uma indicação de que ela foi bem-sucedida.
   Você também pode clicar no ícone de "sino" na parte superior do portal para exibir indicações de andamento.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Depois que a conta recuperada é sincronizada com êxito, ele pode ser usado novamente.

### <a name="some-gotchas"></a>Algumas armadilhas
* Sua conta excluída mostra estado como **fora de retenção**.
  
  Isso significa que a conta excluída excedeu o período de retenção e não pode ser recuperada.
* Sua conta excluída não será mostrado na lista de contas.
  
  Isso pode significar que a conta excluída já foi coletado como lixo. Nesse caso ele não pode ser recuperado. Consulte [recuperar a capacidade](#reclaim) neste tópico.

## <a name="set-the-retention-period"></a>Definir o período de retenção
A configuração do período de retenção permite que um operador de nuvem especificar um período de tempo em dias (entre 0 e 9999 dias) durante o qual qualquer conta excluída potencialmente pode ser recuperada. O período de retenção padrão é definido como 15 dias. Definir o valor como "0" significa que qualquer conta excluída imediatamente está fora de retenção e marcado para coleta de lixo periódica.

**Para alterar o período de retenção:**

1. Em um navegador da internet, navegue até https://adminportal.local.azurestack.external.
2. Entre no portal de administração do Azure pilha como um operador de nuvem (usando as credenciais fornecidas durante a implantação)
3. No painel padrão – localizar o **gerenciamento região** lista e clique na região que você deseja explorar – por exemplo **(local**).
4. Selecione **armazenamento** do **provedores de recursos** lista.
5. Clique em **configurações** na parte superior para abrir a folha de configuração.
6. Clique em **configuração** , em seguida, edite o valor do período de retenção.

   Definir o número de dias e, em seguida, salvá-lo.
   
   Esse valor estará em vigor imediatamente e é definido para a sua região inteira.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Recuperar a capacidade
Um dos efeitos colaterais de ter um período de retenção é que uma conta excluída continua a consumir capacidade até que ele fique fora do período de retenção. Como um operador de nuvem, talvez seja necessário uma maneira de recuperar o espaço de conta excluída mesmo que o período de retenção ainda não expirou.

Você pode recuperar a capacidade usando o portal ou o PowerShell.

**Para recuperar a capacidade de usar o portal:**
1. Navegue até a folha de contas de armazenamento. Consulte [encontrar uma conta de armazenamento](#find).
2. Clique em **recuperar espaço** na parte superior da folha.
3. Ler a mensagem e, em seguida, clique em **Okey**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Aguarde até ver o ícone de sino no portal de notificação de êxito.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Atualize a página de contas de armazenamento. As contas excluídas não são mostradas na lista porque eles foram removidos.

Você pode também usar o PowerShell para substituir explicitamente o período de retenção e recuperar a capacidade imediatamente.

**Para recuperar a capacidade de usar o PowerShell:**   

1. Confirme que você tenha instalado e configurado o PowerShell do Azure. Caso contrário, use as instruções a seguir: 
   * Para instalar a versão mais recente do PowerShell do Azure e associá-lo a sua assinatura do Azure, consulte [como instalar e configurar o Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Para obter mais informações sobre os cmdlets do Gerenciador de recursos do Azure, consulte [usando o PowerShell do Azure com o Gerenciador de recursos do Azure](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Execute o cmdlet a seguir:

> [!NOTE]
> Se você executar esse cmdlet você excluir permanentemente a conta e seu conteúdo. Ele não é recuperável. Use isso com cuidado.


        Clear-ACSStorageAccount -ResourceGroupName system.local -FarmName <farm ID>


Para obter mais detalhes, consulte [documentação do powershell do Azure pilha.](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="migrate-a-container"></a>Migrar um contêiner
Devido ao uso do armazenamento irregular pelos locatários, um operador de nuvem pode encontrar um ou mais subjacente locatário compartilhamentos usando mais espaço do que outras pessoas. Se isso ocorrer, o operador de nuvem pode tentar liberar espaço no compartilhamento enfatizado migrando manualmente alguns contêineres de blob para outro compartilhamento. 

Você deve usar o PowerShell para migrar os contêineres.
> [!NOTE]
>Migração de contêiner de blob não dá suporte a migração ao vivo e atualmente é uma operação offline. Durante a migração e até sua conclusão subjacentes blobs nesse contêiner não podem ser usados e estão "offline". 

**Para migrar usando o PowerShell de contêineres:**

1. Confirme que você tenha instalado e configurado o PowerShell do Azure. Caso contrário, use as instruções a seguir:
    * Para instalar a versão mais recente do PowerShell do Azure e associá-lo a sua assinatura do Azure, consulte [como instalar e configurar o Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). Para obter mais informações sobre os cmdlets do Gerenciador de recursos do Azure, consulte [usando o PowerShell do Azure com o Gerenciador de recursos do Azure](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Obter o nome do farm: 
      
      `$farm = Get-ACSFarm -ResourceGroupName system.local`
3. Obter os compartilhamentos: 

   `$shares = Get-ACSShare -ResourceGroupName system.local -FarmName $farm.FarmName`

4. Obter os contêineres para um determinado compartilhamento. Observe que a contagem e a intenção são parâmetros opcionais:
            
   `$containers = Get-ACSContainer -ResourceGroupName system.local -FarmName $farm.FarmName -ShareName $shares[0].ShareName -Count 4 -Intent Migration`  

   Em seguida, examine $containers:

   `$containers`

    ![](media/azure-stack-manage-storage-accounts/image13.png)
5. Obter os melhor compartilhamentos de destino para a migração de contêiner:

    `$destinationshares= Get-ACSSharesForMigration  -ResourceGroupName system.local -FarmName $farm.farmname -SourceShareName $shares[0].ShareName`

    Em seguida, examine $destinationshares:

    `$destinationshares`

    ![](media/azure-stack-manage-storage-accounts/image14.png)
6. Iniciar a migração para um contêiner, observe que esta é uma implementação de assíncrono, para que um possa loop todos os contêineres em um compartilhamento e acompanhar o status usando a id de trabalho retornado.

    `$jobId = Start-ACSContainerMigration -ResourceGroupName system.local -FarmName $farm.farmname -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares.UncPath`

    Em seguida, examine $jobId:

   ```
   $jobId
   d1d5277f-6b8d-4923-9db3-8bb00fa61b65
   ```
7. Verifique o status do trabalho de migração por sua id do trabalho. Quando a migração do contêiner for concluído, MigrationStatus é definido como "Concluído".

    `Get-ACSContainerMigrationStatus -ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image15.png)

8. Você pode cancelar um trabalho de migração em andamento. Novamente, isso é uma operação assíncrona e pode ser acompanhado $jobid:

    `Stop-ACSContainerMigration-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId-Verbose`

    ![](media/azure-stack-manage-storage-accounts/image16.png)

    Você pode verificar o status de cancelar a migração novamente:

    `Get-ACSContainerMigrationStatus-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image17.png)




  
  