---
title: "Azure Active Directory Domain Services: solução de problemas de configuração da entidade de LDAP Seguro | Microsoft Docs"
description: "Solução de problemas de LDAP Seguro para o Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: ergreenl
ms.openlocfilehash: ad98f3fb1ddb753976be627764d34864e5bf3d50
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services: Solução de problemas de configuração do LDAP Seguro

Este artigo fornece soluções para problemas comuns ao [configurar o LDAP Seguro](active-directory-ds-admin-guide-configure-secure-ldap.md) para o Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Configuração do Grupo de Segurança de Rede de LDAP Seguro

**Mensagem de alerta:**

*LDAP Seguro pela internet está habilitado para o domínio gerenciado. No entanto, o acesso à porta 636 não é protegido usando um grupo de segurança de rede. Isso pode expor as contas de usuário no domínio gerenciado a ataques de força bruta da senha.*

### <a name="secure-ldap-port"></a>Porta do LDAP Seguro

Quando o LDAP Seguro estiver habilitado, recomendamos a criação de regras adicionais para permitir acesso de LDAPS apenas de determinados endereços IP. Essas regras protegem seu domínio contra ataques de força bruta que podem causar uma ameaça à segurança. A porta 636 permite o acesso aos seus domínios gerenciados. Veja como atualizar o NSG para permitir acesso ao LDAP Seguro:

1. Navegue até a guia [Grupos de Segurança de Rede](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) no portal do Azure
2. Escolha o NSG associado ao seu domínio na tabela.
3. Clique em **Regras de segurança de entrada**
4. Criar a regra da porta 636
   1. Clique em **Adicionar** na barra de navegação superior.
   2. Escolha **Endereços IP** para a origem.
   3. Especifique os intervalos de porta de origem para essa regra.
   4. Entrada "636" para os intervalos de porta de destino.
   5. O protocolo é **TCP**.
   6. Dê um nome apropriado, descrição e prioridade para a regra. A prioridade dessa regra deve ser maior do que a prioridade da regra "Negar todos", se você tiver uma.
   7. Clique em **OK**.
5. Verifique se a regra foi criada.
6. Verifique a integridade de seu domínio em duas horas para garantir que você tenha concluído as etapas corretamente.


## <a name="contact-us"></a>Fale conosco
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](active-directory-ds-contact-us.md).
