---
title: Restringir o acesso ao seu conteúdo da CDN do Azure por país | Microsoft Docs
description: Aprenda a restringir o acesso ao seu conteúdo da CDN do Azure usando o recurso Filtragem por País.
services: cdn
documentationcenter: ''
author: camsoper
manager: erikre
editor: ''

ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: casoper

---
# Restringir o acesso ao seu conteúdo por país
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Quando um usuário solicita o conteúdo, por padrão, o conteúdo é apresentado, independentemente de onde o usuário fez essa solicitação. Em alguns casos, você talvez queira restringir o acesso ao seu conteúdo por país. Este tópico explica como usar o recurso **Filtragem de País** para configurar o serviço para permitir ou bloquear o acesso por país.

> [!NOTE]
> Depois que a configuração estiver configurada, ela será aplicada a todos os pontos de extremidade da **CDN do Azure da Verizon** neste perfil de CDN do Azure.
> 
> 

Para obter informações sobre as considerações que se aplicam à configuração desse tipo de restrições, consulte a seção [Considerações](cdn-restrict-access-by-country.md#considerations) no final do tópico.

![Filtragem de país](./media/cdn-filtering/cdn-country-filtering.png)

## Etapa 1: Definir o caminho de diretório
Ao configurar um filtro de país, você deve especificar o caminho relativo para o local o acesso dos usuários será permitido ou negado. Você pode aplicar a filtragem de país para todos os arquivos com "/" ou pastas selecionadas, especificando os caminhos de diretório.

Filtro de caminho do diretório de exemplo:

    /                                 
    /Photos/
    /Photos/Strasbourg

## Etapa 2: Definir a ação: bloquear ou permitir
**Bloqueio**: os usuários dos países especificados terão o acesso negado a ativos solicitados nesse caminho recursivo. Se nenhuma outra opção de filtragem de país tiver sido configurada para esse local, então, todos os outros usuários terão acesso permitido.

**Permissão**: somente os usuários dos países especificados terão o acesso permitido aos ativos solicitados desse caminho recursivo.

## Etapa 3: Definir os países
Selecione os países que você deseja bloquear ou permitir para o caminho. Para obter mais informações, confira [CDN do Azure a partir dos Códigos do País Verizon](https://msdn.microsoft.com/library/mt761717.aspx).

Por exemplo, a regra de bloqueio /Photos/Strasbourg/ filtrará arquivos, incluindo:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


## Códigos de país
O recurso **Filtragem de País** usa códigos de país para definir os países nos quais uma solicitação será permitida ou bloqueada para um diretório protegido. Você encontrará os códigos do país na [CDN do Azure a partir dos Códigos do País Verizon](https://msdn.microsoft.com/library/mt761717.aspx). Se você especificar "UE" (Europa) ou "AP" (Ásia/Pacífico), um subconjunto de endereços IP originados de qualquer país dessas regiões será bloqueado ou permitido.

## <a id="considerations"></a>Considerações
* Pode levar até 90 minutos para que as alterações em sua configuração de filtragem do país entrem em vigor.
* Esse recurso não oferece suporte a caracteres curinga (por exemplo, ‘*’).
* A configuração de filtragem de país associada com o caminho relativo será aplicada recursivamente para esse caminho.
* Apenas uma regra pode ser aplicada no mesmo caminho relativo (você não pode criar vários filtros de país que apontam para o mesmo caminho relativo). No entanto, uma pasta pode ter vários filtros de país. Isso é devido à natureza recursiva de filtros de país. Em outras palavras, uma subpasta de uma pasta configurada anteriormente pode ter um filtro de país diferente atribuído.

<!---HONumber=AcomDC_0803_2016-->