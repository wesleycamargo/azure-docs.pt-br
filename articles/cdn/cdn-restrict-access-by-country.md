---
title: Restringir o conteúdo da CDN do Azure por país | Microsoft Docs
description: Saiba como restringir o acesso por país ao conteúdo da CDN do Azure usando o recurso de filtragem geográfica.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: 248a51da76cdee06e55438a706c543c70dcf141e
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526174"
---
# <a name="restrict-azure-cdn-content-by-country"></a>Restringir conteúdo da CDN do Azure por país

## <a name="overview"></a>Visão geral
Quando um usuário solicita o conteúdo, por padrão, o conteúdo é exibido, independentemente da localização do usuário que fez a solicitação. No entanto, em alguns casos, é possível restringir o acesso ao conteúdo por país. Com o recurso *filtragem geográfica*, você pode criar regras em caminhos específicos no ponto de extremidade da CDN para permitir ou bloquear conteúdo em países selecionados.

> [!IMPORTANT]
> **A CDN Standard do Azure dos perfis da Microsoft** não oferecem suporte baseado no caminho da filtragem geográfica.
> 

## <a name="standard-profiles"></a>Perfis Standard
Os procedimentos nesta seção são apenas para perfis **CDN Standard do Azure da Akamai** e **CDN Standard do Azure da Verizon**. 

Para perfis**CDN Premium do Azure da Verizon**, você deve usar o portal **Gerenciar** para ativar a filtragem geográfica. Para obter mais informações, consulte os [perfis CDN Premium do Azure da Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definir o caminho do diretório
Para acessar o recurso de filtragem geográfica, selecione o ponto de extremidade da CDN no portal e, em seguida, selecione **Filtragem Geográfica** em CONFIGURAÇÕES no menu esquerdo. 

![Filtragem geográfica Standard](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Na caixa **PATH**, especifique o caminho relativo para o local ao qual os usuários terão permissão ou acesso negado. 

É possível aplicar filtragem geográfica a todos os arquivos com uma barra (/) ou selecionar pastas específicas, especificando caminhos de diretórios (por exemplo, */imagens/*). Adicionalmente, é possível aplicar filtragem geográfica a um único arquivo (por exemplo */imagens/city.png*). Várias regras são permitidas e, após inserir uma regra, aparecerá uma linha em branco para que você insira a próxima regra.

Por exemplo, todos os filtros de caminho de diretório a seguir são válidos:   
*/*                                 
*/Fotos/*     
*/Fotos/Estrasburgo/*     
*/Fotos/Estrasburgo/city.png*

### <a name="define-the-type-of-action"></a>Definir o tipo de ação

Na lista **AÇÃO**, selecione **Permitir** ou **Bloquear**: 

- **Permitir**: Apenas os usuários de países especificados terão o acesso aos ativos solicitados o caminho recursivo.

- **Bloco**: Os usuários dos países especificados terão acesso negados aos ativos solicitados o caminho recursivo. Se nenhuma outra opção de filtragem de país/região tiver sido configurada para esse local, então, todos os outros usuários terão acesso permitido.

Por exemplo, uma regra de filtragem geográfica para bloquear o caminho */Fotos/Estrasburgo/* filtra os arquivos a seguir:     
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countries"></a>Definir os países
Na lista **CÓDIGOS DE PAÍS/REGIÃO**, selecione os países que você quer bloquear ou permitir para o caminho. 

Após concluir a seleção dos países, selecione **Salvar** para ativar a nova regra de filtragem geográfica. 

![Regras de filtragem geográfica](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Limpar recursos
Para excluir uma regra, selecione-a na lista da página **Filtragem Geográfica** e, em seguida escolha **Excluir**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Perfis CDN Premium do Azure da Verizon
Para perfis**CDN Premium do Azure da Verizon**, a interface do usuário para criar uma regra de filtragem geográfica é diferente:

1. No menu superior do perfil da CDN do Azure, selecione **Gerenciar**.

2. No portal da Verizon, selecione **HTTP Grande** e, em seguida, selecione **Filtragem por País/Região**.

    ![Filtragem geográfica Standard](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Selecione **Adicionar Filtro por País/Região**.

    A página **Etapa Um:** aparece.

4. Insira o caminho do diretório, selecione **Bloquear** ou **Adicionar** e, em seguida, selecione **Avançar**.

    A página **Etapa Dois:** aparece. 

5. Selecione um ou mais países da lista e, em seguida, selecione **Concluir** para ativar a regra. 
    
    A nova regra aparece na tabela na página **Filtragem por País/Região**.

    ![Regras de filtragem geográfica](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Limpar recursos
Na tabela de regras de filtragem por país, selecione o ícone de exclusão ao lado de uma regra para excluí-la ou o ícone de edição para modificá-la.

## <a name="considerations"></a>Considerações
* As alterações na configuração de filtragem geográfica não são efetivadas imediatamente:
   * Para perfis da **CDN Standard do Azure da Microsoft**, a propagação geralmente é concluída em dez minutos. 
   * Para perfis da **CDN Standard do Azure da Akamai**, a propagação normalmente é concluída em um minuto. 
   * Para perfis da **CDN Standard do Azure da Verizon** e da **CDN Premium do Azure da Verizon**, a propagação geralmente é concluída em 10 minutos. 
 
* Esse recurso não dá suporte a caracteres curinga (por exemplo, *).

* A configuração de filtragem geográfica associada ao caminho relativo é aplicada recursivamente a esse caminho.

* Apenas uma regra pode ser aplicada para o mesmo caminho relativo. Ou seja, você não pode criar vários filtros de país que apontam para o mesmo caminho relativo. No entanto, como os filtros por país são recursivos, uma pasta poderá ter vários filtros por país. Em outras palavras, uma subpasta de uma pasta configurada anteriormente pode ter um filtro de país diferente atribuído.

* O recurso de filtragem geográfica usa códigos do país para definir os países dos quais uma solicitação será permitida ou bloqueada para um diretório seguro. Embora os perfis da Akamai e da Verizon deem suporte para a maioria dos códigos do país, existem algumas diferenças. Para obter mais informações, consulte [Códigos do país da CDN do Azure](/previous-versions/azure/mt761717(v=azure.100)). 

