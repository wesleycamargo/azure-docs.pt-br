<properties linkid="manage-services-how-to-create-a-storage-account" urlDisplayName="How to create" pageTitle="How to create a storage account | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# <span id="createstorageaccount"></span></a>Como criar uma conta de armazenamento

Para armazenar arquivos e dados nos serviços Blob, Tabela, Fila e Arquivo no Azure, crie uma conta de armazenamento na região geográfica em que deseja armazenar os dados. Este tópico descreve como criar uma conta de armazenamento no Portal de Gerenciamento do Azure.

Para obter detalhes sobre a capacidade e produtividade da conta de armazenamento, consulte [Escalabilidade e metas de desempenho do Armazenamento do Azure][].

> [WACOM.NOTE] Para uma máquina virtual do Azure, uma conta de armazenamento será criada automaticamente no local da implantação se você ainda não tiver uma conta de armazenamento nesse local. O nome da conta de armazenamento será baseado no nome da máquina virtual.

## Sumário

-   [Como: Criar uma conta de armazenamento][]
-   [Próximas etapas][]

## <span id="create"></span></a>Como: Criar uma conta de armazenamento

1.  Entre no [Portal de Gerenciamento][].

2.  Clique em **Criar Novo**, em **Armazenamento** e em **Criação Rápida**.

    ![NewStorageAccount][]

3.  Na **URL**, digite um nome de subdomínio a ser usado na URL da conta de armazenamento. Para acessar um objeto no armazenamento, você acrescentará o local do objeto ao ponto de extremidade. Por exemplo, a URL para acessar um blob deve ser [http://\*minhaconta][]*.blob.core.windows.net/*meucontêiner*/*meublob\*.

4.  Em **Região/Grupo de Afinidade**, selecione uma região ou um grupo de afinidade para o armazenamento. Selecione um grupo de afinidade em vez de uma região se desejar ter os serviços de armazenamento no mesmo data center com outros serviços do Azure que você está usando. Isso pode melhorar o desempenho e nenhum encargo será incorrido para a saída.

    > [WACOM.NOTE] Para criar um grupo de afinidade, abra a área **Configurações** do Portal de Gerenciamento, clique em **Grupos de Afinidade** e em **Adicionar um grupo de afinidade** ou no botão **Adicionar**. Você também pode criar e gerenciar os grupos de afinidade usando o Portal de Gerenciamento de Serviços do Azure. Para obter mais informações, consulte [Operações em grupos de afinidade][].

5.  Se você tiver mais de uma assinatura do Azure, o campo **Assinatura** será exibido. Em **Assinatura**, digite a assinatura do Azure com a qual você deseja usar a conta de armazenamento. Você pode criar até cinco contas de armazenamento para uma assinatura.

6.  Em **Replicação**, selecione o nível de replicação desejado para sua conta de armazenamento.

    [WACOM.INCLUDE [storage-replication-options][]]

7.  Clique em **Criar Conta de Armazenamento**.

    Pode levar alguns minutos para criar sua conta de armazenamento. Para verificar o status, você pode monitorar as notificações na parte inferior do portal. Depois que a conta de armazenamento for criada, sua nova conta de armazenamento terá o status **Online** e estará pronta para uso.

    ![StoragePage][]

## <span id="next"></span></a>Próximas etapas

-   Para saber mais sobre o Armazenamento do Azure, consulte a documentação de Armazenamento do Azure em [azure.com][] e no [MSDN][].

-   Visite o [Blog da equipe do Armazenamento do Azure][].

  [Escalabilidade e metas de desempenho do Armazenamento do Azure]: http://msdn.microsoft.com/en-us/library/dn249410.aspx
  [Como: Criar uma conta de armazenamento]: #create
  [Próximas etapas]: #next
  [Portal de Gerenciamento]: https://manage.windowsazure.com
  [NewStorageAccount]: ./media/storage-create-storage-account/storage_NewStorageAccount.png
  [http://\*minhaconta]: http://*myaccount
  [Operações em grupos de afinidade]: http://msdn.microsoft.com/en-us/library/windowsazure/ee460798.aspx
  [storage-replication-options]: ../includes/storage-replication-options.md
  [StoragePage]: ./media/storage-create-storage-account/Storage_StoragePage.png
  [azure.com]: http://azure.microsoft.com/en-us/documentation/services/storage/
  [MSDN]: http://msdn.microsoft.com/en-us/library/gg433040.aspx
  [Blog da equipe do Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
