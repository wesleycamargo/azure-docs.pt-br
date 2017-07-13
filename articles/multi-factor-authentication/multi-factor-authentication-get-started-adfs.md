---
title: "Verificação em duas etapas e AD FS - Azure MFA | Microsoft Docs"
description: "Esta é a página do Azure Multi-Factor Authentication que descreve como começar a usar o Azure MFA e o AD FS."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 44fbba68-6cf9-46c1-a9df-736580b68ae3
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/23/2017
ms.author: kgremban
ms.translationtype: HT
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 28aede545c738137ff04257214e4a3f42792d85c
ms.contentlocale: pt-br
ms.lasthandoff: 07/12/2017

---
# Introdução ao Azure Multi-Factor Authentication e aos Serviços de Federação do Active Directory
<a id="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services" class="xliff"></a>
<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Caso organização tenha federado seu Active Directory local com o Azure Active Directory usando o AD FS, existem duas opções para usar a Autenticação Multifator do Azure.

* Proteger os recursos de nuvem usando o Azure Multi-Factor Authentication ou os Serviços de Federação do Active Directory
* Proteger os recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication

A tabela a seguir resume a experiência de verificação entre a proteção dos recursos com a Autenticação Multifator do Azure e o AD FS

| Experiência de verificação – Aplicativos baseados em navegador | Experiência de verificação – Aplicativos não baseados em navegador |
|:--- |:--- |:--- |
| Protegendo os recursos do AD do Azure usando o Azure Multi-Factor Authentication |<li>A primeira etapa de verificação é executada localmente usando o AD FS.</li> <li>A segunda etapa é um método baseado em telefone executado com a autenticação na nuvem.</li> |
| Protegendo recursos do AD do Azure usando os Serviços de Federação do Active Directory |<li>A primeira etapa de verificação é executada localmente usando o AD FS.</li><li>A segunda etapa é realizada localmente, cumprindo a declaração.</li> |

Advertências sobre senhas de aplicativo para usuários federados:

* Senhas de aplicativo são verificadas usando a autenticação na nuvem e, portanto, ignora as federações. A federação só é usada ativamente ao configurar a senha de aplicativo.
* As configurações do Controle de Acesso do Cliente local não são consideradas pela Senha de aplicativo.
* Você perde a capacidade de registro de autenticação local para senha de aplicativo.
* A desabilitação/exclusão da conta pode levar até três horas para a sincronização do diretório, atrasando a desabilitação/exclusão da senha de aplicativo na identidade da nuvem.

## Próximas etapas
<a id="next-steps" class="xliff"></a>
Para obter informações sobre como configurar a Autenticação Multifator do Azure ou o Servidor de Autenticação Multifator do Azure com o AD FS, consulte os artigos:

* [Proteger os recursos de nuvem usando o Azure Multi-Factor Authentication e o AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
* [Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com o AD FS do Windows Server 2012 R2](multi-factor-authentication-get-started-adfs-w2k12.md)
* [Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)

