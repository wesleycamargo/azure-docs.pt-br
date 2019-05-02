---
title: Criar um pool de hosts de Versão Prévia da Área de Trabalho Virtual do Windows com o Azure Marketplace – Azure
description: Como criar um pool de hosts de versão prévia da Área de Trabalho Virtual do Windows com o Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: c4ab209d63a01d9e1089eec351eff64fc116f403
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924967"
---
# <a name="tutorial-create-a-host-pool-with-azure-marketplace"></a>Tutorial: Criar um pool de host com o Azure Marketplace

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de locatário de Versão Prévia da Área de Trabalho Virtual do Windows. Cada pool de hosts pode conter um grupo de aplicativo com o qual os usuários podem interagir como se eles estivessem em uma área de trabalho física.

Este artigo descreve como criar um pool de hosts em um locatário de Área de Trabalho Virtual do Windows usando uma oferta do Microsoft Azure Marketplace. Isso inclui a criação de um pool de hosts na Área de Trabalho Virtual do Windows, a criação de um grupo de recursos com VMs em uma assinatura do Azure, o ingresso dessas VMs ao domínio do Active Directory e o registro das VMs com a Área de Trabalho Virtual do Windows.

Antes de começar, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usar na sessão do PowerShell, caso ainda não tenha feito isso.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em <https://portal.azure.com>.

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Execute a oferta do Azure Marketplace para provisionar um novo pool de hosts

Para executar a oferta do Azure Marketplace para provisionar um novo pool de hosts:

1. Selecione **+** ou **+ Criar um recurso**.
2. Insira **Área de Trabalho Virtual do Windows** na janela de pesquisa do Marketplace.
3. Selecione **Área de Trabalho Virtual do Windows – Provisionar um pool de hosts** e, em seguida, selecione **Criar**.

Siga as diretrizes para inserir as informações nas folhas apropriadas.

### <a name="basics"></a>Noções básicas

Veja o que você faz para a folha Noções básicas:

1. Insira um nome para o pool de hosts que seja exclusivo dentro do locatário da Área de Trabalho Virtual do Windows.
2. Selecione a opção apropriada para a área de trabalho pessoal. Se você selecionar **Sim**, cada usuário que se conectar a esse pool de hosts será atribuído permanentemente a uma máquina virtual.
3. Insira uma lista separada por vírgulas de usuários que podem entrar nos clientes da Área de Trabalho Virtual do Windows e acesse uma área de trabalho após a conclusão da oferta do Azure Marketplace. Por exemplo, se você desejar atribuir o acesso user1@contoso.com e user2@contoso.com, insira "user1@contoso.com,user2@contoso.com".
4. Selecione **Criar** e forneça um nome para o novo grupo de recursos.
5. Para **Local**, selecione o mesmo local da rede virtual que tenha conectividade com o servidor do Active Directory.
6. Selecione **OK**.

### <a name="configure-virtual-machines"></a>Configurar máquinas virtuais

Para a folha Configurar máquinas virtuais:

1. Aceite os padrões ou personalize o número e tamanho das VMs.
2. Digite um prefixo para os nomes das máquinas virtuais. Por exemplo, se você inserir o nome “prefixo”, as máquinas virtuais serão chamadas de "prefixo-0," "prefixo-1" e assim por diante.
3. Selecione **OK**.

### <a name="virtual-machine-settings"></a>Configurações da máquina virtual

Para a folha de configuração da máquina virtual:

1. Selecione a **Origem da imagem** e insira as informações apropriadas para saber como encontrá-las e como armazená-las. Se você optar por não usar discos gerenciados, selecione a conta de armazenamento que contém o arquivo .vhd.
2. Insira o nome UPN e a senha para a conta de domínio que ingressará as VMs ao domínio do Azure Directory. Esse mesmo nome de usuário e senha será criado nas máquinas virtuais como uma conta local. É possível redefinir essas contas locais mais tarde.
3. Selecione a rede virtual que tem conectividade com o servidor do Azure Directory e, em seguida, escolha uma sub-rede para hospedar as máquinas virtuais.
4. Selecione **OK**.

