---
title: Provisionamento de usuário automatizado em aplicativo SaaS no AD do Azure | Microsoft Docs
description: Uma introdução a como você pode usar o Azure AD para provisionar, desprovisionar e atualizar contínua e automaticamente as contas de usuário em vários aplicativos SaaS de terceiros.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2016
ms.author: asmalser-msft

---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory
## <a name="what-is-automated-user-provisioning-for-saas-apps?"></a>O que é o provisionamento automatizado de usuário para aplicativos SaaS?
O Azure AD (Azure Active Directory) permite que você automatize a criação, a manutenção e a remoção de identidades de usuário em aplicativos em nuvem ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) como Dropbox, Salesforce, ServiceNow e muito mais.

**Abaixo estão alguns exemplos do que esse recurso permite que você faça:**

* Crie automaticamente novas contas nos aplicativos SaaS certos para novas pessoas quando entrarem em sua equipe.
* Desative contas automaticamente de aplicativos SaaS quando as pessoas inevitavelmente deixarem a equipe.
* Mantenha as identidades nos seus aplicativos SaaS atualizadas baseadas nas alterações no diretório.
* Provisione objetos não usuário, como grupos, para aplicativos SaaS que dão suporte a eles.

**O provisionamento automatizado de usuário também inclui a seguinte funcionalidade:**

* A capacidade de corresponder identidades existentes entre os aplicativos Azure AD e SaaS.
* Opções de personalização para ajudar o Azure AD a se ajustar às configurações atuais dos aplicativos SaaS que sua organização está usando atualmente.
* Alertas de email opcionais para erros de provisionamento.
* Logs de relatórios e de registros para ajudá-lo a monitorar e a solucionar problemas.

## <a name="why-use-automated-provisioning?"></a>Por que usar o provisionamento automatizado?
Alguns motivos comuns para usar esse recurso incluem:

* Evitar custos, ineficiências e erro humano associados a processos manuais de provisionamento.
* Proteger sua organização removendo instantaneamente as identidades dos usuários dos principais aplicativos SaaS quando eles saírem da organização.
* Importar facilmente um número em massa de usuários para determinado aplicativo SaaS.
* Aproveitar a conveniência de ter a solução de provisionamento ser executada fora das mesmas políticas de acesso do aplicativo que você definiu para o Logon Único do Azure AD.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
**Com que frequência o Azure AD grava as alterações de diretório para o aplicativo SaaS?**

O Azure AD verifica as alterações a cada cinco ou dez minutos. Se o aplicativo SaaS estiver retornando vários erros (por exemplo, como no caso de credenciais de administrador inválidas), o Azure AD diminuirá gradualmente sua frequência para até uma vez por dia até que os erros sejam corrigidos.

**Quanto tempo levará para provisionar meus usuários?**

As alterações incrementais ocorrem quase instantaneamente, mas se você estiver tentando provisionar a maior parte do seu diretório, dependerá do número de usuários e de grupos que você tem. Diretórios pequenos podem levar apenas alguns minutos, diretórios de médio porte podem levar vários minutos e diretórios muito grandes podem levar várias horas.

**Como posso acompanhar o progresso do trabalho de provisionamento atual?**

Você pode revisar o Relatório de Provisionamento de Conta na seção Relatórios do diretório. Outra opção é acessar a guia Painel de Controle para o aplicativo SaaS para o qual você está provisionando e procurar na seção "Status de Integração" na parte inferior da página.

**Como saber se os usuários não estão sendo provisionados corretamente?**

No final do assistente de configuração de provisionamento, existe uma opção para assinar notificações de email em caso de falhas de provisionamento. Você também pode verificar o Relatório de erros de provisionamento para ver quais usuários não foram provisionados e por quê.

**Pode o Azure AD gravar alterações do aplicativo SaaS novamente no diretório?**

