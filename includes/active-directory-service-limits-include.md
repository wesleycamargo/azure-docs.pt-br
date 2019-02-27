---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/19/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 619dd7f3b01e2e7ce71e945fce77aa73cb87f264
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443295"
---
Aqui estão as restrições de uso e outros limites de serviço para o serviço Microsoft Azure Active Directory (Azure AD).

| Categoria | Limites |
| --- | --- |
| Diretórios | Um único usuário pode pertencer a um máximo de 500 diretórios do Microsoft Azure Active Directory como um membro ou convidado.<br/>Um único usuário pode criar no máximo 20 diretórios. |
| Domínios | É possível adicionar no máximo 900 nomes de domínio gerenciados. Se você estiver configurando todos os domínios para federação com o Active Directory local, poderá adicionar no máximo 450 nomes de domínio em cada diretório. |
| Objetos |<ul><li>Os usuários da edição Gratuita do Azure Active Directory podem criar no máximo 500.000 objetos em um único diretório.</li><li>Um usuário não administrador pode criar até 250 objetos.</li></ul> |
| Extensões de esquema |<ul><li>As extensões de tipo de cadeia de caracteres podem ter, no máximo, 256 caracteres. </li><li>Extensões de tipo binário são limitadas a 256 bytes.</li><li>É possível gravar 100 valores de extensão (entre todos os tipos e todos os aplicativos) em um único objeto.</li><li>Somente as entidades "User", "Group", "TenantDetail", "Device", "Application" e "ServicePrincipal" podem ser estendidas com atributos de valor único de tipo "cadeia de caracteres" ou "binário".</li><li>As extensões de esquema estão disponíveis apenas na versão 1.21-preview da API do Graph. O aplicativo precisa obter acesso de gravação para registrar uma extensão.</li></ul> |
| APLICATIVOS |Um máximo de 100 usuários podem ser proprietários de um único aplicativo. |
| Grupos |<ul><li>Um máximo de 100 usuários podem ser proprietários de um único grupo.</li><li>Qualquer número de objetos pode ser membro de um único grupo.</li><li>Um usuário pode ser um membro de qualquer número de grupos.</li><li>Você pode sincronizar no máximo 50 mil membros de um grupo de seu Active Directory local para o Azure Active Directory usando o Azure AD Connect.</li></ul> |
| Painel de acesso |<ul><li>Não há nenhum limite para o número de aplicativos que podem ser vistos por cada usuário final no Painel de acesso, para licenças atribuídas por usuários para o Azure AD Premium ou do Enterprise Mobility Suite.</li><li>No máximo 10 blocos de aplicativos (exemplos: Box, Salesforce ou Dropbox) podem ser vistos no Painel de Acesso para cada usuário final, para usuários com licenças para edições gratuitas ou básicas do Azure AD do Azure Active Directory. Esse limite não se aplica às contas de Administrador.</li></ul> |
| Relatórios | Um máximo de 1.000 linhas podem ser exibidas ou baixadas em qualquer relatório. Todos os dados adicionais serão truncados. |
| Unidades administrativas | Um objeto pode ser um membro de até 30 unidades administrativas. |
| Funções e permissões do administrador | <li>Um grupo não pode ser adicionado como proprietário<li>Um grupo não pode ser atribuído a uma função<li>Não é possível alterar as permissões de usuário padrão além das opções do locatário (configurações do usuário no Azure AD) |
