<properties 
	pageTitle="Como restringir o acesso ao seu conteúdo por país" 
	description="Quando um usuário solicita o conteúdo, por padrão, o conteúdo é apresentado, independentemente de onde o usuário fez essa solicitação. Em alguns casos, você talvez queira restringir o acesso ao seu conteúdo por país. Este tópico explica como configurar o serviço para permitir ou bloquear o acesso por país." 
	services="cdn" 
	documentationCenter=".net" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="mazha"/>

#Como restringir o acesso ao seu conteúdo por país
 
 
Quando um usuário solicita o conteúdo, por padrão, o conteúdo é apresentado, independentemente de onde o usuário fez essa solicitação. Em alguns casos, você talvez queira restringir o acesso ao seu conteúdo por país. Este tópico explica como configurar o serviço para permitir ou bloquear o acesso por país.

>[AZURE.NOTE]Depois que a configuração estiver configurada, ela será aplicada a todos os pontos de extremidade CDN em sua assinatura.

##Etapa 1: Definir o caminho de diretório 

Ao configurar um filtro de país, você deve especificar o caminho relativo para o local o acesso dos usuários será permitido ou negado. Você pode aplicar a filtragem de país para todos os arquivos com "/" ou pastas selecionadas, especificando os caminhos de diretório.

Filtro de caminho do diretório de exemplo:
		
	/                                 
	/Photos/
	/Photos/Strasbourg

##Etapa 2: Definir a ação: bloquear ou permitir

**Bloqueio**: os usuários de países especificados terão o acesso negado a ativos solicitados nesse caminho recursivo. Se nenhuma outra opção de filtragem de país tiver sido configurada para esse local, então, todos os outros usuários terão acesso permitido.

**Permissão**: somente os usuários de países especificados terão o acesso permitido aos ativos solicitados do caminho recursivo.
	 
##Etapa 3: Definir os países 

Selecione os países que você deseja bloquear ou permitir para o caminho.

Exemplo:

A regra de bloqueio **/Photos/Strasbourg/** filtrará arquivos incluindo:

	http://az123456.vo.msecnd.net/Photos/Strasbourg/1000.jpg. 
	http://az123456.vo.msecnd.net/Photos/Strasbourg/Cathedral/1000.jpg. 

##Códigos de país

O recurso de **Filtragem de país** usa códigos de país para definir os países nos quais uma solicitação será permitida ou bloqueada para um diretório protegido. Atualmente, os dois códigos de país a seguir têm suporte: "EU" (Europa) e "AP" (Ásia/Pacífico). No entanto, esses códigos de país não podem ser usados para permitir ou bloquear todas as solicitações dessas regiões. Em vez disso, esses códigos de país são aplicados às solicitações originadas de endereços IP que são distribuídos ao longo de uma região, em vez de um país. Para bloquear ou permitir o acesso em países da Europa e Ásia/Pacífico, selecione os países e a região correspondente: EU e AP.


##Considerações
 
- Pode levar até uma hora para que as alterações em sua configuração de filtragem de país entrem em vigor.
- Esse recurso não oferece suporte a caracteres curinga (por exemplo, *). - A configuração de filtragem de país associada com o caminho relativo será aplicada recursivamente para esse caminho.
- Apenas uma regra pode ser aplicada no mesmo caminho relativo (você não pode criar vários filtros de país que apontam para o mesmo caminho relativo). No entanto, uma pasta pode ter vários filtros de país. Isso é devido à natureza recursiva de filtros de país. Em outras palavras, uma subpasta de uma pasta configurada anteriormente pode ter um filtro de país diferente atribuído.

<!---HONumber=July15_HO3-->