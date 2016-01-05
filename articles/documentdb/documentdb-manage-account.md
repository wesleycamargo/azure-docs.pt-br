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
	ms.date="11/18/2015" 
	ms.author="anhoh"/>

# Como gerenciar uma conta do Banco de Dados de Documentos

Saiba como trabalhar com chaves e configurações de consistência e saiba como excluir uma conta no Portal do Azure.

## <a id="keys"></a>Exibir, copiar e regenerar chaves de acesso
Quando você cria uma conta do Banco de Dados de Documentos, o serviço gera duas chaves de acesso mestras que podem ser usadas para autenticação quando a conta do Banco de Dados de Documentos é acessada. Ao fornecer as duas chaves de acesso, o Banco de Dados de Documentos permite regenerar as chaves sem interrupção para a sua conta do Banco de Dados de Documentos.

No [portal do Microsoft Azure](https://portal.azure.com/), acesse a parte das **Chaves** na folha de sua **Conta do Banco de Dados de Documentos** para exibir, copiar e regenerar as chaves de acesso que são usadas para acessar sua conta do Banco de Dados de Documentos.

![Captura de tela do Portal do Azure, folha Chaves](media/documentdb-manage-account/keys.png)

### Exibir e copiar uma chave de acesso no Portal do Azure

1.      No [Portal do Azure](https://portal.azure.com/), acesse sua conta do Banco de Dados de Documentos. 

2.      Na lente **Resumo**, clique em **Chaves**.

3.      Na folha **Chaves**, clique no botão **Copiar** à direita da chave que você quer copiar.

  ![Exibir e copiar uma chave de acesso no Portal do Azure, folha Chaves](./media/documentdb-manage-account/image004.jpg)

### Regenerar chaves de acesso

Você deve alterar as chaves de acesso da sua conta do Banco de Dados de Documentos periodicamente para ajudar a manter as conexões mais seguras. Duas chaves de acesso são atribuídas para permitir que você mantenha conexões com a conta do Banco de Dados de Documentos usando uma chave de acesso enquanto regenera a outra chave de acesso.

> [AZURE.WARNING]A regeneração de suas chaves de acesso afeta quaisquer aplicativos que são dependentes da chave atual. Todos os clientes que usam a chave de acesso para acessar a conta do Banco de Dados de Documentos devem ser atualizados para usarem a nova chave.

Se tiver aplicativos ou serviços de nuvem usando a conta do Banco de Dados de Documentos, você perderá as conexões se regenerar chaves, a menos que você as reverta. As etapas a seguir descrevem o processo envolvido ao reverter suas chaves.

1.      Atualize as chaves de acesso no código do aplicativo para fazer referência à chave de acesso secundária da conta do Banco de Dados de Documentos.

2.      Regenere a chave de acesso primária de sua conta do Banco de Dados de Documentos. No [Portal do Azure](https://portal.azure.com/), acesse sua conta do Banco de Dados de Documentos.

3.      Na lente Resumo, clique em **Chaves**.

4.      Na folha de **Chaves**, clique no comando **Regenerar Principal** e, em seguida, clique em **Ok** para confirmar que você quer gerar uma nova chave.

5.      Uma vez que você verificou que a nova chave está disponível para uso(aproximadamente 5 minutos após a regeneração), atualize a chave de acesso em seu código do aplicativo para fazer referência à nova chave de acesso principal.

6.      Regenere a chave de acesso secundária.

*Observe que levará alguns minutos antes que a chave gerada recentemente possa ser usada para acessar sua conta do Banco de Dados de Documentos.*

## <a id="consistency"></a>Gerenciar as configurações de consistência do Banco de Dados de Documentos
O Banco de Dados de Documentos dá suporte a quatro níveis de consistência de dados configuráveis pelo usuário bem definidos para permitir que os desenvolvedores façam compensações previsíveis entre consistência, disponibilidade e latência.

- A coerência **Forte** garante que as operações de leitura sempre devolvam o valor que foi gravado por último.

- A consistência **Bounded Staleness** garante que as leituras não estejam muito desatualizadas. Ela especificamente garante que as leituras não sejam mais do que versões *K* mais antigas do que a última versão gravada.

- A consistência **Session** garante leituras monotônicas (você nunca lê dados antigos, e novos e antigos novamente), gravações monotônicas (as gravações são ordenadas) e que você lê as gravações mais recentes dentro de qualquer ponto de vista do cliente único.

- A consistência **Eventual** garante que as operações de leitura sempre leiam um subconjunto de gravações válido e, eventualmente, convergirá.

*Observe que, por padrão, as contas do Banco de Dados de Documentos são provisionadas com consistência de nível Session. Para obter informações adicionais sobre configurações de consistência do Banco de Dados de Documentos, consulte a seção [Nível de Consistência](http://go.microsoft.com/fwlink/p/?LinkId=402365).*

### Para especificar a consistência padrão para uma conta do Banco de Dados de Documentos

1.      No [Portal do Azure](https://portal.azure.com/), acesse sua conta do Banco de Dados de Documentos. 

2.      Na lente **Configuração**, clique em **Consistência Padrão**.

3.      Na folha **Consistência Padrão**, selecione o nível de consistência padrão que você quer para sua conta do Banco de Dados de Documentos.

![Sessão de consistência padrão](./media/documentdb-manage-account/image005.png)

![Consistência limitada padrão](./media/documentdb-manage-account/image006.png)

4.      Clique em **Salvar**.

5.      O andamento da operação pode ser monitorado por meio do hub de Notificações do Portal do Azure.

*É importante lembrar que levará alguns minutos antes que uma alteração nas configurações padrão de consistência tenha efeito em toda a sua conta do Banco de Dados de Documentos.*

## <a id="delete"></a> Como: excluir uma conta do Banco de Dados de Documentos no Portal do Azure
Para remover uma conta do Banco de Dados de Documentos do Portal do Azure que não está mais sendo usada, use o comando **Delete** na folha **Conta do Banco de Dados de Documentos**.

![Como excluir uma conta do Banco de Dados de Documentos no Portal do Azure](./media/documentdb-manage-account/image009.png)

1.      No [Portal do Azure](https://portal.azure.com/), acesse a Conta do Banco de Dados de Documentos que deseja excluir. 

2.      Na folha **Conta do Banco de Dados de Documentos**, clique o comando **Excluir**.

3.      Na folha de confirmação de resultado, digite o nome da Conta do Banco de Dados de Documentos para confirmar que você quer excluir a conta.

4.      Clique no botão **Excluir** na folha de confirmação.

## <a id="next"></a>Próximas etapas

Saiba como [começar com sua conta do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Para saber mais sobre o Banco de Dados de Documentos, consulte a documentação do Banco de Dados de Documentos do Azure em [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).

 
 

<!---HONumber=AcomDC_1203_2015-->