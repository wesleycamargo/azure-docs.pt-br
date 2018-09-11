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
ms.topic: conceptual
ms.component: compliance
ms.date: 09/04/2018
ms.author: rolyon
ms.openlocfilehash: fbed0f5fb213a18f4450ee3aa96c3b8485b16e8b
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781319"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Recurso Termos de uso do Azure Active Directory
Os Termos de uso do Azure AD fornecem um método simples que as organizações podem usar para apresentar informações aos usuários finais. Essa apresentação faz com que os usuários vejam os avisos de isenção de responsabilidade relevantes para os requisitos de conformidade ou legais. Este artigo descreve como começar a usar os Termos de uso do Azure AD.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-can-i-do-with-terms-of-use"></a>O que posso fazer com os Termos de uso?
Os Termos de uso do Azure AD permitem que você faça o seguinte:
- Exigir que funcionários ou convidados aceitem seus Termos de uso antes de obter acesso.
- Apresentar Termos de uso gerais para todos os usuários em sua organização.
- Apresentar Termos de uso específicos com base em atributos de um usuário (por exemplo, médicos, enfermeiras, funcionários locais ou internacionais, usando [grupos dinâmicos](users-groups-roles/groups-dynamic-membership.md)).
- Apresentar Termos de uso específicos no acesso de aplicativos com alto impacto comercial, como o Salesforce.
- Apresentar Termos de uso em idiomas diferentes.
- Listar quem aceitou e quem não aceitou seus Termos de uso.
- Exibir um log de auditoria da atividade dos Termos de uso.

## <a name="prerequisites"></a>Pré-requisitos
Para usar e configurar os Termos de uso do Azure AD, você deve ter:

