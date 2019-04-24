---
title: Termos de uso - Azure Active Directory | Microsoft Docs
description: Descreve como começar a usar o Azure Active Directory termos de uso para apresentar informações aos funcionários ou convidados antes de obter acesso.
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
ms.subservice: compliance
ms.date: 04/03/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1f03cd518a15d08971968e04fa69954951c77e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60356718"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Recurso Termos de uso do Azure Active Directory
Os Termos de uso do Azure AD fornecem um método simples que as organizações podem usar para apresentar informações aos usuários finais. Essa apresentação faz com que os usuários vejam os avisos de isenção de responsabilidade relevantes para os requisitos de conformidade ou legais. Este artigo descreve como começar a usar os Termos de uso.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Vídeos de visão geral

O vídeo a seguir fornece uma visão geral rápida dos Termos de uso.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Para ver outros vídeos, consulte:
- [Como implantar os Termos de uso no Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Como implementar os Termos de uso no Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>O que posso fazer com os Termos de uso?
Os Termos de uso do Azure AD têm as seguintes funcionalidades:
- Exigir que funcionários ou convidados aceitem os Termos de uso antes de obterem acesso.
- Exigir que funcionários ou convidados aceitem os Termos de uso em cada dispositivo antes de obterem acesso.
- Exigir que funcionários ou convidados aceitem os Termos de uso em um cronograma recorrente.
- Apresentar Termos de uso gerais para todos os usuários em sua organização.
- Apresentar Termos de uso específicos com base em atributos de um usuário (por exemplo, médicos, enfermeiras, funcionários locais ou internacionais, usando [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md)).
- Apresentar Termos de uso específicos no acesso de aplicativos com alto impacto comercial, como o Salesforce.
- Apresentar Termos de uso em idiomas diferentes.
- Listar quem aceitou e quem não aceitou os Termos de uso.
- Ajudar a atender às normas de privacidade.
- Exibir um log da atividade dos Termos de uso para conformidade e auditoria.
- Criar e gerenciar os Termos de uso usando [APIs do Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (atualmente em versão prévia).

## <a name="prerequisites"></a>Pré-requisitos
Para usar e configurar os Termos de uso do Azure AD, você deve ter:

- Assinatura do Azure AD Premium P1, P2, EMS E3 ou EMS E5.
    - Se você não tiver uma dessas assinaturas, poderá [obter o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ou [habilitar a avaliação do Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uma das seguintes contas de administrador para o diretório que você deseja configurar:
    - Administrador global
    - Administrador de segurança
    - Administrador de acesso condicional

## <a name="terms-of-use-document"></a>Documento Termos de uso

O conteúdo dos Termos de uso do Azure AD são apresentados em formato PDF. O arquivo em PDF pode ter qualquer conteúdo, por exemplo, contratos existentes, o que permite que você colete os contratos do usuário final na entrada do usuário. Para dar suporte a usuários em dispositivos móveis, o tamanho de fonte recomendado em PDF é de 24 pontos.

## <a name="add-terms-of-use"></a>Adicionar Termos de uso
Depois de preparar o documento dos Termos de uso, use o procedimento a seguir para adicioná-lo.

1. Entre no Azure como administrador global, administrador de segurança ou administrador de acesso condicional.

1. Navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

    ![Folha Termos de uso](./media/terms-of-use/tou-blade.png)

1. Clique em **Novos termos**.

    ![Adicionar Termos de Uso](./media/terms-of-use/new-tou.png)

1. Na caixa **Nome**, insira um nome para os Termos de uso que será usado no portal do Azure.

1. Na caixa **Nome de exibição**, insira um título que os usuários visualizam ao entrar.

1. Para ver o **documento de Termos de uso**, navegue até o PDF com os Termos de uso finalizados e selecione-o.

1. Selecione o idioma para o documento de Termos de uso. A opção de idioma permite carregar vários Termos de uso, cada um com um idioma diferente. A versão dos Termos de uso que um usuário final verá terá base em suas preferências de navegador.

1. Para exigir que os usuários finais exibam os Termos de uso antes de aceitá-los, defina **Exigir que os usuários expandam os termos de uso** para **On**.

1. Para exigir que os usuários finais aceitem os Termos de uso em todos os dispositivos pelos quais estiverem acessando, defina **Exigir consentimento dos usuários em todos os dispositivos** para **On**. Para obter mais informações, consulte [Termos de uso por dispositivo](#per-device-terms-of-use).

1. Se você quiser expirar os consentimentos dos Termos de uso em um cronograma, defina **Expirar consentimentos** para **On**. Quando definido como On, duas configurações de cronograma adicionais são exibidas.

    ![Expirar consentimentos](./media/terms-of-use/expire-consents.png)

1. Use as configurações **Expirar a partir de** e **Frequência** para especificar o cronograma para expirações de Termos de uso. A tabela a seguir mostra o resultado para duas configurações de exemplo:

    | Expirar a partir de | Frequência | Result |
    | --- | --- | --- |
    | Data de hoje  | Mensal | A partir de hoje, os usuários devem aceitar os Termos de uso e depois aceitarem novamente todos os meses. |
    | Data no futuro  | Mensal | A partir de hoje, os usuários devem aceitar os Termos de uso. Quando a data futura chegar, os consentimentos irão expirar e depois os usuários devem aceitar novamente a todos os meses.  |

    Por exemplo, se você definir a expiração a partir da data para **1 de janeiro** e a frequência para **Mensal**, segue como ocorreriam as expirações para dois usuários:

    | Usuário | Primeira data de aceitação | Primeira data de expiração | Segunda data de expiração | Terceira data de expiração |
    | --- | --- | --- | --- | --- |
    | Alice | 1 de janeiro | 1 de fevereiro | 1 de março | 1 de abril |
    | Roberto | 15 de janeiro | 1 de fevereiro | 1 de março | 1 de abril |

1. Use a configuração **Duração (dias) até exigir a nova aceitação** para especificar o número de dias antes que o usuário deva aceitar novamente os Termos de uso. Isso permite que os usuários sigam seu próprio cronograma. Por exemplo, se você definir a duração como **30** dias, segue como ocorreriam as expirações para dois usuários:

    | Usuário | Primeira data de aceitação | Primeira data de expiração | Segunda data de expiração | Terceira data de expiração |
    | --- | --- | --- | --- | --- |
    | Alice | 1 de janeiro | 31 de janeiro | 2 de março | 1 de abril |
    | Roberto | 15 de janeiro | 14 de fevereiro | 16 de março | 15 de abril |

    É possível usar as configurações **Expirar consentimentos** e **Duração (dias) até exigir a nova aceitação** juntas, mas normalmente você usa uma ou a outra.

1. Em **Acesso condicional**, use a lista **Imposição com modelo de política de acesso condicional** para selecionar o modelo para impor os Termos de uso.

    ![Políticas de acesso condicional](./media/terms-of-use/conditional-access-templates.png)

    | Modelo | DESCRIÇÃO |
    | --- | --- |
    | **Acesso a aplicativos de nuvem para todos os convidados** | Uma política de acesso condicional será criada para todos os convidados e todos os aplicativos de nuvem. Essa política afeta o portal do Azure. Após ela ser criada, talvez seja necessário sair e entrar novamente. |
    | **Acesso a aplicativos de nuvem para todos os usuários** | Uma política de acesso condicional será criada para todos os usuários e todos os aplicativos de nuvem. Essa política afeta o portal do Azure. Após ela ser criada, será necessário sair e entrar novamente. |
    | **Política personalizada** | Selecione os usuários, grupos e aplicativos aos quais esses Termos de Uso serão aplicados. |
    | **Criar a política de acesso condicional mais tarde** | Estes termos de uso serão exibidos na lista de controle de concessão ao criar uma política de acesso condicional. |

    >[!IMPORTANT]
    >Os controles da política de acesso condicional (incluindo os Termos de uso) não oferecem suporte à imposição nas contas de serviço. Recomenda-se excluir todas as contas de serviço da política de acesso condicional.

     As políticas de acesso condicional personalizadas permitem Termos de uso granulares, para até um aplicativo de nuvem ou um grupo de usuários específicos. Para obter mais informações, consulte [Início rápido: Exigir a aceitação dos termos de uso antes de acessar os aplicativos de nuvem](require-tou.md).

1. Clique em **Criar**.

    Se você selecionou um modelo de acesso condicional personalizado, será exibida uma nova tela que permite personalizar a política de acesso condicional.

    ![Política personalizada](./media/terms-of-use/custom-policy.png)

    Agora você deve ver os novos Termos de uso.

    ![Adicionar Termos de Uso](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Exibir relatório de quem aceitou e recusou
A folha Termos de uso mostra uma contagem de usuários que os aceitaram e recusaram. Essas contas e quem aceitou/recusou ficam armazenados enquanto os Termos de uso estão em vigor.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

    ![Folha Termos de uso](./media/terms-of-use/view-tou.png)

1. Para um Termos de uso, clique no s números em **Aceito** ou **Recusado** para exibir o estado atual dos usuários.

    ![Consentimentos dos Termos de uso](./media/terms-of-use/accepted-tou.png)

1. Para exibir o histórico de um usuário individual, clique no botão de reticências (**...** ) e, em seguida **Exibir histórico**.

    ![Menu Exibir Histórico](./media/terms-of-use/view-history-menu.png)

    No painel de exibição de histórico, você vê um histórico de todos os aceites, recusas e expirações.

    ![Painel Exibir Histórico](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Exibir logs de auditoria do Azure AD
Se você quiser exibir outras atividades, os Termos de uso do Azure AD incluem logs de auditoria. Cada consentimento do usuário dispara um evento nos logs de auditoria que ficam armazenados durante **30 dias**. Você pode exibir esses logs no portal ou baixá-los como um arquivo .csv.

Para começar a usar os logs de auditoria do Microsoft Azure AD, use o procedimento a seguir:

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

1. Selecionar um Termos de uso.

1. Clique em **Exibir logs de auditoria**.

    ![Folha Termos de uso](./media/terms-of-use/audit-tou.png)

1. Na tela de logs de auditoria do Azure AD, é possível filtrar as informações usando as listas fornecidas para buscar informações de log de auditoria específico.

    Você também pode clicar em **Download** para baixar as informações em um arquivo .csv para uso local.

    ![Logs de auditoria](./media/terms-of-use/audit-logs-tou.png)

    Se você clicar em um log, um painel é exibido com detalhes adicionais da atividade.

    ![Detalhes da atividade](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Qual é a aparência dos Termos de uso para os usuários
Após os Termos de uso terem sido criados e aplicados, os usuários que estiverem no escopo visualizarão a tela a seguir na entrada.

![Entrada do usuário da Web](./media/terms-of-use/user-tou.png)

Os usuários podem exibir os termos de uso e, se necessário, use os botões para ampliar e reduzir.

![Exibir termos de uso com botões de zoom](./media/terms-of-use/zoom-buttons.png)

A tela a seguir mostra a aparência dos Termos de uso em dispositivos móveis.

![Entrada do dispositivo móvel do usuário](./media/terms-of-use/mobile-tou.png)

Os usuários só precisam aceitar os termos de uso de uma vez e não poderão ver os termos de uso novamente em conexões subsequentes.

### <a name="how-users-can-review-their-terms-of-use"></a>Como os usuários podem analisar os Termos de uso
Os usuários podem analisar e ver os Termos de uso aceitos usando o procedimento a seguir.

1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. No canto superior direito, clique em seu nome e selecione **Perfil**.

    ![Perfil](./media/terms-of-use/tou14.png)

1. Na página de seu perfil, clique em **Analisar termos de uso**.

    ![Perfil - Revisar termos de uso](./media/terms-of-use/tou13a.png)

1. A partir daí, você pode analisar os Termos de uso aceitos.

## <a name="edit-terms-of-use-details"></a>Editar os termos de detalhes de uso
Você pode editar alguns detalhes dos termos de uso, mas não é possível modificar um documento existente. O procedimento a seguir descreve como editar os detalhes.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

1. Selecione os termos de uso que você deseja editar.

1. Clique em **editar termos**.

1. Em termos de edição do painel de uso, altere o nome, nome de exibição ou exigir que os usuários expandam os valores.

    Se houver outras configurações que você queira alterar, como o documento PDF, exigir consentimento dos usuários em todos os dispositivos, expirar consentimentos, duração para nova aceitação, ou a política de acesso condicional, é necessário criar um novo Termos de uso.

    ![Editar termos de uso](./media/terms-of-use/edit-tou.png)

1. Clique em **Salvar** para salvar as alterações.

    Depois de salvar as alterações, os usuários terão que aceitar novamente essas edições.

## <a name="add-a-terms-of-use-language"></a>Adicionar termos de uso de linguagem
O procedimento a seguir descreve como adicionar um idioma dos Termos de Uso.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

1. Selecione os termos de uso que você deseja editar.

1. No painel de detalhes, clique na guia **Idiomas**.

    ![Adicionar Termos de Uso](./media/terms-of-use/languages-tou.png)

1. Clique em **Adicionar idioma**.

1. Em termos do painel de linguagem do uso de Add, carregar o PDF localizado e selecione o idioma.

    ![Adicionar Termos de Uso](./media/terms-of-use/language-add-tou.png)

1. Clique em **adicionar** para adicionar o idioma.

## <a name="per-device-terms-of-use"></a>Termos de uso por dispositivo

A configuração **Exigir consentimento dos usuários finais em cada dispositivo** permite que você exija que os usuários finais aceitem os Termos de uso em cada dispositivo dos quais eles acessam. O usuário final precisará ingressar o dispositivo dele no Azure AD. Quando o dispositivo estiver ingressado, a ID do dispositivo é usada para impor os Termos de uso em cada dispositivo.

Segue uma lista dos softwares e plataformas com suporte.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Outros |
> | --- | --- | --- | --- | --- |
> | **Aplicativo nativo** | Sim | sim | Sim |  |
> | **Microsoft Edge** | Sim | sim | Sim |  |
> | **Internet Explorer** | Sim | sim | Sim |  |
> | **Chrome (com extensão)** | Sim | sim | Sim |  |

Os Termos de uso por dispositivo têm as seguintes restrições:

- Um dispositivo somente pode ser unido a um locatário.
- Um usuário deve ter permissões para ingressar seu dispositivo.
- Não há suporte para o aplicativo de registro do Intune.
- Não há suporte para os usuários de B2B do AD do Azure.

Se o dispositivo do usuário não estiver ingressado, ele receberá uma mensagem de que é necessário ingressar o dispositivo dele. A experiência dele será dependente da plataforma e do software.

### <a name="join-a-windows-10-device"></a>Ingressar um dispositivo com Windows 10

Se um usuário estiver usando o Windows 10 e o Microsoft Edge, ele receberá uma mensagem semelhante à seguinte para [ingressar o dispositivo](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e Microsoft Edge - confirmação para ingressar dispositivo](./media/terms-of-use/per-device-win10-edge.png)

Se estiver usando o Chrome, o usuário será solicitado a instalar a [Extensão de Contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Navegadores

Se um usuário estiver usando um navegador sem suporte, ele será solicitado a usar um navegador diferente.

![Navegador sem suporte](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Excluir Termos de uso
Você pode excluir Termos de uso antigos usando o procedimento a seguir.

1. Entre no Azure e navegue até **Termos de uso** em [https://aka.ms/catou](https://aka.ms/catou).

1. Selecione os Termos de uso que você deseja remover.

1. Clique em **Excluir termos**.

1. Na mensagem que será exibida perguntando se deseja continuar, clique em **Sim**.

    ![Excluir Termos de uso](./media/terms-of-use/delete-tou.png)

    Agora você não deve mais ver seus Termos de uso.

## <a name="deleted-users-and-active-terms-of-use"></a>Usuários excluídos e Termos de uso ativos
Por padrão, um usuário excluído permanece excluído no Azure AD por 30 dias, durante os quais ele pode ser restaurado por um administrador, se necessário. Após 30 dias, esse usuário será excluído permanentemente. Além disso, usando o portal do Azure Active Directory, um Administrador Global pode explicitamente [excluir permanentemente um usuário excluído recentemente](../fundamentals/active-directory-users-restore.md) antes de atingir o período de tempo. Após um usuário ter sido excluído permanentemente, os dados posteriores sobre esse usuário serão removidos dos Termos de uso ativos. Auditar informações sobre usuários excluídos na trilha de auditoria.

## <a name="policy-changes"></a>Alterações na política
As políticas de acesso condicional entram em vigor imediatamente. Quando isso acontece, o administrador começa a ver uma imagem de "nuvens tristes" ou "problemas de token do Azure AD". O administrador precisa sair e entrar novamente para atender à nova política.

>[!IMPORTANT]
> Os usuários que estão no escopo precisarão sair e entrar novamente para atender a uma nova política se:
> - uma política de acesso condicional for habilitada nos Termos de uso
> - ou se outros Termos de uso forem criados

## <a name="b2b-guests-preview"></a>Convidados B2B (versão prévia)

A maioria das organizações tem um processo vigente para que seus funcionários consintam com os Termos de uso e as políticas de privacidade da organização. Mas como você pode impor os mesmos consentimentos para convidados B2B (business-to-business) do Azure AD quando eles forem adicionados por meio do SharePoint ou Teams? Usando o acesso condicional e termos de uso, é possível impor uma política diretamente para os usuários convidados B2B. Durante o fluxo de resgate de convite, o usuário é apresentado aos termos de uso. No momento, esse suporte está na versão prévia.

Termos de uso só serão exibidos quando o usuário tiver uma conta de convidado no Azure AD. SharePoint Online no momento, tem um [experiência de destinatário compartilhamento externa ad-hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) para compartilhar um documento ou uma pasta que não requer que o usuário tenha uma conta de convidado. Nesse caso, um Termos de uso não é exibido.

![Todos os usuários convidados](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Suporte para aplicativos de nuvem (versão prévia)

Termos de uso podem ser usados para diferentes aplicativos de nuvem, como Proteção de Informações do Azure e Microsoft Intune. No momento, esse suporte está na versão prévia.

### <a name="azure-information-protection"></a>Proteção de Informações do Azure

É possível configurar uma política de acesso condicional para o aplicativo de Proteção de Informações do Azure e exigir os Termos de uso quando um usuário acessa um documento protegido. Isso vai disparar os Termos de uso antes que um usuário acesse um documento protegido pela primeira vez.

![Aplicativo de nuvem de Proteção de Informações do Azure](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Registro do Microsoft Intune

É possível configurar uma política de acesso condicional para o aplicativo de Registro do Microsoft Intune e exigir os Termos de uso antes do registro de um dispositivo no Intune. Para obter mais informações, leia [Escolher a solução de Termos correta para a postagem no blog da organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Aplicativo de nuvem do Microsoft Intune](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Não há suporte para o aplicativo de registro do Intune com [termos de uso por dispositivo](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Como fazer para ver quando/se um usuário aceitou os Termos de uso?**<br />
R: Na folha de Termos de uso, clique no número em **Aceito**. Também é possível exibir ou pesquisar a atividade de aceitação nos logs de auditoria do Azure AD. Para obter mais informações, confira Exibir relatório de quem aceitou e recusou e [Exibir logs de auditoria do Azure AD](#view-azure-ad-audit-logs).

**P: Por quanto tempo as informações ficam armazenadas?**<br />
R: As contagens de usuário no relatório de Termos de uso, incluindo quem aceitou/recusou, são armazenadas durante a vigência dos Termos de uso. Os logs de auditoria do Azure AD são armazenados por 30 dias.

**P: Por que vejo um número diferente de consentimentos no relatório de Termos de uso em comparação com os logs de auditoria do Azure AD?**<br />
R: O relatório de Termos de uso é armazenado durante o período de vigência desses Termos de uso, enquanto os logs de auditoria do Azure AD são armazenados por 30 dias. Além disso, o relatório Termos de uso exibe apenas o estado de consentimento atual dos usuários. Por exemplo, se um usuário recusar e aceitar, o relatório de Termos de uso mostrará apenas a aceitação desse usuário. Se você precisar ver o histórico, poderá usar os logs de auditoria do Azure AD.

**P: Se eu editar os detalhes de um Termos de uso, ele exige que os usuários aceitem novamente?**<br />
R: Não, se um administrador editar os detalhes dos Termos de uso (nome, nome de exibição, exigir que usuários expandam ou adicionar um idioma), não é necessário que os usuários aceitem novamente os novos termos.

**P: Posso atualizar um documento de Termos de uso existente?**<br />
R: Atualmente, não é possível atualizar um documento de Termos de uso existente. Para alterar os termos de uso de documento, você terá que criar novos termos de uso de instância.

**P: Se houverem hiperlinks no documento PDF de Termos de uso, os usuários finais poderão clicar neles?**<br />
R: O PDF é renderizado por padrão como JPEG, portanto, os hiperlinks não são clicáveis. Os usuários têm a opção de selecionar **Você está tendo problemas para exibir? Clique aqui**, que renderiza o PDF nativamente onde os hiperlinks têm suporte.

**P: Os Termos de uso podem dar suporte para vários idiomas?**<br />
R: Sim. Atualmente, existem 108 idiomas diferentes que um administrador pode configurar para um único Termos de uso. Um administrador pode carregar vários documentos em PDF e marcar esses documentos com um idioma correspondente (até 108). Quando os usuários finais entram, examinamos a preferência de idioma do navegador deles e exibimos o documento correspondente. Se não houver nenhuma correspondência, exibimos o documento padrão, que é o primeiro documento carregado.

**P: Quando os Termos de uso são disparados?**<br />
R: Os Termos de uso são disparados durante a experiência de logon.

**P: Para quais aplicativos posso destinar os Termos de uso?**<br />
R: Você pode criar uma política de acesso condicional nos aplicativos empresariais usando autenticação moderna. Para obter mais informações, consulte [aplicativos empresariais](./../manage-apps/view-applications-portal.md).

**P: Posso adicionar vários Termos de uso para determinado usuário ou aplicativo?**<br />
R: Sim, criando várias políticas de acesso condicional direcionadas a esses grupos ou aplicativos. Se um usuário estiver no escopo de vários Termos de uso, ele deve aceitar os Termos de uso um por um.

**P: O que acontece se um usuário recusar os Termos de uso?**<br />
R: O usuário é impedido de acessar o aplicativo. O usuário precisa entrar novamente e aceitar os termos para obter acesso.

**P: É possível recusar os Termos de uso que foram anteriormente aceitos?**<br />
R: Você pode [revisar os Termos de uso anteriormente aceitos](#how-users-can-review-their-terms-of-use), mas atualmente não há nenhuma maneira de cancelar a aceitação.

**P: O que acontece se também estiver usando os termos e condições do Intune?**<br />
R: Caso já tenha configurado os Termos de uso do Azure AD e os [Termos e condições do Intune](/intune/terms-and-conditions-create), o usuário será solicitado a aceitar ambos. Para obter mais informações, consulte a [Escolha da solução de Termos correta para a publicação do seu blog da organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

## <a name="next-steps"></a>Próximas etapas

- [Início rápido: Exigir a aceitação dos termos de uso antes de acessar os aplicativos de nuvem](require-tou.md)
- [Práticas recomendadas para o acesso condicional no Azure Active Directory](best-practices.md)
