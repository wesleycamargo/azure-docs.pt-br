---
title: "Azure AD Connect: solucionando de problemas durante a sincronização | Microsoft Docs"
description: "Este tópico fornece etapas para solucionar problemas com a sincronização de objetos usando a tarefa de solução de problemas."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: 10ebe039b9d266d15696b397b9cdef9bc7ec2a10
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Solução de problemas de sincronização de objetos com a sincronização do Azure AD Connect
Este documento fornece etapas para solucionar problemas com a sincronização de objetos usando a tarefa de solução de problemas.

## <a name="troubleshooting-task"></a>Tarefa de solução de problemas
Para a implantação do AAD (Azure Active Directory) Connect com a versão <verison> ou posterior, use a tarefa de solução de problemas no assistente para solucionar problemas de sincronização de objetos. Para versões anteriores, solucione o problema manualmente conforme descrito [aqui](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Executar a tarefa de solução de problemas no assistente
Para executar a tarefa de solução de problemas no assistente, execute as etapas a seguir:

1.  Abra uma nova sessão do Windows PowerShell no servidor do Azure AD Connect com a opção Executar como administrador.
2.  Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.
3.  Iniciar o assistente do Azure AD Connect.
4.  Navegue até a página Tarefas Adicionais, selecione Solucionar problemas e clique em Avançar.
5.  Na página de solução de problemas, clique em Iniciar para iniciar o menu de solução de problemas no PowerShell.
6.  No menu principal, selecione Solucionar Problemas de Sincronização de Senha.

### <a name="troubleshooting-input-parameters"></a>Parâmetros de entrada para a solução de problemas
Os parâmetros de entrada a seguir são necessários para a tarefa de solução de problemas:
1.  **Nome distinto do objeto** – este é o nome distinto do objeto que precisa de solução de problemas
2.  **Nome do Conector AD** – este é o nome da floresta do AD em que reside o objeto acima.
3.  Credenciais de administrador global do locatário do Azure Active Directory ![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Entender os resultados da tarefa de solução de problemas
A tarefa de solução de problemas executa as seguintes verificações:

1.  Detectar incompatibilidade de UPN se o objeto estiver sincronizado com o Azure Active Directory
2.  Verificar se o objeto é filtrado devido a filtragem do domínio
3.  Verificar se o objeto é filtrado devido a filtragem do OU

O restante desta seção descreve resultados específicos que são retornados pela tarefa. Em cada caso, a tarefa fornece uma análise seguida pelas ações recomendadas para resolver o problema.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Detectar incompatibilidade de UPN se o objeto estiver sincronizado com o Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>O sufixo UPN não foi verificado com locatário do Azure Active Directory
Quando o sufixo de UserPrincipalName (UPN)/ID de logon alternativo não é verificado com o locatário do Azure Active Directory, os sufixos de UPN são substituídos pelo nome de domínio padrão "onmicrosoft.com".

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Alterando o sufixo de UPN de um domínio federado para outro domínio federado
O Azure Active Directory não permite a sincronização de mudança de sufixo de UserPrincipalName (UPN)/ID de login alternativo de um domínio federado para outro domínio. Isso se aplica a domínios, que são verificados com o locatário do Azure Active Directory e têm o Tipo de Autenticação como Federada.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>O recurso de DirSync do Locatário do Azure Active Directory 'SynchronizeUpnForManagedUsers' está desabilitado
Quando o recurso de DirSync do Locatário do Azure Active Directory 'SynchronizeUpnForManagedUsers' está desabilitado, o Azure Active Directory não permite atualizações de sincronização para UserPrincipalName/ID de logon alternativo para contas de usuário licenciado com autenticação gerenciada.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>O objeto é filtrado devido a filtragem do domínio
### <a name="domain-is-not-configured-to-sync"></a>O domínio não está configurado para sincronização
O objeto está fora do escopo porque o domínio não está sendo configurado. No exemplo abaixo, o objeto está fora do escopo de sincronização, pois o domínio ao qual ele pertence é filtrado da sincronização.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>O domínio é configurado para sincronização, mas tem perfis de execução/etapas de execução ausentes
O objeto está fora do escopo porque o domínio tem perfis de execução/etapas de execução ausentes. No exemplo abaixo, o objeto está fora do escopo de sincronização, pois o domínio ao qual ele pertence tem etapas de execução ausentes para o perfil de execução Importação Completa.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch6.png)

### <a name="object-is-filtered-due-to-ou-filtering"></a>O objeto é filtrado devido a filtragem do OU
O objeto está fora do escopo de sincronização devido a configuração de filtragem de OU. No exemplo abaixo, o objeto pertence à OU=NoSync,DC=bvtadwbackdc,DC=com.  Esse OU não está incluído no escopo de sincronização.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch7.png)

## <a name="html-report"></a>Relatório HTML
Além de analisar o objeto, a tarefa de solução de problemas também gera um relatório HTML que tem tudo o que se sabe sobre o objeto. Este relatório HTML pode ser compartilhado com a equipe de suporte para fazer mais solução de problemas, se necessário.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch8.png)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).