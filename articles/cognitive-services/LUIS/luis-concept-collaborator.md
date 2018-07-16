---
title: Noções básicas sobre a colaboração do aplicativo LUIS – Azure | Microsoft Docs
description: Os aplicativos LUIS requerem um proprietário único e colaboradores opcionais.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 51b3958f83cd110ace27f6ee42571c05843f5aa2
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265058"
---
# <a name="collaborating"></a>Colaborando

O LUIS oferece colaboração para permitir que um grupo de pessoas crie um aplicativo.

## <a name="luis-account"></a>Conta LUIS
Uma conta LUIS está associada uma única conta do [Microsoft Live](https://login.live.com/). Cada conta LUIS recebe uma [chave de criação](luis-concept-keys.md#authoring-key) gratuita para ser usada para criar todos os aplicativos LUIS aos quais a conta tem acesso. 

Uma conta LUIS pode ter muitos aplicativos LUIS.

## <a name="luis-app-owner"></a>Proprietário do aplicativo LUIS
A conta que cria um aplicativo é o proprietário. Cada aplicativo tem um único proprietário. O proprietário está listado nas **[Configurações](luis-how-to-collaborate.md)** do aplicativo. Esta é a conta que pode excluir o aplicativo. Esta também é a conta que recebe o email quando a cota do ponto de extremidade atinge 75% do limite mensal. 

## <a name="transfer-ownership"></a>Transferir propriedade
O LUIS não oferece transferência de propriedade; contudo, qualquer colaborador pode exportar o aplicativo e criar um aplicativo importando-o. Lembre-se de que o novo aplicativo tem uma ID do aplicativo diferente. O novo aplicativo precisa ser treinado, publicado e o novo ponto de extremidade, usado.

## <a name="luis-app-collaborators"></a>Colaboradores do aplicativo LUIS
Um proprietário de aplicativo pode adicionar colaboradores a um aplicativo. O proprietário precisa adicionar o endereço de email do colaborador nas **[Configurações](luis-how-to-collaborate.md)** do aplicativo. O colaborador tem acesso completo ao aplicativo. Se o colaborador excluir o aplicativo, o aplicativo será removido da conta do colaborador, mas continuará na conta do proprietário. 

Se desejar compartilhar vários aplicativos com os colaboradores, cada aplicativo precisará do email do colaborador adicionado. 

## <a name="managing-multiple-authors"></a>Gerenciando vários autores
No momento, o site do [LUIS][LUIS] não oferece criação no nível da transação. É possível permitir que os autores trabalhem em versões independentes de uma versão base. Dois diferentes métodos são descritos nas seções a seguir.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Gerenciar várias versões dentro do mesmo aplicativo
Comece [clonando](luis-how-to-manage-versions.md#clone-a-version), de uma versão base, para cada autor. 

Cada autor faz alterações em sua própria versão do aplicativo. Depois que cada autor estiver satisfeito com o modelo, exporte as novas versões para os arquivos JSON.  

Aplicativos exportados são arquivos formatados para JSON, que podem ser comparados quanto a alterações. Combine os arquivos para criar um único arquivo JSON da nova versão. Altere a propriedade **versionId** no JSON para significar a nova versão mesclada. Importe essa versão para o aplicativo original. 

Esse método permite que você tenha uma versão ativa, uma versão do estágio e uma versão publicada. É possível comparar os resultados no painel de teste interativo entre as três versões.

### <a name="manage-multiple-versions-as-apps"></a>Gerenciar várias versões como aplicativos
[Exporte](luis-how-to-manage-versions.md#export-version) a versão base. Cada autor importa a versão. A pessoa que importa o aplicativo é o proprietário da versão. Quando ela terminar de modificar o aplicativo, exporte a versão. 

Aplicativos exportados são arquivos formatados para JSON, que podem ser comparados com a exportação base quanto a alterações. Combine os arquivos para criar um único arquivo JSON da nova versão. Altere a propriedade **versionId** no JSON para significar a nova versão mesclada. Importe essa versão para o aplicativo original.

## <a name="next-steps"></a>Próximas etapas

Entender os conceitos de [controle de versão](luis-concept-version.md). 

Confira [Configurações do aplicativo](luis-how-to-collaborate.md) para saber como gerenciar colaboradores em seu aplicativo LUIS.

Confira [Add email to access list](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) (Adicionar email à lista de acesso) com as APIs de criação.

[luis-reference-prebuilt-domains]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-domains
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website