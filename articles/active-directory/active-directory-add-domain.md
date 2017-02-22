---
title: "Adicionar seu nome de domínio personalizado ao Azure Active Directory | Microsoft Docs"
description: "Como adicionar os nomes de domínio da sua empresa ao Azure Active Directory e como verificar o nome de domínio."
services: active-directory
documentationcenter: 
author: jeffsta
manager: femila
editor: 
ms.assetid: 35a6e20a-9907-432b-9d36-16b916a5c249
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/30/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7bd889847f07ebbadc5e427f2b1ff3f9f869e69a


---
# <a name="add-a-custom-domain-name-to-azure-active-directory"></a>Adicionar um nome de domínio personalizado ao Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-domains-add-azure-portal.md)
> * [Portal clássico do Azure](active-directory-add-domain.md)
> 
> 

Você tem um ou mais nomes de domínio que sua organização usa para fazer negócios, e seus usuários entram em sua rede corporativa usando seu nome de domínio corporativo. Agora que você está usando o Azure Active Directory (Azure AD), também poderá adicionar seu nome de domínio corporativo ao AD do Azure também. Isso permite que você atribua os nomes de usuário do seu diretório que sejam familiares para seus usuários, como ‘alice@contoso.com.’. O processo é simples:

1. Adicionar o nome de domínio personalizado ao diretório
2. Adicionar uma entrada DNS para o nome de domínio no registrador de nome de domínio
3. Verifique o nome de domínio personalizado no Azure AD

> [!NOTE]
> Se você planeja configurar o nome de domínio personalizado para ser usado com os Serviços de Federação do Active Directory (AD FS) ou um serviço de token de segurança (STS) diferente na rede corporativa, siga as instruções em [Adicionar e configurar um domínio para federação com o Azure Active Directory](active-directory-add-domain-federated.md). Isso é útil se você planeja sincronizar usuários do diretório corporativo no Azure AD e a [sincronização de hash de senha](active-directory-aadconnectsync-implement-password-synchronization.md) não atende a seus requisitos.
> 
> 

## <a name="add-a-custom-domain-name-to-your-directory"></a>Adicionar um nome de domínio personalizado ao diretório
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com/) com uma conta de usuário que seja um administrador global do diretório do Azure AD.
2. No **Active Directory**, abra o diretório e selecione a guia **Domínios**.
3. Na barra de comandos, selecione **Adicionar**. Insira o nome do seu domínio personalizado, como ‘contoso.com’. Inclua a extensão .com, .net ou outra extensão de nível superior e deixe a caixa de seleção "logon único" (federação) desmarcada.
4. Selecione **Adicionar**.
5. Na segunda página do Assistente para Adicionar Domínio, obtenha a entrada DNS que o Azure AD usará para verificar se sua organização possui o nome de domínio personalizado.

Agora que você adicionou o nome de domínio, o AD do Azure deverá verificar se a sua organização é proprietária do nome de domínio. Antes que o AD do Azure possa executar essa verificação, você deverá adicionar uma entrada DNS ao arquivo de zona DNS para o nome de domínio. Essa tarefa pode ser concluída no site do registrador de nome de domínio para o nome de domínio.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Adicione a entrada DNS no registrador de nome de domínio para o domínio
A próxima etapa para usar o nome de domínio personalizado com o Azure AD é atualizar o arquivo de zona DNS do domínio. Isso habilita o Azure AD a verificar se a organização possui o nome de domínio personalizado.

1. Entre no registrador de nome de domínio para o domínio. Se você não tem acesso para atualizar a entrada DNS, peça à pessoa ou à equipe que tem esse acesso para concluir a etapa 2 e avisá-lo quando ela for concluída.
2. Atualize o arquivo de zona DNS para o domínio adicionando a entrada DNS fornecida pelo AD do Azure. Essa entrada DNS permitirá que o AD do Azure verifique sua propriedade do domínio. A entrada DNS não altera nenhum comportamento, como o roteamento de emails ou a hospedagem na Web.

Para obter ajuda sobre essa adição da entrada DNS, leia [Instruções para adicionar uma entrada DNS em registradores DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Verificar o nome de domínio com o AD do Azure
Após adicionar a entrada DNS, você está pronto para verificar o nome de domínio com o Azure AD.

Se você ainda tiver o assistente **Adicionar domínio** aberto, selecione **Verificar** na terceira página do assistente. Quando você selecionar **Verificar**, o Azure AD procurará a entrada DNS no arquivo de zona DNS do domínio. O Azure AD só pode verificar o nome de domínio depois que os registros DNS são propagados. Essa propagação geralmente leva apenas alguns segundos, mas às vezes pode levar uma hora ou mais. Se a verificação não funcionar na primeira vez, tente novamente mais tarde.

Se o assistente para **Adicionar domínio** ainda não estiver aberto, você poderá verificar o domínio no [portal clássico do Azure](https://manage.windowsazure.com/):

1. Entre com uma conta de usuário que seja um administrador global do diretório do AD do Azure.
2. Abra o diretório e selecione a guia **Domínios** .
3. Selecione o nome de domínio que você deseja verificar e selecione **Verificar** na barra de comandos.
4. Selecione **Verificar** na caixa de diálogo para concluir a verificação.

Agora você pode [atribuir nomes de usuário que incluam o nome de domínio personalizado](active-directory-add-domain-add-users.md).

## <a name="troubleshooting"></a>Solucionar problemas
Se você não puder verificar um nome de domínio personalizado, tente o seguinte. Começaremos com as mais comuns e trabalharemos até as menos comuns.

1. **Aguarde uma hora**. Os registros DNS precisam ser propagados para que o Azure AD possa verificar o domínio. Isso pode levar uma hora ou mais.
2. **Verifique se o registro DNS foi inserido e se está correto**. Conclua essa etapa no site do registrador de nome de domínio do domínio. O Azure AD não poderá verificar o nome de domínio se a entrada DNS não estiver presente no arquivo de zona DNS ou se não for uma correspondência exata da entrada DNS que o Azure AD forneceu. Se você não tiver acesso para atualizar os registros DNS parado domínio no registrador de nome de domínio, compartilhe a entrada DNS com a pessoa ou equipe em sua organização que tem acesso a isso e peça-lhe que adicione a entrada DNS.
3. **Exclua o nome de domínio de outro diretório no Azure AD**. Um nome de domínio pode ser verificado apenas em um único diretório. Se um nome de domínio tiver sido verificado anteriormente em outro diretório, deverá ser excluído de lá para poder ser verificado no novo diretório. Para saber mais sobre a exclusão de nomes de domínio, leia [Gerenciar nomes de domínio personalizados](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Adicionar mais nomes de domínio personalizados
Se sua organização usa vários nomes de domínio personalizados, como 'contoso.com' e 'contosobank.com', você pode adicionar até 900 nomes de domínio. Use as mesmas etapas deste artigo para adicionar cada nome de domínio.

## <a name="next-steps"></a>Próximas etapas
* [Atribuir nomes de usuário que incluem o nome de domínio personalizado](active-directory-add-domain-add-users.md)
* [Gerenciar nomes de domínio personalizados](active-directory-add-manage-domain-names.md)
* [Saiba mais sobre os conceitos de gerenciamento de domínio no AD do Azure](active-directory-add-domain-concepts.md)
* [Mostrar a identidade visual de sua empresa quando os usuários entrarem](active-directory-add-company-branding.md)
* [Usar o PowerShell para gerenciar os nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)




<!--HONumber=Dec16_HO2-->


