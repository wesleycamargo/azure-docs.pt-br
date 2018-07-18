---
title: Termos de uso do Azure Active Directory | Microsoft Docs
description: Os Termos de uso do Azure AD permitirão que você e sua empresa forneçam Termos de uso aos usuários de serviços do Azure AD.
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 06/18/2018
ms.author: rolyon
ms.openlocfilehash: 2919ce1d7c57b7a92420ac11b61503caa1fdd3b0
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36267550"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Recurso Termos de uso do Azure Active Directory
Os Termos de uso do Azure AD fornecem um método simples que as organizações podem usar para apresentar informações aos usuários finais. Essa apresentação faz com que os usuários vejam os avisos de isenção de responsabilidade relevantes para os requisitos de conformidade ou legais. Este artigo descreve como começar a usar os Termos de uso do Azure AD.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-can-i-do-with-terms-of-use"></a>O que posso fazer com os Termos de uso?
Os Termos de uso do Azure AD permitem que você faça o seguinte:
- Exigir que funcionários ou convidados aceitem seus Termos de uso antes de obter acesso.
- Apresentar Termos de uso gerais para todos os usuários em sua organização.
- Apresentar Termos de uso específicos com base em atributos de um usuário (por exemplo, médicos, enfermeiras, funcionários locais ou internacionais, usando [grupos dinâmicos](active-directory-groups-dynamic-membership-azure-portal.md)).
- Apresentar Termos de uso específicos no acesso de aplicativos com alto impacto comercial, como o Salesforce.
- Apresentar Termos de uso em idiomas diferentes.
- Listar quem aceitou e quem não aceitou seus Termos de uso.
- Exibir um log de auditoria da atividade dos Termos de uso.

## <a name="prerequisites"></a>pré-requisitos
Para usar e configurar os Termos de uso do Azure AD, você deve ter:

