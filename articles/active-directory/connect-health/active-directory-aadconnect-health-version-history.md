---
title: "Histórico de versão do Azure AD Connect Health"
description: "Este documento descreve as versões do Azure AD Connect Health e o que foi incluído nessas versões."
services: active-directory
documentationcenter: 
author: karavar
manager: mtillman
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: b43eb5e78b70f38226e3e8cb53d1530d348c7c20
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2018
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: histórico de lançamento de versão
A equipe do Active Directory do Azure atualiza regularmente o Azure AD Connect Health com novos recursos e funcionalidades. Este artigo lista as versões e os recursos que foram lançados.

## <a name="december-2017"></a>Dezembro de 2017
**Atualização do agente:**

*   Agente do Azure AD Connect Health para AD DS (versão 3.0.145.0)
  1. Melhorias de disponibilidade do agente 
  2. Novos comandos de solução de problemas de agente foram adicionados
  3. Correções de bugs e aprimoramentos gerais
*   Agente do Azure AD Connect Health para AD FS (versão 3.0.145.0)
  1. Novos comandos de solução de problemas de agente foram adicionados
  2. Melhorias de disponibilidade do agente 
  3. Correções de bugs e aprimoramentos gerais
  
## <a name="october-2017"></a>Outubro de 2017
**Atualização do agente:**

 * O agente do Azure AD Connect Health for Sync (versão 3.0.129.0) lançado com a versão 1.1.649.0 do Azure AD Connect
<br></br> Corrigido um problema de compatibilidade entre o Azure AD Connect e o agente do Azure AD Connect Health para Sincronização. Esse problema afeta os clientes que estão executando a atualização in-loco do Azure AD Connect para a versão 1.1.647.0, mas, atualmente, têm o Agente de Integridade versão 3.0.127.0. Após a atualização, o Agente de Integridade não poderá mais enviar dados de integridade do Azure AD Connect Synchronization Service ao Azure AD Health Service. Com essa correção, o Agente de Integridade versão 3.0.129.0 será instalado durante a atualização in-loco do Azure AD Connect. O Agente de Integridade versão 3.0.129.0 não tem problema de compatibilidade com o Azure AD Connect versão 1.1.649.0.

## <a name="july-2017"></a>Julho de 2017
**Atualização do agente:**

*   Agente do Azure AD Connect Health para AD DS (versão 3.0.68.0)
  1. Correções de bugs e aprimoramentos gerais
  2. Suporte à nuvem soberana
*   Agente do Azure AD Connect Health para AD FS (versão 3.0.68.0)
  1. Correções de bugs e aprimoramentos gerais
  2. Suporte à nuvem soberana
* O agente do Azure AD Connect Health for Sync (versão 3.0.68.0) lançado com a versão 1.1.614.0 do Azure AD Connect
1. Suporte para a Nuvem do Microsoft Azure Governamental e Microsoft Cloud Germany

## <a name="april-2017"></a>Abril de 2017      
**Atualização do agente:**

*   Agente do Azure AD Connect Health para AD FS (versão 3.0.12.0)
  1. Correções de bugs e aprimoramentos gerais
*   Agente do Azure AD Connect Health para AD DS (versão 3.0.12.0)
  1. Aprimoramentos de upload dos contadores de desempenho
  2. Correções de bugs e aprimoramentos gerais

## <a name="october-2016"></a>Outubro de 2016
**Atualização do agente:**

* Agente do Azure AD Connect Health para AD FS \(versão 2.6.408.0\)
1. Aprimoramento da detecção de endereços IP do cliente nas solicitações de autenticação
2. Correções de bugs relacionados aos alertas
* Agente do Azure AD Connect Health para AD DS (versão 2.6.408.0)
1. Correções de bugs relacionadas aos alertas.
* O agente do Azure AD Connect Health for Sync (versão 2.6.353.0) lançado com a versão 1.1.281.0 do Azure AD Connect
1. Fornecer os dados necessários para os relatórios de erros de sincronização
2. Correções de bugs relacionados aos alertas

**Novos recursos de visualização:**

* Relatórios de erros de sincronização do Azure AD Connect

**Novos recursos:**

* Azure AD Connect Health para AD FS - o campo de endereço IP está disponível no relatório sobre os 50 principais usuários com o nome de usuário/senha inválidos.

## <a name="july-2016"></a>Julho de 2016
**Novos recursos de visualização:**

* [Azure AD Connect Health para AD DS](active-directory-aadconnect-health-adds.md).

## <a name="january-2016"></a>Janeiro de 2016
**Atualização do agente:**

* Agente do Azure AD Connect Health para AD FS (versão 2.6.91.1512)

**Novos recursos:**

* [Ferramenta de Teste de Conectividade para agentes do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Novembro de 2015
**Novos recursos:**

* Suporte ao [Controle de Acesso Baseado em Função](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)

**Novos recursos de visualização:**

* [Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md).

**Problemas corrigidos:**

* Correções de bug para erros exibidos durante os registros de agente.

## <a name="september-2015"></a>Setembro de 2015
**Novos recursos:**

* Relatório de senha de nome de usuário incorreto para o AD FS
* Suporte para configurar o Proxy HTTP Não Autenticado
* Suporte para configurar o agente no núcleo do Servidor
* Aprimoramentos em Alertas para o AD FS
* Aprimoramentos no Agente do Azure AD Connect Health Agent para AD FS em conectividade e no upload de dados.

**Problemas corrigidos:**

* Correções de bugs em informações de uso para tipos de Erro do AD FS.

## <a name="june-2015"></a>Junho de 2015
**Versão inicial do Azure AD Connect Health para AD FS e para o Proxy do AD FS.**

**Novos recursos:**

* Alertas para o monitoramento de servidores do AD FS e do Proxy do AD FS com notificações por email.
* Acesso fácil à topologia do AD FS e a padrões nos Contadores de Desempenho do AD FS.
* A tendência em solicitações bem-sucedidas de token em servidores do AD FS agrupadas por Aplicativos, Métodos de Autenticação, Local de Rede da Solicitação etc.
* Tendências em solicitações com falha em servidores do AD FS agrupadas por Aplicativos, Tipos de Erro etc.
* Implantação mais simples de agente usando credenciais de Administrador Global do AD do Azure.  

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Monitorar a infraestrutura de identidade local e os serviços de sincronização na nuvem](active-directory-aadconnect-health.md).

