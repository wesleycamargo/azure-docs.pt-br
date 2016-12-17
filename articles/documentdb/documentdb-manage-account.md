---
title: Gerenciar uma conta do DocumentDB usando o Portal do Azure | Microsoft Docs
description: Saiba como gerenciar sua conta do Banco de Dados de Documentos pelo Portal do Azure. Encontre um guia sobre como usar o Portal do Azure para exibir, copiar, excluir e acessar contas.
keywords: Portal do Azure, banco de dados de documentos, azure, Microsoft azure
services: documentdb
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f38b5d6127897ba2b083e0f3bb41562650214ae9


---
# <a name="how-to-manage-a-documentdb-account"></a>Como gerenciar uma conta do Banco de Dados de Documentos
Saiba como definir a consistência global, trabalhar com chaves e excluir uma conta do DocumentDB no portal do Azure.

## <a name="a-idconsistencyamanage-documentdb-consistency-settings"></a><a id="consistency"></a>Gerenciar as configurações de consistência do Banco de Dados de Documentos
A seleção do nível certo de consistência depende da semântica do aplicativo. Você deve se familiarizar com os níveis de consistência disponíveis no DocumentDB lendo [Níveis de consistência para maximizar a disponibilidade e o desempenho no DocumentDB][consistency]. O DocumentDB fornece garantias de consistência, disponibilidade e desempenho, em cada nível de consistência disponível para sua conta de banco de dados. A configuração da conta do banco de dados com um nível de consistência Strong exige que seus dados sejam confinados em uma única região do Azure, e não globalmente disponíveis. Por outro lado, os níveis de consistência flexíveis — bounded staleness, session ou eventual — permitem associar qualquer número de regiões do Azure à sua conta de banco de dados. As etapas simples a seguir mostram como selecionar o nível de consistência padrão para sua conta de banco de dados. 

