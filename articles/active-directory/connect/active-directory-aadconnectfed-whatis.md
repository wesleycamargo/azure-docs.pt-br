---
title: "Azure AD Connect e Federação | Microsoft Docs"
description: "Esta página é o local central para toda a documentação relativa às operações do AD FS usando o Azure AD Connect"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: fd3699e4ce423ae9490d5bf788729c43eaa623eb
ms.openlocfilehash: 19ebfae5de8576402348e3163f99a54b9308a078
ms.lasthandoff: 01/13/2017


---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect e federação
O Azure AD Connect permite configurar a federação com o AD FS e o Azure AD locais. Com o logon federado, você pode habilitar os usuários a acessarem serviços baseados no Azure AD com suas senhas locais e, enquanto estão na rede corporativa, sem precisar digitar suas senhas novamente. A opção de federação com o AD FS permite que você implante um novo AD FS ou especifique um existente em um farm do Windows Server 2012 R2.

Este tópico é a base das informações sobre funcionalidades relacionadas à Federação para o Azure AD Connect e relaciona links para todos os outros tópicos relacionados a ela. Para obter links para o Azure AD Connect, confira Integrando suas identidades locais ao Azure Active Directory.

## <a name="azure-ad-connect---federation-topics"></a>Azure AD Connect – Tópicos sobre federação
| Tópico | O que ele abrange e quando deve ser lido |
|:--- |:--- |
| **Opções de entrada de usuário do Azure AD Connect** | |
| [Noções básicas sobre as opções de entrada do usuário](active-directory-aadconnect-user-signin.md) |Descrição das diferentes opções de entrada do usuário e como elas afetam a experiência de entrada do usuário no Azure |
| **Instalação do AD FS usando o Azure AD Connect** | |
| [Pré-requisitos](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Pré-requisitos para uma instalação bem-sucedida do AD FS usando o Azure AD Connect |
| [Configurar o farm do AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Como instalar um novo farm do AD FS usando o Azure AD Connect |
| **Modificando a configuração do AD FS** | |
| [Reparando a confiança](active-directory-aadconnect-federation-management.md#repairthetrust) |Como reparar a confiança atual entre o AD FS local e o Office 365 / Azure |
| [Adicionando um novo servidor do AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) |Expandindo o farm do AD FS com um servidor do AD FS adicional após a instalação inicial |
| [Adicionando um novo servidor WAP do AD FS](active-directory-aadconnect-federation-management.md#addwapserver) |Expandindo o farm do AD FS com um servidor WAP adicional após a instalação inicial |
| [Adicionar um novo domínio federado](active-directory-aadconnect-federation-management.md#addfeddomain) |Adicionar outro domínio para ser federado com o Azure AD |
| **Tarefas pós-instalação** | |
| [Adicionar ilustração/logotipo personalizado da empresa](active-directory-aadconnect-federation-management.md#customlogo) |Modificar a experiência de entrada especificando o logotipo personalizado que será exibido na página de entrada do AD FS |
| [Adicionar a descrição de entrada](active-directory-aadconnect-federation-management.md#addsignindescription) |Alterando a descrição de entrada na página de entrada do AD FS |
| [Modificando as regras de declaração do AD FS](active-directory-aadconnect-federation-management.md#modclaims) |Modificar/adicionar regras de declaração do AD FS correspondentes à configuração de sincronização do Azure AD Connect |

## <a name="additional-resources"></a>Recursos adicionais
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)
* [Implantação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md)
* [Implantação do AD FS de alta disponibilidade entre fronteiras geográficas no Azure com o Gerenciador de Tráfego do Azure](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