- Assinatura do Azure AD Premium P1, P2, EMS E3 ou EMS E5.
    - Se você não tiver uma dessas assinaturas, poderá [obter o Azure AD Premium](fundamentals/active-directory-get-started-premium.md) ou [habilitar a avaliação do Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uma das seguintes contas de administrador para o diretório que você deseja configurar:
    - Administrador global
    - Administrador de segurança
    - Administrador de acesso condicional

## <a name="terms-of-use-document"></a>Documento Termos de uso

O conteúdo dos Termos de uso do Azure AD são apresentados em formato PDF. O arquivo em PDF pode ter qualquer conteúdo, por exemplo, contratos existentes, o que permite que você colete os contratos do usuário final na entrada do usuário. O tamanho da fonte recomendado no PDF é 24.

## <a name="add-terms-of-use"></a>Adicionar Termos de uso
Depois de preparar o documento dos Termos de uso, use o procedimento a seguir para adicioná-lo.

1. Entre no Azure como administrador global, administrador de segurança ou administrador de acesso condicional.

1. Navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

    ![Folha Termos de uso](media/active-directory-tou/tou-blade.png)

1. Clique em **Novos termos**.

    ![Adicionar Termos de Uso](media/active-directory-tou/new-tou.png)

1. Insira o **Nome** dos Termos de Uso

2. Insira o **Nome de exibição**.  Esse é o cabeçalho é que os usuários visualizam quando entram.

3. **Navegue** até o PDF com os seus Termos de uso finalizados e selecione-o.

4. **Selecione** um idioma para os Termos de uso.  A opção de idioma permite carregar vários Termos de uso, cada um com um idioma diferente.  A versão dos Termos de uso que um usuário final verá terá base em suas preferências de navegador.

5. Selecione ativar ou desativar para **Exigir que os usuários expandam os Termos de uso**.  Se essa configuração for ativada, os usuários finais precisarão ler os Termos de uso antes de aceitá-los.

6. Em **Acesso Condicional**, você pode **Impor** os Termos de uso carregados selecionando um modelo ou uma política de acesso condicional personalizada na lista suspensa.  As políticas de acesso condicional personalizadas permitem Termos de uso granulares, para até um aplicativo de nuvem ou um grupo de usuários específicos.  Para obter mais informações, confira [Configurando as políticas de acesso condicional](active-directory-conditional-access-best-practices.md).

    >[!IMPORTANT]
    >Os controles da política de acesso condicional (incluindo os Termos de uso) não oferecem suporte à imposição nas contas de serviço.  Recomenda-se excluir todas as contas de serviço da política de acesso condicional.

7. Clique em **Criar**.

8. Se você selecionou um modelo de acesso condicional personalizado, uma nova tela é exibida e permite que você personalize a política de acesso condicional.

    Agora você deve ver os novos Termos de uso.

    ![Adicionar Termos de Uso](media/active-directory-tou/create-tou.png)

## <a name="view-who-has-accepted-and-declined"></a>Ver quem aceitou e quem recusou
A folha Termos de uso mostra uma contagem de usuários que os aceitaram e recusaram. Essas contas e quem aceitou/recusou ficam armazenados enquanto os Termos de uso estão em vigor.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

    ![Auditar evento](media/active-directory-tou/view-tou.png)

1. Clique nos números em **Aceito** ou **Recusado** para exibir o estado atual dos usuários.

    ![Auditar evento](media/active-directory-tou/accepted-tou.png)

## <a name="view-audit-logs"></a>Exibir logs de auditoria
Se você quiser exibir outras atividades, os Termos de uso do Azure AD incluem logs de auditoria. Cada consentimento do usuário dispara um evento nos logs de auditoria que ficam armazenados durante 30 dias. Você pode exibir esses logs no portal ou baixá-los como um arquivo .csv.

Para começar a usar os logs de auditoria, use o procedimento a seguir:

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

1. Clique em **Exibir logs de auditoria**.

    ![Auditar evento](media/active-directory-tou/audit-tou.png)

1. Você pode filtrar as informações usando as listas suspensas fornecidas para buscar informações de log de auditoria específicas na tela de logs de auditoria do Azure AD.

    ![Auditar evento](media/active-directory-tou/audit-logs-tou.png)

1. Você também pode clicar em **Download** para baixar as informações em um arquivo .csv para uso local.

## <a name="what-terms-of-use-looks-like-for-users"></a>Qual é a aparência dos Termos de uso para os usuários
Após os Termos de uso terem sido criados e aplicados, os usuários que estiverem no escopo visualizarão a tela a seguir na entrada.

![Auditar evento](media/active-directory-tou/user-tou.png)

A tela a seguir mostra a aparência dos Termos de uso em dispositivos móveis.

![Auditar evento](media/active-directory-tou/mobile-tou.png)

### <a name="how-users-can-review-their-terms-of-use"></a>Como os usuários podem analisar os Termos de uso
Os usuários podem analisar e ver os Termos de uso aceitos usando o procedimento a seguir.

1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. No canto superior direito, clique em seu nome e selecione **Perfil** no menu suspenso.

    ![Perfil](media/active-directory-tou/tou14.png)

1. Na página de seu perfil, clique em **Analisar termos de uso**.

    ![Auditar evento](media/active-directory-tou/tou13a.png)

1. A partir daí, você pode analisar os Termos de uso aceitos. 

## <a name="delete-terms-of-use"></a>Excluir Termos de uso
Você pode excluir Termos de uso antigos usando o procedimento a seguir.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

1. Selecione os Termos de uso que você deseja remover.

1. Clique em **Excluir termos**.

1. Na mensagem que será exibida perguntando se deseja continuar, clique em **Sim**.

    ![Adicionar Termos de Uso](media/active-directory-tou/delete-tou.png)

    Agora você não deve mais ver seus Termos de uso.

## <a name="deleted-users-and-active-terms-of-use"></a>Usuários excluídos e Termos de uso ativos
Por padrão, um usuário excluído permanece excluído no Azure AD por 30 dias, durante os quais ele pode ser restaurado por um administrador, se necessário.  Após 30 dias, esse usuário será excluído permanentemente.  Além disso, usando o Portal do Azure Active Directory, um Administrador Global pode explicitamente [excluir de forma permanente um usuário excluído recentemente](fundamentals/active-directory-users-restore.md) antes de decorrido o período de tempo.  Após um usuário ter sido excluído permanentemente, os dados posteriores sobre esse usuário serão removidos dos Termos de uso ativos.  Auditar informações sobre usuários excluídos na trilha de auditoria.

## <a name="policy-changes"></a>Alterações na política
As políticas de acesso condicional entram em vigor imediatamente. Quando isso acontece, o administrador começa a ver um ícone de "nuvem triste" ou "Problemas de token do Azure AD". O administrador deve sair e entrar novamente para atender à nova política.

>[!IMPORTANT]
> Os usuários que estão no escopo precisarão sair e entrar novamente para atender a uma nova política se:
> - uma política de acesso condicional for habilitada nos Termos de uso
> - ou se outros Termos de uso forem criados

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: como fazer para ver quando/se um usuário aceitou os Termos de uso?**</br>
R: você pode clicar no número abaixo de Aceito ao lado dos Termos de uso.  Para obter mais informações, confira [Ver quem aceitou e quem recusou](#view-who-has-accepted-and-declined).  Além disso, um usuário que aceita os Termos de uso é gravado no log de auditoria. Você pode pesquisar o log de auditoria do Azure AD para ver os resultados.  

**P: se os Termos de uso forem alterados, os usuários precisarão aceitá-los novamente?**</br>
R: sim, um administrador pode alterar os Termos de uso e isso requer que os novos termos também sejam aceitos.

**P: os Termos de uso possuem suporte para vários idiomas?**</br>
R: Sim.  Atualmente, há 18 idiomas diferentes que um administrador pode configurar para um mesmo documento de Termos de uso. 

**P: quando os Termos de uso são disparados?**</br>
R: os Termos de uso são disparados no momento em que o usuário faz o logon.

**P: em quais aplicativos posso empregar os Termos de uso?**</br>
R: Você pode criar uma política de acesso condicional nos aplicativos empresariais usando autenticação moderna.  Para obter mais informações, consulte [aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal).

**P: posso adicionar vários Termos de uso para determinado usuário ou aplicativo?**</br>
R: sim, criando várias políticas de acesso condicional, direcionadas a esses grupos ou aplicativos. Se um usuário estiver no escopo de vários Termos de uso, ele deve concordar com os Termos de uso um por um.
 
**P: o que acontece se um usuário recusar os Termos de uso?**</br>
R: O usuário é impedido de acessar o aplicativo. O usuário precisa entrar novamente e concordar com os termos para obter acesso.
 
**P: por quanto tempo as informações ficam armazenadas?**</br>
R: a conta de usuário e quem aceitou/recusou ficarão armazenados enquanto os Termos de uso estiverem em vigor. Os logs de auditoria ficam armazenados durante 30 dias.
