---
title: Criar um pool de host de visualização de área de trabalho Virtual do Windows com um modelo do Azure Resource Manager - Azure
description: Como criar um pool de host na visualização de área de trabalho Virtual do Windows com um modelo do Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 262ec35a8a177652dff12bccb3b5435cb5856d81
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401427"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Criar um pool de host com um modelo do Azure Resource Manager

Pools de host são uma coleção de um ou mais máquinas virtuais idênticas em ambientes de locatário de visualização de área de trabalho Virtual do Windows. Cada pool de host pode conter um grupo de aplicativos que os usuários podem interagir com o que seria em uma área de trabalho física.

Siga as instruções desta seção para criar um pool de host para um locatário de área de trabalho Virtual do Windows com um modelo do Azure Resource Manager fornecido pela Microsoft. Este artigo informa como criar um pool de host na área de trabalho Virtual do Windows, crie um grupo de recursos com as VMs em uma assinatura do Azure, Junte-se nessas VMs ao domínio do AD e registrar as máquinas virtuais com área de trabalho Virtual do Windows.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>O que você precisa executar o modelo do Azure Resource Manager

Verifique se que você sabe o seguinte antes de executar o modelo do Azure Resource Manager:

- Onde é a origem da imagem que você deseja usar. Trata-se de galeria do Azure ou é personalizado?
- Suas credenciais de associação de domínio.
- Suas credenciais de área de trabalho Virtual do Windows.

Quando você cria um pool de host de área de trabalho Virtual do Windows com o modelo do Azure Resource Manager, você pode criar uma máquina virtual da Galeria do Azure, uma imagem gerenciada ou uma imagem não gerenciada. Para saber mais sobre como criar imagens de VM, consulte [preparar um VHD do Windows ou o VHDX para carregar no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) e [criar uma imagem gerenciada de uma VM generalizada no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Executar o modelo do Azure Resource Manager para o provisionamento de um novo pool de host

Para começar, acesse [essa URL do GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Implantar o modelo no Azure

Se você estiver implantando em uma assinatura do Enterprise, role para baixo e selecione **implantar no Azure**, em seguida, ignorar ahead preencha os parâmetros com base em sua origem da imagem.

Se você estiver implantando em uma assinatura do provedor de soluções na nuvem, siga estas etapas para implantar no Azure:

1. Role para baixo e clique com botão direito **implantar no Azure**, em seguida, selecione **cópia local do Link**.
2. Abra um editor de texto como bloco de notas e cole o link lá.
3. Logo após "https://portal.azure.com/" e a hashtag (#) antes de inserir um sinal de arroba (@) seguido do nome de domínio de locatário. Aqui está um exemplo do formato você deve usar: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Entre no portal do Azure como um usuário com permissões de administrador/Colaborador à assinatura do provedor de soluções na nuvem.
5. Cole o link que você copiou para o editor de texto na barra de endereços.

Para obter orientação sobre quais parâmetros você deve inserir para seu cenário, consulte a área de trabalho Virtual do Windows [arquivo Leiame](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). O arquivo Leiame esteja sempre atualizado com as alterações mais recentes.

## <a name="assign-users-to-the-desktop-application-group"></a>Atribuir usuários ao grupo de aplicativos da área de trabalho

Após a conclusão do modelo do GitHub do Azure Resource Manager, atribua acesso de usuário antes de você começar a testar as áreas de trabalho de sessão completa em suas máquinas virtuais.

Primeiro, [Baixe e importe o módulo do Windows PowerShell de área de trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) usar em sua sessão do PowerShell se você ainda não fez isso.

Para atribuir usuários ao grupo de aplicativos da área de trabalho, abra uma janela do PowerShell e execute este cmdlet para entrar no ambiente de área de trabalho Virtual do Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Em seguida, defina o contexto para o grupo de locatário especificado no modelo do Azure Resource Manager com este cmdlet:

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

Depois disso, adicione usuários ao grupo de aplicativo da área de trabalho com este cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

O UPN do usuário deve corresponder à identidade do usuário no Azure Active Directory (por exemplo, user1@contoso.com). Se você quiser adicionar vários usuários, você deve executar este cmdlet para cada usuário.

Depois de concluir essas etapas, os usuários adicionados ao grupo de aplicativo da área de trabalho podem entrar área de trabalho Virtual do Windows com clientes de área de trabalho remota com suporte e consulte um recurso para uma área de trabalho de sessão.