### <a name="to-specify-the-default-consistency-for-a-documentdb-account"></a>Para especificar a consistência padrão para uma conta do Banco de Dados de Documentos
1. No [portal do Azure](https://portal.azure.com/), acesse sua conta do DocumentDB.
2. Na folha da conta, clique em **Consistência padrão**.
3. Na folha **Consistência Padrão**, selecione o novo nível de consistência e clique em **Salvar**.
    ![Sessão de consistência padrão][5]

## <a name="a-idkeysaview-copy-and-regenerate-access-keys"></a><a id="keys"></a>Exibir, copiar e regenerar chaves de acesso
Quando você cria uma conta do Banco de Dados de Documentos, o serviço gera duas chaves de acesso mestras que podem ser usadas para autenticação quando a conta do Banco de Dados de Documentos é acessada. Ao fornecer as duas chaves de acesso, o Banco de Dados de Documentos permite regenerar as chaves sem interrupção para a sua conta do Banco de Dados de Documentos. 

No [portal do Azure](https://portal.azure.com/), acesse a folha **Chaves** no menu de recursos da **Conta do DocumentDB** para exibir, copiar e regenerar as chaves de acesso que são usadas para acessar sua conta do DocumentDB.

![Captura de tela do Portal do Azure, folha Chaves](./media/documentdb-manage-account/keys.png)

> [!NOTE]
> A folha **Chaves** também inclui cadeias de conexão primárias e secundárias que podem ser usadas para se conectar à sua conta na [Ferramenta de Migração de Dados](documentdb-import-data.md).
> 
> 

Chaves somente leitura também estão disponíveis nessa folha. Leituras e consultas são operações somente leitura, ao contrário de criações, exclusões e substituições.

### <a name="copy-an-access-key-in-the-azure-portal"></a>Copiar uma chave de acesso no portal do Azure
Na folha **Chaves**, clique no botão **Copiar** à direita da chave que você quer copiar.

![Exibir e copiar uma chave de acesso no Portal do Azure, folha Chaves](./media/documentdb-manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>Regenerar chaves de acesso
Você deve alterar as chaves de acesso da sua conta do Banco de Dados de Documentos periodicamente para ajudar a manter as conexões mais seguras. Duas chaves de acesso são atribuídas para permitir que você mantenha conexões com a conta do Banco de Dados de Documentos usando uma chave de acesso enquanto regenera a outra chave de acesso.

> [!WARNING]
> A regeneração de suas chaves de acesso afeta quaisquer aplicativos que são dependentes da chave atual. Todos os clientes que usam a chave de acesso para acessar a conta do Banco de Dados de Documentos devem ser atualizados para usarem a nova chave.
> 
> 

Se tiver aplicativos ou serviços de nuvem usando a conta do Banco de Dados de Documentos, você perderá as conexões se regenerar chaves, a menos que você as reverta. As etapas a seguir descrevem o processo envolvido ao reverter suas chaves.

1. Atualize as chaves de acesso no código do aplicativo para fazer referência à chave de acesso secundária da conta do Banco de Dados de Documentos.
2. Regenere a chave de acesso primária de sua conta do Banco de Dados de Documentos. No [Portal do Azure](https://portal.azure.com/), acesse sua conta do Banco de Dados de Documentos.
3. Na folha **Conta do DocumentDB**, clique em **Chaves**.
4. Na folha **Chaves**, clique no botão Regenerar e clique em **Ok** para confirmar que você quer gerar uma nova chave.
    ![Regenerar chaves de acesso](./media/documentdb-manage-account/regenerate-keys.png)
5. Uma vez que você verificou que a nova chave está disponível para uso(aproximadamente 5 minutos após a regeneração), atualize a chave de acesso em seu código do aplicativo para fazer referência à nova chave de acesso principal.
6. Regenere a chave de acesso secundária.
   
    ![Regenerar chaves de acesso](./media/documentdb-manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Levará alguns minutos para que a chave gerada recentemente possa ser usada para acessar sua conta do Banco de Dados de Documentos.
> 
> 

## <a name="get-the-connection-string"></a>Obtenha a cadeia de conexão
Para recuperar sua cadeia de conexão, faça o seguinte: 

1. No [portal do Azure](https://portal.azure.com), acesse sua conta do DocumentDB.
2. No menu de recursos, clique em **Chaves**.
3. Clique no botão **Copiar** ao lado da caixa **Cadeia de conexão primária** ou **Cadeia de conexão secundária** caixa. 

Se você estiver usando a cadeia de conexão na [Ferramenta de migração do DocumentDB](documentdb-import-data.md), acrescente o nome do banco de dados ao final da cadeia de conexão. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a name="a-iddeletea-delete-a-documentdb-account"></a><a id="delete"></a> Excluir uma conta do Banco de Dados de Documentos
Para remover uma conta do DocumentDB do Portal do Azure que não está mais sendo usada, use o comando **Excluir Conta** na folha **Conta do DocumentDB**.

![Como excluir uma conta do Banco de Dados de Documentos no Portal do Azure](./media/documentdb-manage-account/deleteaccount.png)

1. No [portal do Azure](https://portal.azure.com/), acesse a conta do DocumentDB que quer excluir.
2. Na folha **Conta do DocumentDB**, clique em **Mais** e em **Excluir Conta**. Ou então, clique com o botão direito do mouse no nome do banco de dados e clique em **Excluir Conta**.
3. Na folha de confirmação resultante, digite o nome da conta do DocumentDB para confirmar que você quer excluir a conta.
4. Clique no botão **Excluir** .

![Como excluir uma conta do Banco de Dados de Documentos no Portal do Azure](./media/documentdb-manage-account/delete-account-confirm.png)

## <a name="a-idnextanext-steps"></a><a id="next"></a>Próximas etapas
Saiba como [começar com sua conta do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Para saber mais sobre o Banco de Dados de Documentos, consulte a documentação do Banco de Dados de Documentos do Azure em [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).

<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/



<!--HONumber=Nov16_HO3-->


