<properties
	pageTitle="Gerenciar uma conta do Banco de Dados de Documentos usando o Portal do Azure | Microsoft Azure"
	description="Saiba como gerenciar sua conta do Banco de Dados de Documentos pelo Portal do Azure. Encontre um guia sobre como usar o Portal do Azure para exibir, copiar, excluir e acessar contas."
	keywords="Portal do Azure, banco de dados de documentos, azure, Microsoft azure"
	services="documentdb"
	documentationCenter=""
	authors="AndrewHoh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/20/2016"
	ms.author="anhoh"/>

# Como gerenciar uma conta do Banco de Dados de Documentos

Saiba como definir a consistência global e gerenciar várias regiões para disponibilidade global de dados no Banco de Dados de Documentos do Azure. Além disso, saiba como trabalhar com chaves e excluir uma conta no Portal do Azure.

## <a id="consistency"></a>Gerenciar as configurações de consistência do Banco de Dados de Documentos

A seleção do nível certo de consistência depende da semântica do aplicativo. Você deve se familiarizar com os níveis de consistência disponíveis no Banco de Dados de Documentos: [Usando níveis de consistência para maximizar a disponibilidade e o desempenho no Banco de Dados de Documentos][consistency]. O Banco de Dados de Documentos fornece garantias de consistência, disponibilidade e desempenho, em cada nível de consistência disponível para sua conta de banco de dados. A configuração da conta do banco de dados com um nível de consistência strong exige que seus dados sejam confinados em uma única região do Azure, e não que estejam globalmente disponíveis. Por outro lado, os níveis de consistência flexíveis — bounded staleness, session ou eventual — permitem associar qualquer número de regiões do Azure à sua conta de banco de dados. As etapas simples a seguir mostram como selecionar o nível de consistência padrão para sua conta de banco de dados.

### Para especificar a consistência padrão para uma conta do Banco de Dados de Documentos

