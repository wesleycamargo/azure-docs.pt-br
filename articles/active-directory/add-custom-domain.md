---
title: "Adicionar um domínio personalizado ao Azure AD | Microsoft Docs"
description: "Explica como adicionar um domínio personalizado no Azure Active Directory."
services: active-directory
author: jeffgilb
manager: femila
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 3acdc406b5e753d407c7c6fb32fe0ad5ed6dd68c
ms.contentlocale: pt-br
ms.lasthandoff: 08/30/2017

---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>Início rápido: adicionar um nome de domínio personalizado ao Azure Active Directory

Todo diretório do Azure AD vem com um nome de domínio inicial no formato *nomededomínio*.onmicrosoft.com. O nome de domínio inicial não pode ser alterado nem excluído, mas você pode adicionar seu nome de domínio corporativo para o Azure AD também. Por exemplo, sua organização provavelmente tem outros nomes de domínio que são usados para fazer negócios e usuários que entram usando seu nome de domínio corporativo. Adicionar nomes de domínio personalizados ao Azure AD permite que você atribua nomes de usuário no diretório que são familiares para seus usuários, tais como "alice@contoso.com". em vez de "alice@*<domain name>*.onmicrosoft.com". O processo é simples:

1. Adicionar o nome de domínio personalizado ao diretório
2. Adicionar uma entrada DNS para o nome de domínio no registrador de nome de domínio
3. Verifique o nome de domínio personalizado no Azure AD

## <a name="add-your-custom-domain"></a>Adicionar o domínio personalizado
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Escolha **Mais serviços**, insira **Azure Active Directory** na caixa de texto e selecione **Enter**.
   
   ![Abrir o gerenciamento de usuários](./media/active-directory-domains-add-azure-portal/user-management.png)
3. Na folha ***nome do diretório***, escolha **Nomes de domínio**.
4. Na folha ***nome do diretório* -nomes de domínio**, selecione o comando **Adicionar**.
   
   ![Selecionando o comando Adicionar](./media/active-directory-domains-add-azure-portal/add-command.png)