### <a name="windows-virtual-desktop-preview-tenant-information"></a>Informações de locatário da Versão Prévia da Área de Trabalho Virtual do Windows

Para a folha de informações de locatário da Área de Trabalho Virtual do Windows:

1. Insira o **nome do grupo de locatário da Área de Trabalho Virtual do Windows** para o grupo de locatário que contém seu locatário. Se você não tiver um nome de grupo de locatário específico planejado, deixe-o como o padrão.
2. Insira o **Nome de locatário da Área de Trabalho Virtual do Windows** para o locatário no qual você criará esse pool de hosts.
3. Especifique o tipo de credenciais que você deseja usar para autenticar como o proprietário RDS do locatário da Área de Trabalho Virtual do Windows. Se você selecionar **Entidade de serviço**, você também deverá fornecer a **ID de locatário do Azure AD** associada à entidade de serviço.
4. Insira as credenciais para a conta do administrador do locatário. Há suporte apenas para entidades de serviço com uma credencial de senha.
5. Selecione **OK**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Conclua a instalação e crie a máquina virtual

Para as últimas duas folhas:

1. Na folha **Resumo**, examine as informações da instalação. Se for necessário alterar alguma coisa, volte para a folha apropriada e faça sua alteração antes de continuar. Se as informações parecerem certas, selecione **OK**.
2. Na folha **Comprar**, examine outras informações sobre sua compra do Azure Marketplace.
3. Selecione **Criar** para implantar seu pool de hosts.

Dependendo de quantas VMs você estiver criando, esse processo poderá levar 30 minutos ou mais para ser concluído.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Opcional) Atribuir usuários adicionais ao grupo de aplicativos da área de trabalho

Após a conclusão da oferta do Azure Marketplace, será possível atribuir mais usuários ao grupo de aplicativos da área de trabalho antes de começar a testar as áreas de trabalho de sessão completa em suas máquinas virtuais. Se você já tiver adicionado usuários padrão na oferta do Azure Marketplace e não desejar adicionar mais, será possível ignorar esta seção.

Para atribuir usuários ao grupo de aplicativo de área de trabalho, primeiro abra uma janela do PowerShell. Depois disso, será necessário inserir os dois cmdlets a seguir.

Execute o seguinte cmdlet para entrar no ambiente da Área de Trabalho Virtual do Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Defina o contexto como o grupo de locatário da Área de Trabalho Virtual do Windows que você especificou na oferta do Azure Marketplace com o seguinte cmdlet. Se você deixou o valor do grupo de locatário da Área de Trabalho Virtual do Windows como o valor padrão na oferta do Azure Marketplace, é possível ignorar esta etapa.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

Depois de concluir essas duas coisas, será possível adicionar usuários ao grupo de aplicativo de área de trabalho com este cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

O UPN do usuário deve corresponder à identidade dele no Azure Active Directory (por exemplo, user1@contoso.com). Se você desejar adicionar vários usuários, deverá executar este cmdlet para cada um.

Após concluir essas etapas, os usuários adicionados ao grupo de aplicativo de área de trabalho poderão entrar na Área de Trabalho Virtual do Windows com os clientes de Área de Trabalho Remota com suporte e verão um recurso para uma área de trabalho de sessão.

Veja os clientes com suporte atuais:

- [Cliente da Área de Trabalho Remota para Windows 7 e Windows 10](connect-windows-7-and-10.md)
- [Cliente Web da Área de Trabalho Virtual do Windows](connect-web.md)

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente da Área de Trabalho Virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 nas VMs. A Área de Trabalho Virtual do Windows não exige uma porta de entrada 3389 aberta para que os usuários acessem as VMs do pool de hosts. Caso você precise abrir a porta 3389 para fins de solução de problemas, recomendamos o uso do [acesso just-in-time à VM](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Próximas etapas

Agora que você criou um pool de hosts e atribuiu usuários para acessar sua área de trabalho, também poderá popular seu pool de hosts com RemoteApps. Para saber mais sobre como gerenciar aplicativos na Área de Trabalho Virtual do Windows, confira o tutorial Gerenciar grupos de aplicativos.

> [!div class="nextstepaction"]
> [Tutorial Gerenciar grupos de aplicativos](./manage-app-groups.md)
