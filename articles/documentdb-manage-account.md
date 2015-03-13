<properties 
	pageTitle="Gerenciar uma conta do Banco de Dados de Documentos | Azure" 
	description="Aprenda a gerenciar sua conta do Banco de Dados de Documentos." 
	services="documentdb" 
	documentationCenter="" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="stbaro"/>
# Como gerenciar uma Conta DocumentDB

## Sumário

-   [Como: Exibir, copiar e regenerar as chaves de acesso do DocumentDB](#keys)
-   [Como: Gerenciar as configurações de consistência do DocumentDB](#consistency)
-   [Como: Gerenciar as configurações de capacidade do DocumentDB](#capacity)
-   [Como: Excluir uma conta DocumentDB](#delete)
-   [Próximas etapas](#next)

## <span id="keys"></span></a>Como: Exibir, copiar e regenerar chaves de acesso

Ao criar uma conta DocumentDB, duas chaves de acesso mestre são geradas, o que pode ser usado para autenticação quando a conta DocumentDB é acessada. Ao fornecer as duas chaves de acesso, o DocumentDB permite que você regenere as chaves sem interrupção para a sua conta
DocumentDB.

No portal de [visualização de gerenciamento do Azure](https://portal.azure.com/)[](http://manage.windowsazure.com), acesse a parte das **Chaves** a partir da folha de sua conta DocumentDB para exibir,copiar e regenerar as chaves de acesso que são usadas para acessar sua
Conta DocumentDB.

![](./media/documentdb-manage-account/image002.jpg)

### Exibir e copiar uma chave de acesso

1.      No portal de [visualização de gerenciamento do Azure](https://portal.azure.com/), acesse a Conta DocumentDB. 

2.      Nas lentes de Resumo, clique em **Chaves**.

3.      Na folha Chaves, clique no botão **Copiar** à direita da chave que você quer copiar.

4.      Pressione Ctrl+C para copiar a chave.

  ![](./media/documentdb-manage-account/image004.jpg)

### Regenerar chaves de acesso

Você deve alterar as chaves de acesso de sua conta DocumentDB periodicamente para ajudar a manter as conexões mais seguras. Duas chaves de acesso são atribuídas para permitir que você mantenha conexões com a conta DocumentDB usando uma chave de acesso enquanto regenera a outra chave de acesso.

**Aviso**

Regenerar suas chaves de acesso afeta quaisquer aplicativos que são dependentes da chave atual. Todos os clientes que usam a chave de acesso para acessar a conta DocumentDB devem ser atualizados para usarem a nova chave.

Se tiver aplicativos ou serviços de nuvem usando a conta DocumentDB, você perderá as conexões se regenerar chaves, a menos que você as reverta. Este é um processo que você pode seguir:

1.      Atualize as chaves de acesso no código do aplicativo para fazer referência à chave de acesso secundária da conta DocumentDB.

2.      Regenere a chave de acesso primária para sua conta DocumentDB. No [portal de visualização de gerenciamento do Azure](https://portal.azure.com/), acesse a Conta DocumentDB.

3.      Nas lentes de Resumo, clique em **Chaves**.

4.      Na folha de Chaves, clique no comando **Regenerar Principal** e, em seguida, clique em **Ok** para confirmar que você quer gerar uma nova chave.

5.      Uma vez que você verificou que a nova chave está disponível para us (aproximadamente 5 minutos após a regeneração), atualize a chave de acesso em seu código do aplicativo para fazer referência da nova chave de acesso principal..

6.      Regenerar a chave de acesso secundária.

*Observe que levará alguns minutos antes que a chave gerada recentemente possa ser usada para acessar sua conta DocumentDB.*

## <span id="consistency"></span></a>Como: Gerenciar as configurações de consistência do DocumentDB

O DocumentDB suporta quatro níveis de consistência de dados configuráveis pelo usuário bem definidos para permitir que os desenvolvedores façam compensações previsíveis entre consistência, disponibilidade e latência.

·         A consistência **Forte** garante que as operações de leitura sempre devolvem o valor que foi gravado por último.

·         A consistência **desatualizada limitada** garante que as leituras não estejam muito desatualizadas. Ela especificamente garante que as leituras não sejam mais do que versões K mais antigas do que a última versão gravada.

·         A consistência da **sessão** garante leituras monotônicas (você nunca lê dados antigos, e novos e antigos novamente), gravações monotônicas (as gravações são ordenadas) e que você lê as gravações mais recentes dentro de qualquer ponto de vista do cliente único.

·         A consistência**eventual** garante que as operações de leitura sempre leiam um subconjunto de gravações válido e, eventualmente, convergirá.

*Observe que, por padrão, as contas DocumentDB são provisionadas com a consistência do nível de Sessão. Para informações adicionais nas configurações de consistência DocumentDB, consulte a seção de [Nível de Consistência](http://go.microsoft.com/fwlink/p/?LinkId=402365).*

### Para especificar a consistência padrão para uma Conta DocumentDB

1.      No portal de [visualização de gerenciamento do Azure](https://portal.azure.com/), acesse a Conta DocumentDB. 

2.      Nas lentes de Configuração, clique em **Consistência**.

3.      Na folha de Consistência Padrão, selecione o nível de consistência padrão que você quer para sua conta DocumentDB.

4.      Clique em **Salvar**.

5.      O progresso da operação pode ser monitorado através do Hub de notificações do portal de visualização de gerenciamento do Azure.

 ![](./media/documentdb-manage-account/image005.png)

 ![](./media/documentdb-manage-account/image006.png)

*Observe que levará alguns minutos antes da mudança para as configurações de consistência padrão terem efeito em toda a sua conta DocumentDB.*

## <span id="capacity"></span></a>Como: Gerenciar as configurações de capacidade do DocumentDB

O DocumentDB do Microsoft Azure permite que você escale de forma elástica conforme as demandas do seu aplicativo muda durante seu ciclo de vida. A escala do DocumentDB é cumprida ao aumentar a capacidade de sua Conta de banco de dados do DocumentDB ateavés do portal de visualização de gerenciamento do Azure.

Ao criar uma Conta de banco de dados, ela é provisionada com o armazenamento do banco de dados e reservada em conformidade. A qualquer momento você pode mudar o armazenamento do banco de dados provisionado e, em conformidade, para sua conta ao adicionar ou remover as unidades de capacidade através do portal de visualização de gerenciamento do Azure. 

### Para adicionar ou remover as unidades de capacidade

1.      No portal de [visualização de gerenciamento do Azure](https://portal.azure.com/), acesse a Conta DocumentDB. 

2.      Nas lentes de Uso, clique em **Escala**.

3.      Na folha de Escala, especifique o número de unidades de capacidade que você quer para sua conta DocumentDB.

4.      Clique em **Salvar** (observe que levará alguns minutos para a operação de escala completar, você pode monitorar o progresso através do Hub de notificações do portal de visualização de gerenciamento do Azure).

*Observe que a Visualização do DocumentDB suporte uma capacidade máxima de 5 unidades por Conta DocumentDB.*

![](./media/documentdb-manage-account/image007.png)

 

## <span id="delete"></span></a>Como: Excluir uma conta DocumentDB

Para remover uma conta DocumentDB que você não esteja mais usando, use o comando **Excluir** na folha de Conta DocumentDB.

**Aviso**

*Na versão de visualização, não existe forma de restaurar o conteúdo de uma conta DocumentDB excluída. Excluindo uma conta DocumentDB excluirá todos os recursos da conta, incluindo bancos de dados, coletas, documentos e anexos.*

![](./media/documentdb-manage-account/image009.png)

1.      No portal de [visualização de gerenciamento do Azure](https://portal.azure.com/), acesse a Conta DocumentDB que você quer excluir. 

2.      Na folha da conta, clique no comando **Excluir**.

3.      Na folha de confirmação de resultado, digite o nome da Conta DocumentDB para confirmar que você quer excluir a conta.

4.      Clique no botão **Excluir** na folha de confirmação.

## <span id="next"></span></a>Próximas etapas

-   Saiba como [começar com sua conta DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).
-   Para aprender mais sobre DocumentDB, consulte a documentação do Azure DocumentDB
     em
    [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409)

 

  [Como: Exibir, copiar e regenerar as chaves de acesso do DocumentDB]: #keys
  [Como: Gerenciar as configurações de consistência do DocumentDB]: #consistency
  [Como: Gerenciar as configurações de capacidade do DocumentDB]: #capacity
  [Como: Excluir uma conta DocumentDB]: #delete
  [Próximas etapas]: #next
  [visualização de gerenciamento do Azure]: https://portal.azure.com/
  []: http://manage.windowsazure.com
  []: ./media/documentdb-manage-account/image002.jpg
  [Nível de Consistência]: http://go.microsoft.com/fwlink/p/?LinkId=402365
  [começar com sua conta DocumentDB]:http://go.microsoft.com/fwlink/p/?LinkId=402364
  [azure.com]: http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409

<!--HONumber=46--> 
