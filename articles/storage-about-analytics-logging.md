<properties 
	pageTitle="Sobre o registro em log da análise de armazenamento" 
	description="Saiba como usar o log da análise de armazenamento, solicitações autenticadas e como os logs são armazenados " 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Sobre o registro em log da análise de armazenamento

## Visão geral
A análise de armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço.

Para usar a análise de armazenamento, você deve habilitá-la separadamente para cada serviço que você deseja monitorar. Você pode habilitá-la no [Portal de gerenciamento do Azure](https://manage.windowsazure.com/). Para obter detalhes, consulte [Como monitorar uma conta de armazenamento](../how-to-monitor-a-storage-account). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as operações Obter propriedades do serviço Blob, Obter propriedades do serviço de fila e obter propriedades do serviço de tabela para habilitar a análise de armazenamento para cada serviço.

As entradas de log são criadas somente se houver atividade de serviço de armazenamento. Por exemplo, se uma conta de armazenamento tiver atividade em seu serviço Blob, mas não em seus serviços de fila ou tabela, somente os logs referentes ao serviço Blob serão criados.

## Solicitações de registro em log autenticadas
Os seguintes tipos de solicitações autenticadas são registrados:

- Solicitações bem sucedidas

- Solicitações com falha, incluindo o tempo limite, limitação, rede, autorização e outros erros

- Solicitações que usam uma assinatura SAS (acesso compartilhado), incluindo solicitações bem-sucedidas e com falha

- Solicitações para dados de análise

As solicitações feitas pela própria análise de armazenamento, como criação de log ou exclusão, não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Operações registradas de análise de armazenamento e mensagens de Status](https://msdn.microsoft.com/library/hh343260.aspx) e [Formato de Log de análise de armazenamento](https://msdn.microsoft.com/library/hh343259.aspx).

## Registro em log de solicitações anônimas
Os seguintes tipos de solicitações anônimas são registrados:

- Solicitações bem sucedidas

- Erros do servidor

- Erros de tempo limite para o cliente e servidor

- Solicitações GET com falha com o código de erro 304 (não modificado)

Todas as outras solicitações anônimas com falha não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Operações registradas de análise de armazenamento e mensagens de Status](https://msdn.microsoft.com/library/hh343260.aspx) e [Formato de Log de análise de armazenamento](](https://msdn.microsoft.com/library/hh343259.aspx)).

## Como os logs são armazenados
Todos os logs são armazenados em blobs de bloco em um contêiner denominado $logs, que é criado automaticamente quando a análise de armazenamento é habilitada para uma conta de armazenamento. O contêiner $logs está localizado no namespace de blob da conta de armazenamento, por exemplo: `http://<accountname>.blob.core.windows.net/$logs`. Este contêiner não pode ser excluído quando a análise de armazenamento tiver sido habilitada, embora seu conteúdo possa ser excluído.

>[Azure.NOTE] O contêiner de $logs não é exibido quando uma operação de listagem do contêiner é executada, como o método [ListContainers](https://msdn.microsoft.com/library/ee758348.aspx). Ele deve ser acessado diretamente. Por exemplo, você pode usar o método [ListBlobs](https://msdn.microsoft.com/library/ee772878.aspx) para acessar os blobs no contêiner `$logs`.
Como as solicitações são registradas, a análise de armazenamento carrega resultados intermediários como blocos. Periodicamente, a análise de armazenamento confirmará esses blocos e os disponibilizará como um blob.

Podem existir registros duplicados para os logs criados na mesma hora. Você pode determinar se um registro é uma duplicata verificando o **RequestId** e o número da **operação**.

## Convenções de nomenclatura de log
Cada log será gravado no seguinte formato:

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log 

A tabela a seguir descreve cada atributo no nome do log:

| Atributo      	| Descrição                                                                                                                                                                                	|
|----------------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| <service-name> 	| O nome do serviço de armazenamento. Por exemplo: blob, tabela ou fila                                                                                                                        	|
| AAAA           	| O ano de quatro dígitos para o log. Por exemplo: 2011                                                                                                                                         	|
| MM             	| O mês de dois dígitos para o log. Por exemplo: 07                                                                                                                                           	|
| DD             	| O mês de dois dígitos para o log. Por exemplo: 07                                                                                                                                           	|
| hh             	| A hora de dois dígitos que indica a hora inicial para os logs, no formato de 24 horas UTC. Por exemplo: 18                                                                                   	|
| mm             	| O número de dois dígitos que indica o minuto inicial dos logs. >[AZURE.NOTE]Esse valor não tem suporte na versão atual da análise de armazenamento e seu valor será sempre 00. 	|
| <counter>      	| Um contador baseado em zero com seis dígitos que indica o número de blobs de log gerado para o serviço de armazenamento em um período de uma hora. Esse contador começa em 000000. Por exemplo: 000001   	|

Este é um nome de log de exemplo completo que combina os exemplos anteriores:

    blob/2011/07/31/1800/000001.log

Este é um exemplo de URI que pode ser usado para acessar o log anterior:

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log 

Quando uma solicitação de armazenamento estiver conectada, o nome do log resultante se correlaciona com a hora quando concluir a operação solicitada. Por exemplo, se uma solicitação GetBlob foi concluída às 6:30 PM em 31/7/2011, o log seria gravado com o seguinte prefixo: `blob/2011/07/31/1800/`

## Metadados de log
Todos os blobs de log são armazenados com metadados que podem ser usados para identificar os dados de log que contém o blob. A tabela a seguir descreve cada atributo de metadados:

| Atributo  	| Descrição                                                                                                                                                                                                                                               	|
|------------	|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| LogType    	| Descreve se o log contém informações referentes a operações de ler, gravar ou de exclusão. Esse valor pode incluir um tipo ou uma combinação dos três, separados por vírgulas.   Exemplo 1: gravar. Exemplo 2: ler, gravar. Exemplo 3: ler, gravar e excluir 	|
| StartTime  	| A hora mais antiga de uma entrada no log, no formato AAAA-MM-DDThh:mm:ssZ. Por exemplo: 2011-07-31T18:21:46Z                                                                                                                                          	|
| EndTime    	| A hora mais recente de uma entrada no log, no formato YYYY-MM-DDThh:mm:ssZ. Por exemplo: 2011-07-31T18:22:09Z                                                                                                                                            	|
| LogVersion 	| A versão do formato do log. Atualmente, o único valor aceito é: 1,0                                                                                                                                                                                 	|

A lista a seguir exibe exemplos completos de metadados que usam os exemplos anteriores:

- LogType=write 

- StartTime=2011-07-31T18:21:46Z 

- EndTime=2011-07-31T18:22:09Z 

- LogVersion=1.0 

## Acessando dados de log

Todos os dados no contêiner `$logs` podem ser acessados usando as APIs do serviço Blob, incluindo as APIs do .NET fornecidas pela biblioteca gerenciada do Azure. O administrador da conta de armazenamento pode ler e excluir logs, mas não é possível criá-los ou atualizá-los. Os metadados do log e o nome do log podem ser usados ao consultar um log. É possível que os logs de uma determinada hora sejam exibidos fora de ordem, mas os metadados sempre especificam o período das entradas de log em um log. Portanto, você pode usar uma combinação de nomes de log e metadados ao procurar um log específico.

## Próximas etapas

[Formato de Log de análise de armazenamento](https://msdn.microsoft.com/library/hh343259.aspx) 

[Operações registradas de análise de armazenamento e mensagens de Status](https://msdn.microsoft.com/library/hh343260.aspx)

[Sobre as métricas de análise de armazenamento](https://msdn.microsoft.com/library/hh343258.aspx) 
<!--HONumber=47-->