- Assinatura do Azure AD Premium P1, P2, EMS E3 ou EMS E5.
    - Se você não tiver uma dessas assinaturas, poderá [obter o Azure AD Premium](fundamentals/active-directory-get-started-premium.md) ou [habilitar a avaliação do Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uma das seguintes contas de administrador para o diretório que você deseja configurar:
    - Administrador global
    - Administrador de segurança
    - Administrador de acesso condicional

## <a name="terms-of-use-document"></a>Documento Termos de uso

O conteúdo dos Termos de uso do Azure AD são apresentados em formato PDF. O arquivo PDF pode ter qualquer conteúdo, como os contratos existentes, o que permite coletar contratos do usuário final durante a entrada do usuário. O tamanho da fonte recomendado no PDF é 24.

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

6. Em **Acesso Condicional**, você pode **Impor** os Termos de uso carregados selecionando um modelo ou uma política de acesso condicional personalizada na lista suspensa.  As políticas de acesso condicional personalizadas permitem Termos de uso granulares, para até um aplicativo de nuvem ou um grupo de usuários específicos.  Para obter mais informações, confira [Configurando as políticas de acesso condicional](conditional-access/best-practices.md).

    >[!IMPORTANT]
    >Os controles da política de acesso condicional (incluindo os Termos de uso) não oferecem suporte à imposição nas contas de serviço.  Recomenda-se excluir todas as contas de serviço da política de acesso condicional.

7. Clique em **Criar**.

8. Se você selecionou um modelo de acesso condicional personalizado, uma nova tela é exibida e permite que você personalize a política de acesso condicional.

    Agora você deve ver os novos Termos de uso.

    ![Adicionar Termos de Uso](media/active-directory-tou/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Exibir relatório de quem aceitou e recusou
A folha Termos de uso mostra uma contagem de usuários que os aceitaram e recusaram. Essas contas e quem aceitou/recusou ficam armazenados enquanto os Termos de uso estão em vigor.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

    ![Auditar evento](media/active-directory-tou/view-tou.png)

1. Clique nos números em **Aceito** ou **Recusado** para exibir o estado atual dos usuários.

    ![Auditar evento](media/active-directory-tou/accepted-tou.png)

## <a name="view-azure-ad-audit-logs"></a>Exibir logs de auditoria do Azure AD
Se você quiser exibir outras atividades, os Termos de uso do Azure AD incluem logs de auditoria. Cada consentimento do usuário dispara um evento nos logs de auditoria que ficam armazenados durante 30 dias. Você pode exibir esses logs no portal ou baixá-los como um arquivo .csv.

Para começar a usar os logs de auditoria do Microsoft Azure AD, use o procedimento a seguir:

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

1. Clique em **Exibir logs de auditoria**.

    ![Auditar evento](media/active-directory-tou/audit-tou.png)

1. Você pode filtrar as informações usando as listas suspensas fornecidas para buscar informações de log de auditoria específicas na tela de logs de auditoria do Azure AD.

    ![Auditar evento](media/active-directory-tou/audit-logs-tou.png)

1. Você também pode clicar em **Download** para baixar as informações em um arquivo .csv para uso local.

## <a name="what-terms-of-use-looks-like-for-users"></a>Qual é a aparência dos Termos de uso para os usuários
Depois que os Termos de Uso forem criados e aplicados, os usuários que estiverem no escopo verão a tela a seguir na entrada.

![Auditar evento](media/active-directory-tou/user-tou.png)

A tela a seguir mostra a aparência dos Termos de uso em dispositivos móveis.

![Auditar evento](media/active-directory-tou/mobile-tou.png)

Os usuários só precisam aceitar os Termos de uso uma vez e não verão ver os Termos de uso nas entradas subsequentes.

### <a name="how-users-can-review-their-terms-of-use"></a>Como os usuários podem analisar os Termos de uso
Os usuários podem analisar e ver os Termos de uso aceitos usando o procedimento a seguir.

1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. No canto superior direito, clique em seu nome e selecione **Perfil** no menu suspenso.

    ![Perfil](media/active-directory-tou/tou14.png)

1. Na página de seu perfil, clique em **Analisar termos de uso**.

    ![Auditar evento](media/active-directory-tou/tou13a.png)

1. A partir daí, você pode analisar os Termos de uso aceitos. 

## <a name="edit-terms-of-use-details"></a>Editar os termos de detalhes de uso
Você pode editar alguns detalhes dos termos de uso, mas não é possível modificar um documento existente. O procedimento a seguir descreve como editar os detalhes.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

1. Selecione os termos de uso que você deseja editar.

1. Clique em **editar termos**.

1. Em termos de edição do painel de uso, altere o nome, nome de exibição ou exigir que os usuários expandam os valores.

    ![Adicionar Termos de Uso](media/active-directory-tou/edit-tou.png)

1. Clique em **Salvar** para salvar as alterações.

    Depois de salvar suas alterações, os usuários terão aceitem novamente os novos termos.

## <a name="add-a-terms-of-use-language"></a>Adicionar termos de uso de linguagem
O procedimento a seguir descreve como adicionar um idioma dos Termos de Uso.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

1. Selecione os termos de uso que você deseja editar.

1. No painel de detalhes, clique na guia **Idiomas**.

    ![Adicionar Termos de Uso](media/active-directory-tou/languages-tou.png)

1. Clique em **Adicionar idioma**.

1. Em termos do painel de linguagem do uso de Add, carregar o PDF localizado e selecione o idioma.

    ![Adicionar Termos de Uso](media/active-directory-tou/language-add-tou.png)

1. Clique em **adicionar** para adicionar o idioma.

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
As políticas de acesso condicional entram em vigor imediatamente. Quando isso acontece, o administrador começa a ver uma imagem de "nuvens tristes" ou "problemas de token do Azure AD". O administrador precisa sair e entrar novamente para atender à nova política.

>[!IMPORTANT]
> Os usuários que estão no escopo precisarão sair e entrar novamente para atender a uma nova política se:
> - uma política de acesso condicional for habilitada nos Termos de uso
> - ou se outros Termos de uso forem criados

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: como fazer para ver quando/se um usuário aceitou os Termos de uso?**</br>
R: Em termos de uso de folha, clique no número em **Aceito**. Também é possível exibir ou pesquisar a atividade de aceitação nos logs de auditoria do Azure AD. Para obter mais informações, consulte [Exibir relatório de quem aceitou e recusou](#view-who-has-accepted-and-declined) e [Exibir logs de auditoria do Azure AD](#view-azure-ad-audit-logs).
 
**P: por quanto tempo as informações ficam armazenadas?**</br>
R: As contagens de usuário no relatório de Termos de uso e quem aceitou/recusou são armazenadas durante a vigência dos Termos de uso. Os logs de auditoria do Azure AD são armazenados por 30 dias.

**P: Por que vejo um número diferente de consentimentos no relatório de Termos de uso em comparação com os logs de auditoria do Azure AD?**</br>
R: O relatório de Termos de uso é armazenado durante o período de vigência desses Termos de uso, enquanto os logs de auditoria do Azure AD são armazenados por 30 dias. Além disso, o relatório Termos de uso exibe apenas o estado de consentimento atual dos usuários. Por exemplo, se um usuário recusar e aceitar, o relatório de Termos de uso mostrará apenas a aceitação desse usuário. Se você precisar ver o histórico, poderá usar os logs de auditoria do Azure AD.

**P: Se eu editar os detalhes de um Termos de uso, ele exige que os usuários aceitem novamente?**</br>
R: Sim, se um administrador editar os detalhes para termos de uso, ele exige que os usuários aceitem novamente os novos termos.

**P: posso atualizar uma existente de condições de uso de documento?**</br>
R: atualmente, é possível atualizar uma existente de condições de uso de documento. Para alterar os termos de uso de documento, você terá que criar novos termos de uso de instância.

**P: Se os hiperlinks estiverem no documento PDF dos Termos de uso, os usuários finais poderão clicar neles?**</br>
R: O PDF é renderizado por padrão como JPEG, portanto, os hiperlinks não são clicáveis. Os usuários têm a opção de selecionar **Você está tendo problemas para exibir? Clique aqui**, que renderiza o PDF nativamente onde os hiperlinks têm suporte.

**P: os Termos de uso possuem suporte para vários idiomas?**</br>
R: Sim. Atualmente, existem 108 idiomas diferentes que um administrador pode configurar para um único Termos de uso.

**P: quando os Termos de uso são disparados?**</br>
R: os Termos de uso são disparados no momento em que o usuário faz o logon.

**P: em quais aplicativos posso empregar os Termos de uso?**</br>
R: Você pode criar uma política de acesso condicional nos aplicativos empresariais usando autenticação moderna.  Para obter mais informações, consulte [aplicativos empresariais](./manage-apps/view-applications-portal.md).

**P: posso adicionar vários Termos de uso para determinado usuário ou aplicativo?**</br>
R: sim, criando várias políticas de acesso condicional, direcionadas a esses grupos ou aplicativos. Se um usuário estiver no escopo de vários Termos de uso, ele deve concordar com os Termos de uso um por um.
 
**P: o que acontece se um usuário recusar os Termos de uso?**</br>
R: O usuário é impedido de acessar o aplicativo. O usuário precisa entrar novamente e concordar com os termos para obter acesso.
 
**P: É possível recusar os Termos de Uso que já foram aceitos?**</br>
R: Você pode [examinar os termos de uso já aceitos](#how-users-can-review-their-terms-of-use), mas atualmente não há nenhuma maneira de recusá-los.

## <a name="next-steps"></a>Próximas etapas

- [Práticas recomendadas para o acesso condicional no Azure Active Directory](conditional-access/best-practices.md)