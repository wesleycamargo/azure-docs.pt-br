---
title: Criar um locatário na Versão Prévia da Área de Trabalho Virtual do Windows – Azure
description: Descreve como configurar locatários da Versão Prévia da Área de Trabalho Virtual do Windows no Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 1c66b3de9e18cb74c43f20499e4065c7ec7ae5ca
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801667"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Tutorial: Criar um locatário na Versão Prévia da Área de Trabalho Virtual do Windows

A criação de um locatário na Versão Prévia da Área de Trabalho Virtual do Windows é a primeira etapa para a criação de sua solução de virtualização de área de trabalho. Um locatário é um grupo de um ou mais pools de host. Cada pool de hosts consiste em vários hosts de sessão, em execução como máquinas virtuais no Azure e registrados no serviço da Área de Trabalho Virtual do Windows. Cada pool de hosts também consiste em um ou mais grupos de aplicativos que são usados para publicar recursos de aplicativo remoto e área de trabalho remota para usuários. Com um locatário, você pode criar pools de host, criar grupos de aplicativos, atribuir usuários e fazer conexões por meio do serviço.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Conceda permissões do Azure Active Directory ao serviço da Área de Trabalho Virtual do Windows.
> * Atribua a função de aplicativo TenantCreator a um usuário em seu locatário do Azure Active Directory.
> * Crie um locatário da Área de Trabalho Virtual do Windows.

Isto é o que você precisa para configurar seu locatário da Área de Trabalho Virtual do Windows:

* A ID de locatário do [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para os usuários da Área de Trabalho Virtual do Windows.
* Uma conta de administrador global no locatário do Azure Active Directory.
   * Isso também se aplica às organizações CSP (Provedor de Soluções na Nuvem) que criam um locatário da Área de Trabalho Virtual do Windows para seus clientes. Se você for uma organização CSP, deverá conseguir entrar como administrador global do Azure Active Directory do cliente.
   * A conta de administrador deve se originar de locatário do Azure Active Directory no qual você está tentando criar o locatário de Área de Trabalho Virtual do Windows. Esse processo não dá suporte a contas do Azure Active Directory B2B (convidado).
   * A conta de administrador deve ser uma conta corporativa ou de estudante.
* Uma ID da assinatura do Azure

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Conceder permissões do Azure Active Directory ao serviço da Versão Prévia da Área de Trabalho Virtual do Windows

Se você já tiver concedido permissões à Área de Trabalho Virtual do Windows para esse Azure Active Directory, ignore esta seção.

A concessão de permissões ao serviço da Área de Trabalho Virtual do Windows permite que ele consulte o Azure Active Directory para tarefas administrativas e do usuário final.

Para conceder as permissões de serviço:

1. Abra um navegador e conecte-se à [página de consentimento da Área de Trabalho Virtual do Windows](https://rdweb.wvd.microsoft.com).
2. Para **Opção de Consentimento** > **Aplicativo de Servidor**, insira o nome do locatário ou a ID de Diretório do Azure Active Directory e, em seguida, selecione **Enviar**.
        Para clientes do Provedor de Soluções na Nuvem, a ID é a ID da Microsoft do cliente no Portal do Partner. Para clientes do Enterprise, a ID está localizada em **Azure Active Directory** > **Propriedades** > **ID de Diretório**.
3. Entre na página de consentimento da Área de Trabalho Virtual do Windows com uma conta de administrador global. Por exemplo, se você fosse entrar com a organização Contoso, sua conta poderia ser admin@contoso.com ou admin@contoso.onmicrosoft.com.  
4. Selecione **Aceitar**.
5. Aguarde um minuto.
6. Navegue novamente para a [página de consentimento da Área de Trabalho Virtual do Windows](https://rdweb.wvd.microsoft.com).
7. Acesse **Opção de Consentimento** > **Aplicativo Cliente**, insira o mesmo nome de locatário ou a ID de Diretório do Azure AD e, em seguida, selecione **Enviar**.
8. Entre na página de consentimento da Área de Trabalho Virtual do Windows como administrador global como você fez na etapa 3.
9. Selecione **Aceitar**.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Atribuir a função de aplicativo TenantCreator a um usuário em seu locatário do Azure Active Directory

A atribuição de um usuário do Azure Active Directory à função de aplicativo TenantCreator permite que o usuário crie um locatário da Área de Trabalho Virtual do Windows associado ao Azure Active Directory. Você precisará usar sua conta de administrador global para atribuir a função TenantCreator.

Para atribuir a função de aplicativo TenantCreator com sua conta de administrador global:

1. Abra um navegador e conecte-se ao [portal do Azure Active Directory](https://aad.portal.azure.com) com sua conta de administrador global.
   - Se você está trabalhando com vários locatários do Azure AD, é uma melhor prática abrir uma sessão particular do navegador e copiar e colar URLs no endereço.
2. Selecione **Aplicativos empresariais** e pesquise **Área de Trabalho Virtual do Windows**. Você verá os dois aplicativos para os quais forneceu consentimento na seção anterior. Desses dois aplicativos, selecione **Área de Trabalho Virtual do Windows**.
3. Selecione **Usuários e grupos** e, em seguida, **Adicionar usuário**.
4. Selecione Usuários e grupos na folha **Adicionar Atribuição**
5. Pesquise uma conta de usuário que criará o locatário da Área de Trabalho Virtual do Windows.
   - Para simplificar, isso pode ser a conta de administrador global.
6. Selecione a conta de usuário, clique no botão **Selecionar** e, em seguida, selecione **Atribuir**.

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Criar um locatário da Versão Prévia da Área de Trabalho Virtual do Windows

Agora que você recebeu as permissões de serviço da Área de Trabalho Virtual do Windows para consultar o Azure Active Directory e atribuiu a função TenantCreator a uma conta de usuário, crie um locatário da Área de Trabalho Virtual do Windows.

Primeiro, [baixe e importe o módulo da Área de Trabalho Virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso.

Entre na Área de Trabalho Virtual do Windows usando a conta de usuário TenantCreator com este cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Depois disso, crie um locatário da Área de Trabalho Virtual do Windows associado ao locatário do Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Os valores entre colchetes devem ser substituídos pelos valores relevantes à sua organização e ao locatário. Por exemplo, digamos que você seja o TenantCreator da Área de Trabalho Virtual do Windows para a organização Contoso. O cmdlet que você executaria seria semelhante a este:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Próximas etapas

Depois de criar o locatário, você precisará criar um pool de hosts. Para saber mais sobre os pools de host, continue com o tutorial para criar um pool de hosts na Área de Trabalho Virtual do Windows.

> [!div class="nextstepaction"]
> [Tutorial de pool de hosts da Área de Trabalho Virtual do Windows](./create-host-pools-azure-marketplace.md)