1. No [Portal do Azure](https://portal.azure.com/), acesse sua conta do Banco de Dados de Documentos.
2. Na folha da conta, se a folha **Configurações** ainda não estiver aberta, clique em **Todas as configurações**. ![Sessão de consistência padrão][5]

3. Na folha **Todas as Configurações**, clique na entrada **Consistência Padrão** em **Recurso**. ![Sessão de consistência padrão][6]
4. Na folha **Consistência Padrão**, selecione o novo nível de consistência e clique em **Salvar**.
5. O andamento da operação pode ser monitorado por meio do hub de Notificações do Portal do Azure.

> [AZURE.NOTE] Pode ser que demore um pouco para que uma alteração na configuração padrão da consistência entre em vigor na sua conta do Banco de Dados de Documentos.

## <a id="addregion"></a>Adicionar regiões

O Banco de Dados de Documentos está disponível na maioria das [regiões do Azure][azureregions]. Após a seleção do nível de consistência padrão para sua conta de banco de dados, você pode associar uma ou mais regiões (dependendo da sua escolha do nível de consistência padrão e das necessidades de distribuição global).

> [AZURE.NOTE] No momento, novas regiões podem ser adicionadas às novas Contas do Banco de Dados de Documentos criadas a partir de 13 de junho de 2016. Selecione "Banco de Dados de Documentos do Azure — conta do banco de dados em várias regiões" no Marketplace para criar uma conta de várias regiões. As contas criadas antes de 13 de junho serão habilitadas para disponibilidade global em um futuro próximo.

1. No [Portal do Azure](https://portal.azure.com/), no Jumpbar, clique em **Contas do Banco de Dados de Documentos**.
2. Na folha **Conta do Banco de Dados de Documentos**, selecione a conta do banco de dados a ser modificada.
3. Na folha da conta, se a folha **Todas as Configurações** ainda não estiver aberta, clique em **Todas as configurações**.
4. Na folha **Todas as Configurações**, clique em **Adicionar/Remover Regiões**. ![Adicionar regiões em Conta do Banco de Dados de Documentos > Configurações > Adicionar/remover Regiões][1]
5. Na folha **Adicionar/Remover Regiões**, selecione as regiões a serem adicionadas ou removidas e clique em **OK**. Há um custo para adicionar regiões. Consulte a página de preços para obter mais informações.

    ![Clicar nas regiões no mapa para adicioná-las ou removê-las][2]

### Selecionar regiões

Ao configurar duas ou mais regiões, é recomendável que as regiões sejam selecionadas com base nos pares de região descritos no artigo [Continuidade dos negócios e recuperação de desastres (BCDR): Regiões Emparelhadas do Azure][bcdr].

Especificamente, ao configurar para várias regiões, certifique-se de selecionar o mesmo número de regiões (+/- 1 para par/ímpar) de cada uma das colunas de região emparelhada. Por exemplo, se quiser implantar em 4 regiões dos EUA, você seleciona 2 regiões dos EUA na coluna esquerda e 2 na coluna direita. Assim, a definição apropriada seria: Oeste dos EUA, Leste dos EUA, Centro-Norte dos EUA e Centro-Sul dos EUA.

É importante seguir essa instrução quando apenas 2 regiões são configuradas para cenários de recuperação de desastre. Para mais de 2 regiões, seguir essa instrução é uma boa prática, mas não crítica, pois algumas da regiões selecionadas adotam esse emparelhamento.

## <a id="selectwriteregion"></a>Selecionar a região de gravação

Embora todas as regiões associadas à sua conta de banco de dados do Banco de Dados de Documentos possam atender às leituras (tanto as paginadas de único item, como a de vários itens) e consultas, apenas uma região pode receber ativamente as solicitações de gravação (inserção, upsert, substituição, exclusão). Para definir a região de gravação ativa, faça o seguinte


1. Na folha **Conta do Banco de Dados de Documentos**, selecione a conta do banco de dados a ser modificada.
2. Na folha da conta, se a folha **Todas as Configurações** ainda não estiver aberta, clique em **Todas as configurações**.
3. Na folha **Todas as Configurações**, clique em **Prioridade da Região de Gravação**. ![Alterar a região de gravação em Conta do Banco de Dados de Documentos > Configurações > Adicionar/Remover Regiões][3]
4. Clique e arraste as regiões para ordenar a lista de regiões. A primeira região na lista de regiões é a região de gravação ativa. ![Alterar a região de gravação ao reordenar a lista de regiões em Conta do Banco de Dados de Documentos > Configurações > Alterar Regiões de Gravação][4]

## <a id="keys"></a>Exibir, copiar e regenerar chaves de acesso
Quando você cria uma conta do Banco de Dados de Documentos, o serviço gera duas chaves de acesso mestras que podem ser usadas para autenticação quando a conta do Banco de Dados de Documentos é acessada. Ao fornecer as duas chaves de acesso, o Banco de Dados de Documentos permite regenerar as chaves sem interrupção para a sua conta do Banco de Dados de Documentos.

No [Portal do Microsoft Azure](https://portal.azure.com/), acesse a folha **Chaves** na barra **Fundamentos** da folha de sua **Conta do Banco de Dados de Documentos** para exibir, copiar e regenerar as chaves de acesso que são usadas para acessar sua conta do Banco de Dados de Documentos.

![Captura de tela do Portal do Azure, folha Chaves](./media/documentdb-manage-account/keys.png)

Outra opção é acessar a entrada **Chaves** na folha **Todas as Configurações**.

![Folha Todas as Configurações e Chaves](./media/documentdb-manage-account/allsettingskeys.png)

> [AZURE.NOTE] A folha **Chaves** também inclui cadeias de conexão primárias e secundárias que podem ser usadas para se conectar à sua conta na [Ferramenta de Migração de Dados](documentdb-import-data.md).

Ele também inclui chaves somente leitura para fornecer aos usuários o acesso somente leitura ao Banco de Dados de Documentos. Leituras e consultas são operações somente leitura, ao contrário de criações, exclusões e substituições.

### Exibir e copiar uma chave de acesso no Portal do Azure

1. No [Portal do Azure](https://portal.azure.com/), acesse sua conta do Banco de Dados de Documentos.
2. Na barra **Fundamentos** da folha **Conta do Banco de Dados de Documentos**, clique em **Chaves**.
3. Na folha **Chaves**, clique no botão **Copiar** à direita da chave que você quer copiar. ![Exibir e copiar uma chave de acesso no Portal do Azure, folha Chaves](./media/documentdb-manage-account/copykeys.png)

### Regenerar chaves de acesso

Você deve alterar as chaves de acesso da sua conta do Banco de Dados de Documentos periodicamente para ajudar a manter as conexões mais seguras. Duas chaves de acesso são atribuídas para permitir que você mantenha conexões com a conta do Banco de Dados de Documentos usando uma chave de acesso enquanto regenera a outra chave de acesso.

> [AZURE.WARNING] A regeneração de suas chaves de acesso afeta quaisquer aplicativos que são dependentes da chave atual. Todos os clientes que usam a chave de acesso para acessar a conta do Banco de Dados de Documentos devem ser atualizados para usarem a nova chave.

Se tiver aplicativos ou serviços de nuvem usando a conta do Banco de Dados de Documentos, você perderá as conexões se regenerar chaves, a menos que você as reverta. As etapas a seguir descrevem o processo envolvido ao reverter suas chaves.

1. Atualize as chaves de acesso no código do aplicativo para fazer referência à chave de acesso secundária da conta do Banco de Dados de Documentos.

2. Regenere a chave de acesso primária de sua conta do Banco de Dados de Documentos. No [Portal do Azure](https://portal.azure.com/), acesse sua conta do Banco de Dados de Documentos.

3. Na barra **Fundamentos** da folha **Conta do Banco de Dados de Documentos**, clique em **Chaves**.

4. Na folha de **Chaves**, clique no comando **Regenerar Principal** e, em seguida, clique em **Ok** para confirmar que você quer gerar uma nova chave.

5. Uma vez que você verificou que a nova chave está disponível para uso(aproximadamente 5 minutos após a regeneração), atualize a chave de acesso em seu código do aplicativo para fazer referência à nova chave de acesso principal.

6. Regenere a chave de acesso secundária.


> [AZURE.NOTE] Levará alguns minutos para que a chave gerada recentemente possa ser usada para acessar sua conta do Banco de Dados de Documentos.

## <a id="delete"></a> Excluir uma conta do Banco de Dados de Documentos
Para remover uma conta do Banco de Dados de Documentos do Portal do Azure que não está mais sendo usada, use o comando **Delete** na folha **Conta do Banco de Dados de Documentos**.

![Como excluir uma conta do Banco de Dados de Documentos no Portal do Azure](./media/documentdb-manage-account/deleteaccountconfirmation.png)


1. No [Portal do Azure](https://portal.azure.com/), acesse a Conta do Banco de Dados de Documentos que deseja excluir.
2. Na folha **Conta do Banco de Dados de Documentos**, clique em **Excluir Conta**.
3. Na folha de confirmação de resultado, digite o nome da Conta do Banco de Dados de Documentos para confirmar que você quer excluir a conta.
4. Clique no botão **Excluir** na folha de confirmação.

## <a id="next"></a>Próximas etapas

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
[azureregions]: https://azure.microsoft.com/pt-BR/regions/#services
[offers]: https://azure.microsoft.com/pt-BR/pricing/details/documentdb/

<!---HONumber=AcomDC_0727_2016-->