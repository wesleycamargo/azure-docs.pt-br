---
title: Controlar ingresso no Azure AD híbrido de seus dispositivos | Microsoft Docs
description: Saiba como controlar o ingresso no Azure AD híbrido de seus dispositivos no Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b113dd3e354e778d2cf16182665afff5440d2e5
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57408523"
---
# <a name="control-the-hybrid-azure-ad-join-of-your-devices"></a>Controlar ingresso no Azure AD híbrido de seus dispositivos

O ingresso no Azure AD (Azure Active Directory) híbrido é um processo para registrar automaticamente os dispositivos ingressado no domínio local com Azure AD. Há casos em que você não deseja que todos seus dispositivos sejam registrados automaticamente. Isso é verdadeiro, por exemplo, durante a distribuição inicial para verificar se tudo está funcionando conforme o esperado.

Este artigo fornece diretrizes sobre como controlar ingresso no Azure AD híbrido dos dispositivos. 


## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com:

-  [Introdução ao gerenciamento de dispositivos no Azure Active Directory](../device-management-introduction.md)
 
-  [Planejar sua implementação de junção do Azure Active Directory híbrido](hybrid-azuread-join-plan.md)

-  [Configurar a junção híbrida do Azure Active Directory para domínios gerenciados](hybrid-azuread-join-managed-domains.md) ou [Configurar a junção híbrida do Azure Active Directory para domínios federados](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Controlar dispositivos atuais do Windows

Para os dispositivos que executam o sistema operacional da área de trabalho do Windows, a versão com suporte é a Atualização de Aniversário do Windows 10 (versão 1607) ou posterior. Como melhor prática, atualize para a última versão do Windows 10.

Todos os dispositivos atuais do Windows registram-se automaticamente no Azure AD ao iniciar o dispositivo ou na entrada do usuário. É possível controlar esse comportamento com um GPO (Objeto de Política de Grupo) ou o System Center Configuration Manager.

Para controlar os dispositivos atuais do Windows, é necessário: 


1.  **Todos os dispositivos**: desabilite o registro automático de dispositivos.
2.  **Para dispositivos selecionados**: habilite o registro automático de dispositivos.

Depois de verificar que tudo funciona conforme o esperado, você está pronto para habilitar o registro de dispositivos automático para todos os dispositivos novamente.



### <a name="group-policy-object"></a>Objeto de Política de Grupo 

Você pode controlar o comportamento de registro do dispositivo de seus dispositivos ao implantar o GPO a seguir: **Registrar computadores ingressados no domínio como dispositivos**.

Para definir o GPO:

1.  Abra o **Gerenciador do Servidor** e vá para **Ferramentas** > **Gerenciamento de Política de Grupo**.

2.  Vá para o nó do domínio correspondente ao domínio que você quer desabilitar ou habilitar o registro automático.

3.  Clique com o botão direito do mouse em **Objetos de Política de Grupo** e selecione **Novo**.

4.  Insira um nome (por exemplo, **ingresso no Azure AD híbrido**) para o Objeto de Política de Grupo. 

5.  Selecione **OK**.

6.  Clique com o botão direito do mouse no novo GPO e selecione **Editar**.

7.  Vá para **Configuração do Computador** > **Políticas** > **Modelos Administrativos** > **Componentes do Windows** > **Registro de Dispositivo**. 

8.  Clique com o botão direito do mouse em **Registrar computadores associados ao domínio como dispositivos** e selecione **Editar**.

    > [!NOTE] 
    > Esse modelo de Política de Grupo foi renomeado de versões anteriores do console de Gerenciamento de Política de Grupo. Se você estiver usando uma versão anterior do console, acesse **configuração do computador** > **políticas** > **modelos administrativos**  >  **Componentes do Windows** > **o registro de dispositivo** > **integrado ao domínio de registro de computador como dispositivo**. 

9.  Selecione uma das configurações a seguir e, em seguida, selecione **Aplicar**:

    - **Desabilitado**: Para evitar o registro de dispositivos automático.
    - **Habilitado**: Para habilitar o registro de dispositivos automático.

10. Selecione **OK**.

É necessário vincular o GPO a um local de sua escolha. Por exemplo, para definir essa política para todos os dispositivos atuais ingressados no domínio na organização, vincule o GPO ao domínio. Para fazer uma implantação controlada, defina essa política para dispositivos atuais do Windows ingressados no domínio que pertençam a uma unidade organizacional ou a um grupo de segurança.

### <a name="configuration-manager-controlled-deployment"></a>Implantação controlada pelo Gerenciador de Configurações 

Você pode controlar o comportamento de registro de dispositivo dos dispositivos atuais definindo a seguinte configuração do cliente: **Registrar automaticamente novos dispositivos ingressados no domínio do Windows 10 com o Azure Active Directory**.

Para definir a configuração do cliente:

1.  Abra **Configuration Manager**, selecione **administração**e, em seguida, vá para **configurações de cliente**.

2.  Abra as propriedades de **configurações do cliente padrão** e selecione **serviços de nuvem**.

3.  Em **Configurações do Dispositivo**, selecione uma das seguintes configurações para **Registrar automaticamente novos dispositivos ingressados no domínio do Windows 10 com Azure Active Directory**:

    - **Não**: Para evitar o registro de dispositivos automático.
    - **Sim**: Para habilitar o registro de dispositivos automático.

4.  Selecione **OK**.

É necessário vincular essa configuração do cliente a um local de sua escolha. Por exemplo, para definir essa configuração do cliente para todos os dispositivos atuais do Windows na organização, vincule a configuração do cliente ao domínio. Para fazer uma implantação controlada, é possível definir a configuração do cliente para dispositivos atuais do Windows ingressados no domínio que pertençam a uma unidade organizacional ou a um grupo de segurança.

> [!Important]
> Embora a configuração anterior se encarregue de dispositivos do Windows 10 ingressados no domínio existentes, dispositivos que estão recém ingressando no domínio ainda podem precisar tentar concluir o ingresso no Azure AD híbrido devido ao possível atraso na aplicação da Política de Grupo ou nas configurações do Configuration Manager nos dispositivos. 
>
> Para evitar isso, é recomendável criar uma nova imagem de Sysprep (usada como um exemplo para um método de provisionamento). Crie-a usando um dispositivo que nunca foi ingressado no Azure AD híbrido anteriormente e que já tenha a configuração da Política de Grupo ou a configuração de cliente do Configuration Manager aplicada. Você também deve usar a nova imagem para o provisionamento de novos computadores que ingressam no domínio da sua organização. 

## <a name="control-windows-down-level-devices"></a>Controlar os dispositivos de nível inferior do Windows

Para registrar os dispositivos de nível inferior do Windows, é necessário baixar e instalar o pacote do Windows Installer (.msi) do Centro de Download na página do [Microsoft Workplace Join para computadores não Windows 10](https://www.microsoft.com/download/details.aspx?id=53554).

Você pode implantar o pacote usando um sistema de distribuição de software como o [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro quiet. O atual branch do Configuration Manager oferece benefícios adicionais em relação às versões anteriores, como a capacidade de rastrear registros concluídos.

O instalador cria uma tarefa agendada no sistema que é executada no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa ingressa silenciosamente o dispositivo com Azure AD com as credenciais do usuário após a autenticação no Azure AD.

Para controlar o registro de dispositivos, é necessário implantar o pacote do Windows Installer somente em um grupo selecionado de dispositivos de nível inferior do Windows. Se você verificou que tudo funciona conforme o esperado, está pronto para distribuir o pacote para todos os dispositivos de nível inferior.


## <a name="next-steps"></a>Próximas etapas

* [Introdução ao gerenciamento de dispositivos no Azure Active Directory](../device-management-introduction.md)



