---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/22/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 64751e0fcbf9a2255964d0de673e2cc2020ceb9a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50254739"
---
Os [Arquivos do Azure](../articles/storage/files/storage-files-introduction.md) dão suporte à autenticação baseada em identidade via protocolo SMB (versão prévia) por meio do [Azure AD (Azure Active Directory) Domain Services](../articles/active-directory-domain-services/active-directory-ds-overview.md). As VMs (máquinas virtuais) do Windows ingressadas no domínio podem acessar os compartilhamentos de arquivos do Azure usando as credenciais do [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md). 

O Azure AD autentica uma identidade, como um usuário, grupo ou entidade de serviço com [RBAC (controle de acesso baseado em função)](../articles/role-based-access-control/overview.md). É possível definir funções personalizadas do RBAC que abrangem conjuntos comuns de permissões usadas para acessar os arquivos do Azure. Ao atribuir a função RBAC personalizada a uma identidade do Azure AD, essa identidade recebe acesso a um compartilhamento de arquivos do Azure de acordo com essas permissões.

Como parte da versão prévia, os Arquivos do Azure também dão suporte à preservação, herança e imposição de [DACLs do NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) em todos os arquivos e diretórios em um compartilhamento de arquivos. Se você copiar dados de um compartilhamento de arquivos para os Arquivos do Azure, ou vice-versa, poderá especificar que as DACLs do NTFS sejam mantidas. Dessa forma, é possível implementar cenários de backup usando os Arquivos do Azure, preservando as DACLs do NTFS entre o compartilhamento de arquivos local e o compartilhamento de arquivos na nuvem. 

> [!NOTE]
> - Não há suporte para autenticação do Azure AD via SMB em VMs do Linux para a versão prévia. Há suporte apenas para VMs do Windows Server.
> - A autenticação do AD do Azure em SMB não é suportada em máquinas locais que acessam os arquivos do Azure.
> - A autenticação do Azure AD está disponível somente para contas de armazenamento criadas após 24 de setembro de 2018.
> - Não há suporte para a autenticação do Azure AD por SMB e também não há suporte para ACL do NTFS persistente em compartilhamentos de arquivos do Azure gerenciados pelo Serviço de Sincronização de Arquivos do Azure. 
