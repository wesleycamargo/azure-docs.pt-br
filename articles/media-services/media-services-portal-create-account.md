---
title: " Criar uma conta dos Serviços de Mídia do Azure com o Portal do Azure | Microsoft Docs"
description: "Este tutorial orienta você pelas etapas de criação de uma conta do Serviços de Mídia do Azure com o portal do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8e03f7b3d4b9c17b08aca033f44ebfc5f3332eec


---
# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Criar uma conta dos Serviços de Mídia do Azure usando o portal do Azure
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)
> 
> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

O portal do Azure fornece uma maneira de criar rapidamente uma conta de Serviços de Mídia do Azure (AMS). Você pode usar sua conta para acessar os Serviços de Mídia que permitem que você armazene, criptografe, codifique, gerencie e transmita conteúdo de mídia no Azure. Quando você cria uma conta de Serviços de Mídia, você também cria uma conta de armazenamento associada (ou usa uma existente) na mesma região geográfica que a conta de Serviços de Mídia.

Este artigo explica alguns conceitos comuns e mostra como criar uma conta do Serviços de Mídia com o portal do Azure.

## <a name="concepts"></a>Conceitos
O acesso aos Serviços de Mídia requer duas contas associadas:

* Uma conta dos Serviços de Mídia. Sua conta fornece acesso a um conjunto de Serviços de Mídia baseado na nuvem que está disponível no Azure. Uma conta de Serviços de Mídia não armazena o conteúdo de mídia real. Em vez disso, armazena metadados sobre o conteúdo de mídia e os trabalhos de processamento de mídia em sua conta. No momento em que cria a conta, você seleciona uma região de Serviços de Mídia disponível. A região selecionada é um data center que armazena os registros dos metadados de sua conta.
  
    As regiões de Serviços de Mídia Disponíveis (AMS) incluem o seguinte: Norte da Europa, Europa Ocidental, Oeste dos EUA, Leste dos EUA, Sudeste da Ásia, Ásia Oriental, Oeste do Japão, Leste do Japão. Os Serviços de Mídia não usam grupos de afinidade.
  
    O AMS agora também está disponível nos seguintes data centers: Sul do Brasil, Índia Ocidental, Sul da Índia e Índia Central. Agora você pode usar o portal do Azure para criar contas do Serviço de Mídia e realizar diversas tarefas descritas aqui. No entanto, a Codificação Ativa não está habilitada nesses datacenters. Além disso, nem todos os tipos de Unidades Reservadas para Codificação estão disponíveis nesses datacenters.
  
  * Sul do Brasil: somente as Unidades Reservadas para Codificação Standard e Básico estão disponíveis.
  * Índia Ocidental, Sul da Índia: 
* Uma conta de armazenamento do Azure. As contas de armazenamento devem estar localizadas na mesma região geográfica que a conta dos Serviços de Mídia. Quando cria uma conta de Serviços de Mídia, você pode escolher uma conta de armazenamento existente na mesma região ou criar uma nova conta de armazenamento na mesma região. Se você excluir uma conta de Serviços de Mídia, os blobs em sua conta de armazenamento relacionada não serão excluídos.

## <a name="create-an-ams-account"></a>Criar uma conta do AMS
As etapas nesta seção mostram como criar uma conta do AMS.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Clique em **+ Novo** > **Web + Móvel** > **Serviços de Mídia**.
   
    ![Criar Serviços de Mídia](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. Em **CRIAR CONTA DOS SERVIÇOS DE MÍDIA** , insira os valores necessários.
   
    ![Criar Serviços de Mídia](./media/media-services-portal-vod-get-started/media-services-new3.png)
   
   1. Em **Nome da Conta**, insira o nome da nova conta AMS. Um nome de conta de Serviços de Mídia deve ser composto de letras minúsculas ou números, sem espaços, e deve ter de 3 a 24 caracteres de comprimento.
   2. Em Assinatura, selecione uma das diferentes assinaturas do Azure às quais você tem acesso.
   3. Em **Grupo de Recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é uma coleção de recursos que compartilham o ciclo de vida, as permissões e as políticas. Saiba mais [aqui](../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. Em **Localização**, selecione a região geográfica que será usada para armazenar os registros de mídia e metadados para sua conta de Serviços de Mídia. Essa região será usada para processar e transmitir a mídia. Somente as regiões de Serviços de Mídia disponíveis são exibidas na caixa da lista suspensa. 
   5. Em **Conta de Armazenamento**, selecione uma conta de armazenamento para fornecer armazenamento de blobs do conteúdo de mídia de sua conta de Serviços de Mídia. Você pode selecionar uma conta de armazenamento existente na mesma região geográfica que sua conta de Serviços de Mídia ou criar uma conta de armazenamento. É criada uma nova conta de armazenamento na mesma região. As regras para nomes de contas de armazenamento são as mesmas que para contas de Serviços de Mídia.
      
       Saiba mais sobre o armazenamento [aqui](../storage/storage-introduction.md).
   6. Selecione **Fixar no painel** para ver o progresso da implantação da conta.
4. Clique em **Criar** na parte inferior do formulário.
   
    Quando a conta for criada com êxito, o status mudará para **Executando**. 
   
    ![Configurações dos Serviços de Mídia](./media/media-services-portal-vod-get-started/media-services-settings.png)
   
    Para gerenciar sua conta AMS (por exemplo, carregar vídeos, codificar ativos, monitorar o andamento do trabalho), use a janela **Configurações** .

## <a name="manage-keys"></a>Gerenciar Chaves
Será necessário o nome da conta e as informações da chave primária para acessar a conta de Serviços de Mídia de modo programático.

1. No Portal do Azure, selecione sua conta. 
   
    A janela **Configurações** aparece à direita. 
2. Na janela **Configurações**, selecione **Chaves**. 
   
    A janela **Gerenciar chaves** mostra o nome da conta e as chaves primária e secundária são exibidas. 
3. Pressione o botão Copiar para copiar os valores.
   
    ![Chaves dos Serviços de Mídia](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="next-steps"></a>Próximas etapas
Agora você pode carregar arquivos em sua conta do AMS. Para saber mais, veja [Carregar arquivos](media-services-portal-upload-files.md).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO2-->


