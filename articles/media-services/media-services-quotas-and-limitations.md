<properties 
	pageTitle="Cotas e limitações dos Serviços de Mídia" 
	description="Este tópico descreve as cotas e limitações associadas aos serviços de mídia do Microsoft Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="juliako"/>


#Cotas e limitações

Este tópico descreve as cotas e limitações associadas aos serviços de mídia do Microsoft Azure.

## Cotas e limitações

- O número máximo de ativos permitidos na conta dos Serviços de Mídia: 1.000.000. 

- O número máximo de tarefas encadeadas por trabalho: 30.

- O número máximo de ativos permitidos em uma tarefa: 50 ativos por tarefa.
 
- O número máximo de ativos por trabalho: 100.
 
- O número máximo de trabalhos em sua conta não deve exceder 50.000. Esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos.
 
- Você pode excluir os trabalhos antigos usando solicitação HTTP de **IJob.Delete** ou **DELETE**. Para obter mais informações, consulte [Limite de registro de trabalho para o Codificador de Mídia do Azure](http://blogs.msdn.com/b/randomnumber/archive/2014/05/05/job-record-limit-for-windows-azure-media-encoder.aspx) e [Gerenciando ativos](https://msdn.microsoft.com/library/azure/dn642436.aspx).
 
- Ao fazer uma solicitação para listar as entidades de trabalho, será retornado um máximo de 1.000 por solicitação. Se você precisar manter o controle dos de todos os trabalhos enviados, você pode usar superior/pular conforme descrito em [Opções de consulta OData sistema](http://msdn.microsoft.com/library/gg309461.aspx).


- Você não pode ter mais do que cinco localizadores exclusivos associados a um determinado ativo ao mesmo tempo.
	
	**Observação** os localizadores não foram desenvolvidos para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM).

- Você não pode ter mais de 25 contas dos serviços de mídia em uma única assinatura.


- Por padrão, você pode adicionar até 5 canais ao vivo para sua conta de serviços de mídia.

	Você também pode iniciar até 5 canais ao mesmo tempo. Para solicitar um limite superior, consulte *Como solicitar um limite superior para cotas atualizáveis*, a seguir.

- Por padrão, você pode adicionar até 50 programas (no estado parado) para um único canal.

	Você só pode ter até três programas em estado de execução ao mesmo tempo. Para solicitar um limite superior, consulte *Como solicitar um limite superior para cotas atualizáveis*, a seguir.

- Por padrão, todas as contas de serviços de mídia podem ter até 2 pontos de extremidade de streaming (origens).

	Você também pode ter até 2 pontos de extremidade de streaming em estado de execução ao mesmo tempo.

	Você pode expandir cada ponto de extremidade de streaming para até 10 unidades de streaming. Para solicitar um limite superior, consulte *Como solicitar um limite superior para cotas atualizáveis*, a seguir.


- Por padrão, todas as contas de serviços de mídia podem ser dimensionadas para até 25 unidades de codificação. Para obter mais informações, consulte Como dimensionar os serviços de mídia. Para solicitar um limite superior, consulte Como solicitar um limite superior para cotas atualizáveis.
	
	**Importante** não crie mais contas de serviços de mídia para aumentar os limites, em vez disso, envie um tíquete de suporte.


- O mecanismo de aceleração dos Serviços de Mídia restringe o uso dos recursos para aplicativos que fazem solicitações excessivas ao serviço. O serviço pode retornar o código de status HTTP Serviço Não Disponível (503). Para obter mais informações, consulte a descrição do erro 503 no tópico [Códigos de erro dos serviços de mídia do Azure](http://msdn.microsoft.com/library/azure/dn168949.aspx).

##<a id="request_higher_limit"></a>Como solicitar um limite superior para cotas atualizáveis

###Cotas atualizáveis

Você pode solicitar para atualizar os limites para as seguintes cotas abrindo um tíquete de suporte - unidades de codificação

- Canais ao vivo (no estado parado e em execução)
 
- Streaming de pontos de extremidade (no estado parado e em execução)
 
- Unidades de streaming

###Abra um tíquete de suporte

Para abrir um tíquete de suporte, faça o seguinte:

1. Clique em [Obter suporte](https://manage.windowsazure.com/?getsupport=true). Se você não estiver conectado, você será solicitado a inserir suas credenciais.

1. Selecione sua assinatura.
 
1. Em tipo de suporte, selecione "Técnico".
 
1. Clique em "Criar Tíquete".
 
1. Selecione "Serviços de Mídia do Azure" na lista de produtos apresentados na próxima página.
 
1. Selecione um "tipo de problema" que é apropriado para o seu problema.
 
1. Clique em Continuar.
 
1. Siga as instruções na próxima página e, em seguida, insira os detalhes sobre o problema.
 
1. Clique em Enviar para abrir o tíquete.
  

<!---HONumber=58_postMigration-->