Para a maioria dos aplicativos SaaS, o provisionamento é apenas de saída, o que significa que os usuários são gravados do diretório no aplicativo e as alterações do aplicativo não podem ser gravadas de volta no diretório. Para o [Workday](https://msdn.microsoft.com/library/azure/dn762434.aspx), no entanto, o provisionamento é apenas de entrada, o que significa que os usuários são importados para o diretório do Workday e, da mesma forma, as alterações no diretório não são gravadas novamente no Workday.

**Como posso enviar comentários à equipe de engenharia?**

Entre em contato conosco através do [Fórum de comentários do Active Directory do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="how-does-automated-provisioning-work?"></a>Como funciona o trabalho de provisionamento automatizado?
O Azure AD provisiona usuários para aplicativos SaaS conectando-se a pontos de extremidade de provisionamento fornecidos por cada fornecedor de aplicativo. Esses pontos de extremidade permitem que o Azure AD crie, atualize e remova usuários por meio de programação. Abaixo está uma breve visão geral das diferentes etapas que o Azure AD segue para automatizar o provisionamento.

1. Quando você habilita o provisionamento de um aplicativo pela primeira vez, as seguintes ações são executadas:
   * O Azure AD tentará corresponder usuários existentes no aplicativo SaaS com suas identidades correspondentes no diretório. Quando um usuário for correspondido, ele *não* será automaticamente habilitado para logon único. Para que um usuário tenha acesso ao aplicativo, ele deve ser explicitamente atribuído ao aplicativo no AD do Azure, seja diretamente ou seja por meio da associação de grupo.
   * Se você já especificou quais usuários devem ser atribuídos ao aplicativo e o Azure AD não conseguir localizar contas existentes para esses usuários, o Azure AD provisionará novas contas para eles no aplicativo.
2. Depois que a sincronização inicial foi concluída conforme descrito acima, o Azure AD verificará as alterações abaixo a cada 10 minutos:
   * Se novos usuários tiverem sido atribuídos ao aplicativo (diretamente ou por meio de associação de grupo), eles serão provisionados com uma nova conta no aplicativo SaaS.
   * Se um acesso de usuário tiver sido removido, suas contas no aplicativo SaaS serão marcadas como desabilitadas (os usuários nunca são totalmente excluídos, o que protege contra perda de dados no caso de uma configuração incorreta).
   * Se um usuário tiver sido recentemente atribuído ao aplicativo e já tiver uma conta no aplicativo SaaS, essa conta será marcada como habilitada e determinadas propriedades de usuário poderão ser atualizadas se estiverem desatualizadas em relação ao diretório.
   * Se as informações de um usuário (como número de telefone, localização do escritório etc.) forem alteradas no diretório, essas informações também serão atualizadas no aplicativo SaaS.

Para saber mais sobre como os atributos são mapeados entre o Azure AD e seu aplicativo SaaS, confira o artigo [Personalização de mapeamentos de atributos](active-directory-saas-customizing-attribute-mappings.md).

## <a name="list-of-apps-that-support-automated-user-provisioning"></a>Lista de aplicativos que dão suporte a provisionamento automatizado de usuários
Clique em um aplicativo para ver um tutorial sobre como configurar o provisionamento automatizado para ele:

* [Box](http://go.microsoft.com/fwlink/?LinkId=286016)
* [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
* [Concur](http://go.microsoft.com/fwlink/?LinkId=309575)
* [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
* [Dropbox for Business](http://go.microsoft.com/fwlink/?LinkId=309581)
* [Google Apps](http://go.microsoft.com/fwlink/?LinkId=309577)
* [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
* [Salesforce](http://go.microsoft.com/fwlink/?LinkId=286017)
* [Salesforce Sandbox](http://go.microsoft.com/fwlink/?LinkId=327869)
* [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
* [Workday](http://go.microsoft.com/fwlink/?LinkId=690250) (provisionamento de entrada)

Para que um aplicativo dê suporte a provisionamento automatizado de usuários, primeiro ele deve fornecer os pontos de extremidade necessários que permitam que programas externos automatizem a criação, a manutenção e a remoção de usuários. Portanto, nem todos os aplicativos SaaS são compatíveis com esse recurso. Para aplicativos que dão suporte a isso, a equipe de engenharia do Azure AD será capaz de criar um conector de provisionamento para eles e esse trabalho é priorizado de acordo com as necessidades de clientes atuais e potenciais.

Para entrar em contato com a equipe de engenharia do Azure AD e solicitar o suporte de provisionamento para mais aplicativos, envie uma mensagem no [Fórum de comentários do Active Directory do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Personalizando os mapeamentos de atributos para provisionamento de usuários](active-directory-saas-customizing-attribute-mappings.md)
* [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de escopo para provisionamento de usuários](active-directory-saas-scoping-filters.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)
* [Notificações de provisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

<!--HONumber=Oct16_HO2-->