5. Na folha **Nome de domínio**, insira o nome do seu domínio personalizado na caixa, como "contoso.com" e selecione **Adicionar domínio**. Inclua .com, .net ou outra extensão de nível superior.
6. Na folha ***nome de domínio*** (com o nome de domínio personalizado no título), obtenha informações de entrada de DNS que o Azure AD usará para verificar se sua organização é proprietária do nome de domínio personalizado.
   
   ![obter informações de entrada DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> Se você planeja federar seu AD do Windows Server local com o Azure AD, você precisa selecionar a caixa de seleção **Pretendo configurar esse domínio para logon único com meu Active Directory local** quando você executa a ferramenta Azure AD Connect para sincronizar seus diretórios. Você também precisa registrar o mesmo nome de domínio selecionado para federação com seu diretório local na etapa **domínio do Azure AD** no assistente. Você pode ver como é essa etapa do assistente [nessas instruções](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se você não tiver a ferramenta Azure AD Connect, poderá [baixá-la aqui](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Adicione a entrada DNS no registrador de nome de domínio para o domínio
A próxima etapa para usar o nome de domínio personalizado com o Azure AD é atualizar o arquivo de zona DNS do domínio. O Azure AD pode então verificar se a organização é proprietária do nome de domínio personalizado. Você pode usar [DNS do Azure no portal do Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) para um único ponto de gerenciamento para o Azure, Office 365 e registros DNS externos no Azure.

1. Entre no registrador de nome de domínio para o domínio. Se você não tem acesso para atualizar a entrada DNS, peça à pessoa ou à equipe que tem esse acesso para concluir a etapa 2 e avisá-lo quando ela for concluída.
2. Atualize o arquivo de zona DNS para o domínio adicionando a entrada DNS fornecida pelo AD do Azure. Essa entrada DNS permitirá que o AD do Azure verifique sua propriedade do domínio. A entrada DNS não altera nenhum comportamento, como o roteamento de emails ou a hospedagem na Web.

Para obter ajuda sobre essa adição da entrada DNS, leia [Instruções para adicionar uma entrada DNS em registradores DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Verificar o nome de domínio com o AD do Azure
Após adicionar a entrada DNS, você está pronto para verificar o nome de domínio com o Azure AD.

Um nome de domínio só poderá ser verificado depois que os registros DNS forem propagados. Essa propagação geralmente leva apenas alguns segundos, mas às vezes pode levar uma hora ou mais. Se a verificação não funcionar na primeira vez, tente novamente mais tarde.

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **Procurar**, insira Gerenciamento de Usuário na caixa de texto e selecione **Enter**.
   
   ![Abrir o gerenciamento de usuários](./media/active-directory-domains-add-azure-portal/user-management.png)
3. Na folha **Gerenciamento de usuário – Nomes de domínio** , selecione o nome de domínio não verificado que você deseja verificar.
4. Na folha ***nome de domínio*** (ou seja, a folha que é aberta com o novo nome de domínio no título), selecione **Verificar** para concluir a verificação.

> [!TIP]
> Você pode adicionar até 900 nomes de domínio personalizados, mas apenas um pode ser [definido como o nome de domínio primário para seu diretório do Azure AD](active-directory-domains-manage-azure-portal.md#set-the-primary-domain-name-for-your-azure-ad-directory) usado por padrão quando você criar novas contas.

Agora você pode criar contas de usuário baseadas em nuvem ou atualizar informações de conta de usuário locais sincronizadas anteriormente, usando seu nome de domínio personalizado. Você também pode modificar as informações de sufixo de domínio da conta de usuário sincronizada anteriormente usando o [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou a [API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## <a name="troubleshooting"></a>Solucionar problemas
Se você não puder verificar um nome de domínio personalizado, experimente as seguintes etapas de solução de problemas:

1. **Aguarde uma hora**. Os registros DNS precisam ser propagados para que o Azure AD possa verificar o domínio. Isso pode levar uma hora ou mais.
2. **Verifique se o registro DNS foi inserido e se está correto**. Conclua essa etapa no site do registrador de nome de domínio do domínio. O Azure AD não poderá verificar o nome de domínio se a entrada DNS não estiver presente no arquivo de zona DNS ou se não for uma correspondência exata da entrada DNS que o Azure AD forneceu. Se você não tiver acesso para atualizar os registros DNS parado domínio no registrador de nome de domínio, compartilhe a entrada DNS com a pessoa ou equipe em sua organização que tem acesso a isso e peça-lhe que adicione a entrada DNS.
3. **Exclua o nome de domínio de outro diretório no Azure AD**. Um nome de domínio pode ser verificado apenas em um único diretório. Se um nome de domínio tiver sido verificado anteriormente em outro diretório, deverá ser excluído de lá para poder ser verificado no novo diretório. Para saber mais sobre a exclusão de nomes de domínio, leia [Gerenciar nomes de domínio personalizados](active-directory-domains-manage-azure-portal.md).    

## <a name="add-more-custom-domain-names"></a>Adicionar mais nomes de domínio personalizados
Se sua organização usa mais de um nome de domínio personalizado, como 'contoso.com' e 'contosobank.com', você pode adicionar até 900 outros repetindo as etapas neste artigo para cada um deles.

### <a name="learn-more"></a>Saiba mais
[Visão geral conceitual de nomes de domínio personalizados no Azure AD](active-directory-add-domain-concepts.md)

[Gerenciar nomes de domínio personalizados](active-directory-domains-manage-azure-portal.md)


## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você aprendeu como adicionar um domínio personalizado ao Azure AD. 

Você pode usar o link a seguir para adicionar um novo domínio personalizado do Azure AD do Portal do Azure.

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 
