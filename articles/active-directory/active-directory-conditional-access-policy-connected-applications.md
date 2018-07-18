---
title: Configurar políticas de acesso condicional com base no dispositivo do Azure Active Directory | Microsoft Docs
description: Saiba como configurar políticas de acesso condicional com base no dispositivo do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 07957d5ec843c414813d69b7084915bcd70a5a61
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930850"
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Configurar políticas de acesso condicional com base no dispositivo do Azure Active Directory

Com o [acesso condicional do Azure AD (Azure Active Directory)](active-directory-conditional-access-azure-portal.md), você pode controlar como os usuários autorizados podem acessar seus recursos. Por exemplo, é possível limitar o acesso a dispositivos gerenciados para determinados recursos. Uma política de acesso condicional que exige um dispositivo gerenciado também é conhecida como política de acesso condicional com base no dispositivo.

Esse tópico explica como você pode configurar políticas de acesso condicional com base no dispositivo para aplicativos conectados do Azure AD. 


## <a name="before-you-begin"></a>Antes de começar

O acesso condicional com base no dispositivo associa o **acesso condicional do Azure AD** e o **gerenciamento de dispositivos do Azure AD**. Caso ainda não esteja familiarizado com uma dessas áreas, você deverá ler os seguintes tópicos primeiro:

- **[Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md)** - esse tópico fornece uma visão geral conceitual do acesso condicional e da terminologia relacionada.

- **[Introduction to device management in Azure Active Directory](device-management-introduction.md)** (Introdução ao gerenciamento de dispositivos no Azure Active Directory) - esse tópico fornece uma visão geral das várias opções para conectar dispositivos ao Azure AD. 



## <a name="managed-devices"></a>Dispositivos gerenciados  

Em um mundo móvel e em nuvem, o Azure Active Directory permite o logon único para dispositivos, aplicativos e serviços de qualquer lugar. Para determinados recursos no seu ambiente, conceder acesso aos usuários certos talvez não seja o suficiente. Além dos usuários corretos, você também pode exigir que as tentativas de acesso possam ser executadas apenas usando um dispositivo gerenciado.

Um dispositivo gerenciado é um dispositivo que atende os padrões de segurança e conformidade. Em termos simples, os dispositivos gerenciados são dispositivos que estão sob *algum tipo* de controle organizacional. No Microsoft Azure AD, o pré-requisito para um dispositivo gerenciado é que ele tenha sido registrado com o Azure AD. Registrar um dispositivo cria uma identidade para o dispositivo na forma de um objeto de dispositivo. Esse objeto é usado pelo Azure para rastrear as informações de status sobre um dispositivo. Como um administrador do Microsoft Azure AD, você já pode usar esse objeto para alternar (ativar/desativar) o estado de um dispositivo.
  
![Condições baseadas no dispositivo](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Para obter um dispositivo registrado com o Microsoft Azure AD, você tem três opções:

- **[Dispositivos registrados do Microsoft Azure AD](device-management-introduction.md#azure-ad-registered-devices)**  - para obter um dispositivo pessoal registrado com o Microsoft Azure AD

- **[Dispositivos ingressados no Microsoft Azure AD](device-management-introduction.md#azure-ad-joined-devices)**  - para obter um dispositivo Windows 10 organizacional que não esteja associado a um AD local registrado com o Microsoft Azure AD registrado. 

- **[Dispositivos ingressados no Microsoft Azure AD Híbrido](device-management-introduction.md#hybrid-azure-ad-joined-devices)**  - para obter um dispositivo Windows 10 que esteja associado a um AD local registrado com o Microsoft Azure AD.

Para se tornar um dispositivo gerenciado, um dispositivo registrado pode ser um dispositivo ingressado no Microsoft Azure AD Híbrido ou um dispositivo que tenha sido marcado como em conformidade.  

![Condições baseadas no dispositivo](./media/active-directory-conditional-access-policy-connected-applications/47.png)


 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Requer dispositivos que tenham ingressado no Microsoft Azure AD Híbrido

Na política de acesso condicional, você pode selecionar **Exige dispositivo ingressado no Microsoft Azure AD Híbrido** para estabelecer que os aplicativos de nuvem selecionados só podem ser acessados usando um dispositivo gerenciado. 

![Condições baseadas no dispositivo](./media/active-directory-conditional-access-policy-connected-applications/10.png)

Essa configuração aplica-se apenas a dispositivos Windows 10 que esteja ingressados em um Azure AD local. Só é possível registrar esses dispositivos com o Microsoft Azure AD usando uma junção do Microsoft Azure AD Híbrido, que é um [processo automatizado](device-management-hybrid-azuread-joined-devices-setup.md) para obter um dispositivo Windows 10 registrado. 

![Condições baseadas no dispositivo](./media/active-directory-conditional-access-policy-connected-applications/45.png)

O que torna um dispositivo ingressado no Microsoft Azure AD Híbrido um dispositivo gerenciado?  Para dispositivos que fazem parte de um AD local, presume-se que o controle sobre esses dispositivos é imposto usando soluções de gerenciamento, como **System Center Configuration Manager (SCCM)** ou **política de grupo (GP)** para gerenciá-los. Como não há nenhum método para que o Microsoft Azure AD determine se qualquer um dos métodos a seguir foram aplicados a um dispositivo, exigir um dispositivo ingressado no Microsoft Azure AD híbrido é um mecanismo relativamente fraco para solicitar um dispositivo gerenciado. Cabe a você como administrador avaliar se os métodos que são aplicados a seus dispositivos ingressados no domínio local são fortes o suficiente para constituir um dispositivo gerenciado, se tal dispositivo também for um dispositivo ingressado no Microsoft Azure AD híbrido.


## <a name="require-device-to-be-marked-as-compliant"></a>Exigir que o dispositivo seja marcado como em conformidade

A opção de *exigir que um dispositivo seja marcado como em conformidade* é a forma mais segura de solicitar um dispositivo gerenciado.

![Condições baseadas no dispositivo](./media/active-directory-conditional-access-policy-connected-applications/11.png)

Essa opção requer que um dispositivo seja registrado com o Microsoft Azure AD e também marcado como em conformidade por:
         
- Intune 
- Um sistema gerenciado por dispositivo móvel de terceiros que gerencia os dispositivos Windows 10 por meio da integração do Azure AD 
 
![Condições baseadas no dispositivo](./media/active-directory-conditional-access-policy-connected-applications/46.png)



Para um dispositivo que esteja marcado como em conformidade, você pode presumir que: 

- Os dispositivos móveis que sua força de trabalho usa para acessar os dados da empresa são gerenciados
- Os aplicativos móveis que sua força de trabalho usa são gerenciados
- As informações da sua empresa são protegidas ao ajudar a controlar a maneira como sua força de trabalho as acessa e compartilha
- O dispositivo e seus aplicativos são compatíveis com os requisitos de segurança da empresa




## <a name="next-steps"></a>Próximas etapas

Antes de configurar uma política de acesso condicional com base no dispositivo em seu ambiente, você deve dar uma olhada nas [melhores práticas para acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md).

