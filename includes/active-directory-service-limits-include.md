---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/21/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 38f2dd301ddc2a5f8d28322856b2011bd2034c30
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554808"
---
Aqui estão as restrições de uso e outros limites de serviço para o serviço Microsoft Azure Active Directory (Azure AD).

| Categoria | Limites |
| --- | --- |
| Diretórios | Um único usuário pode pertencer a um máximo de 500 diretórios do Microsoft Azure Active Directory como um membro ou convidado.<br/>Um único usuário pode criar no máximo 20 diretórios. |
| Domínios | É possível adicionar no máximo 900 nomes de domínio gerenciados. Se você configurar todos os seus domínios para federação com Active Directory no local, você pode adicionar não mais de 450 nomes de domínio em cada diretório. |
| Objetos |<ul><li>Os usuários da edição Gratuita do Azure Active Directory podem criar no máximo 500.000 objetos em um único diretório.</li><li>Um usuário não administrador pode criar até 250 objetos. Objetos do Active Directory e os objetos excluídos que estão disponíveis para restauração contam para essa cota. Somente objetos excluídos que foram excluídos há menos de 30 dias estão disponíveis para restauração. Objetos excluídos que não estão mais disponíveis para restauração contam para essa cota em um valor de um quarto por 30 dias. Talvez [atribuir uma função de administrador](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) para usuários não administradores que estão propensos a repetidamente ultrapassar essa cota no decorrer de suas obrigações regulares.</li></ul> |
| Extensões de esquema |<ul><li>Extensões de tipo de cadeia de caracteres podem ter um máximo de 256 caracteres. </li><li>Extensões de tipo binário são limitadas a 256 bytes.</li><li>Apenas 100 valores de extensão, entre *todos os* tipos e *todos os* aplicativos, podem ser gravados em um único objeto.</li><li>Entidades de usuário, grupo, TenantDetail, dispositivo, Application e ServicePrincipal somente podem ser estendidas com atributos de valor único de tipo binário ou de tipo de cadeia de caracteres.</li><li>Extensões de esquema estão disponíveis somente na API do Graph versão 1.21 preview. O aplicativo precisa obter acesso de gravação para registrar uma extensão.</li></ul> |
| APLICATIVOS |Um máximo de 100 usuários podem ser proprietários de um único aplicativo. |
| Grupos |<ul><li>Um máximo de 100 usuários podem ser proprietários de um único grupo.</li><li>Qualquer número de objetos pode ser membro de um único grupo.</li><li>Um usuário pode ser um membro de qualquer número de grupos.</li><li>O número de membros em um grupo que podem ser sincronizados do Active Directory local ao Azure Active Directory usando o Azure AD Connect é limitado a 50.000 membros.</li></ul> |
| Painel de acesso |<ul><li>Não há nenhum limite para o número de aplicativos que podem ser vistos no painel de acesso por usuário. Isso se aplica a usuários com licenças para o Azure AD Premium ou Enterprise Mobility Suite.</li><li>Um máximo de 10 blocos de aplicativos pode ser visto no painel de acesso para cada usuário. Esse limite se aplica a usuários que possuem licenças atribuídos gratuitamente ou edições do Azure AD Basic do Active Directory do Azure. Exemplos de blocos de aplicativos incluem caixa, Salesforce ou Dropbox. Esse limite não se aplica às contas de administrador.</li></ul> |
| Relatórios | Um máximo de 1.000 linhas podem ser exibidas ou baixadas em qualquer relatório. Todos os dados adicionais serão truncados. |
| Unidades administrativas | Um objeto pode ser um membro de até 30 unidades administrativas. |
| Funções e permissões do administrador | <li>Um grupo não pode ser adicionado como um proprietário.<li>Um grupo não pode ser atribuído a uma função.<li>As permissões não podem ser alteradas, exceto para comutadores de locatário, que são as configurações do usuário no Azure AD de usuário por padrão. |
