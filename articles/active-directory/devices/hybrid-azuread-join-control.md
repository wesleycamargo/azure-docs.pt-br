---
title: Como configurar dispositivos adicionados ao Azure Active Directory híbrido | Microsoft Docs
description: Saiba como configurar dispositivos adicionados ao Azure Active Directory híbrido.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 2332923946e414325b9723a59cf493d9d1060cc6
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368674"
---
# <a name="how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>Como controlar ingresso no Azure AD híbrido dos dispositivos

O ingresso no Azure AD híbrido é um processo para registrar automaticamente os dispositivos ingressado no domínio local com Azure AD. Há casos em que não é necessário que todos os dispositivos sejam registrados automaticamente. Isso acontece, por exemplo, durante a distribuição inicial para verificar se tudo está funcionando conforme o esperado.

Este artigo fornece diretrizes sobre como é possível controlar ingresso no Azure AD híbrido dos dispositivos. 


## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com:

-  [Introdução ao gerenciamento de dispositivos no Azure Active Directory](../device-management-introduction.md)
 
-  [Como planejar a implementação de ingresso no Azure Active Directory híbrido](hybrid-azuread-join-plan.md)

-  Configurar ingresso no Azure Active Directory híbrido para [domínios gerenciados](hybrid-azuread-join-managed-domains.md) ou [domínios federados](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Controlar dispositivos atuais do Windows

Para os dispositivos que executam o sistema operacional da área de trabalho do Windows, a versão com suporte é a Atualização de Aniversário do Windows 10 (versão 1607) ou posterior. Como melhor prática, atualize para a última versão do Windows 10.

Todos os dispositivos atuais do Windows registram-se automaticamente no Azure AD ao iniciar o dispositivo ou na entrada do usuário. É possível controlar esse comportamento com um GPO (Objeto de Política de Grupo) ou o System Center Configuration Manager.

Para controlar os dispositivos atuais do Windows, é necessário: 

1.  **Para todos os dispositivos**: desabilitar o registro de dispositivos automático.
2.  **Para dispositivos selecionados**: habilitar o registro de dispositivos automático.

Se você verificou que tudo está funcionando conforme o esperado, significa que está pronto para habilitar o registro de dispositivos automático para todos os dispositivos novamente.



## <a name="group-policy-object"></a>Objeto de Política de Grupo 

É possível controlar o comportamento do registro de dispositivos dos dispositivos implantando o GPO a seguir: **Registrar computadores ingressados nos domínio como dispositivos**

**Para definir o GPO**:

1.  Abra o **Gerenciador do Servidor** e, em seguida, vá para **Ferramentas \> Gerenciamento de Política de Grupo**.

2.  Vá para o nó do domínio correspondente ao domínio que você quer desabilitar/habilitar o registro automático.

3.  Clique com o botão direito do mouse em **Objetos de Política de Grupo** e selecione **Novo**.

4.  Digite um nome (por exemplo, *ingresso no Azure AD híbrido*) para o Objeto de Política de Grupo. 

5.  Clique em **OK**.

6.  Clique com o botão direito do mouse no novo GPO e selecione **Editar**.

7.  Vá para **Configuração do Computador \> Políticas \> Modelos Administrativos \> Componentes do Windows \> Registro de Dispositivos**. 

8.  Clique com o botão direito do mouse em **Registrar computadores associados ao domínio como dispositivos** e selecione **Editar**.

    > [!NOTE] 
    > Esse modelo de política de grupo foi renomeado de versões anteriores do console de Gerenciamento de Política de Grupo. Se estiver usando uma versão anterior do console, vá para **Configuração do Computador \> Políticas \> Modelos Administrativos \> Componentes do Windows \> Ingressar no Local de Trabalho \> Ingressar automaticamente computadores clientes no local de trabalho**. 

9.  Selecione uma das configurações a seguir e clique em **Aplicar**:

    - **Desabilitado** - Para evitar o registro de dispositivos automático
    - **Habilitar** - Para habilitar o registro de dispositivos automático

10. Clique em **OK**.

É necessário vincular o GPO a um local de sua escolha. Por exemplo, para definir essa política para todos os dispositivos atuais ingressados no domínio na organização, vincule o GPO ao domínio. Para fazer uma implantação controlada, defina essa política para dispositivos atuais do Windows ingressados no domínio que pertençam a uma unidade organizacional ou a um grupo de segurança.

### <a name="configuration-manager-controlled-deployment"></a>Implantação controlada pelo Gerenciador de Configurações 

É possível controlar o comportamento do registro de dispositivos atual, configurando a configuração do cliente a seguir: **Registrar automaticamente novos dispositivos ingressados no domínio do Windows 10 com Azure Active Directory**


**Para definir a configuração do cliente**:

1.  Abra o **Gerenciador de Configurações** e, em seguida, vá para **Serviços de Nuvem**.

2.  Em **Configurações do Dispositivo**, selecione uma das seguintes configurações para **Registrar automaticamente novos dispositivos ingressados no domínio do Windows 10 com Azure Active Directory**:

    - **Não** - Para evitar o registro de dispositivos automático
    - **Sim** - Para habilitar o registro de dispositivos automático


3.  Clique em **OK**.
    

É necessário vincular essa configuração do cliente a um local de sua escolha. Por exemplo, para definir essa configuração do cliente para todos os dispositivos atuais do Windows na organização, vincule a configuração do cliente ao domínio. Para fazer uma implantação controlada, é possível definir a configuração do cliente para dispositivos atuais do Windows ingressados no domínio que pertençam a uma unidade organizacional ou a um grupo de segurança.

## <a name="control-windows-down-level-devices"></a>Controlar os dispositivos de nível inferior do Windows

Para registrar os dispositivos de nível inferior do Windows, é necessário baixar e instalar o pacote do Windows Installer (.msi) do Centro de Download na página do [Microsoft Workplace Join para computadores não Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=53554).

Você pode implantar o pacote usando um sistema de distribuição de software como o System Center Configuration Manager. O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro quiet. O Branch Atual do [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) oferece benefícios adicionais de versões anteriores, como a capacidade de rastrear registros concluídos.

O instalador cria uma tarefa agendada no sistema que é executada no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa ingressa silenciosamente o dispositivo com Azure AD com as credenciais do usuário após a autenticação no Azure AD.


Para controlar o registro de dispositivos, é necessário implantar o pacote do Windows Installer somente em um grupo selecionado de dispositivos de nível inferior do Windows. Se você verificou que tudo funciona conforme o esperado, significa que está pronto para distribuir o pacote para todos os dispositivos de nível inferior.


## <a name="next-steps"></a>Próximas etapas

* [Introdução ao gerenciamento de dispositivos no Azure Active Directory](../device-management-introduction.md)



