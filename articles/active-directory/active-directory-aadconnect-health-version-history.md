<properties 
	pageTitle="Histórico de versão do Azure AD Connect Health" 
	description="Este documento descreve as versões do Azure AD Connect Health e o que foi incluído nessas versões." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2016" 
	ms.author="billmath"/>

# Azure AD Connect Health: histórico de lançamento de versão

A equipe do Active Directory do Azure atualiza regularmente o Azure AD Connect Health com novos recursos e funcionalidades.

Este artigo destina-se para ajudar você a controlar as versões que foram lançadas.


## Julho de 2016

**Novos recursos de visualização:**

- [Azure AD Connect Health para AD DS](active-directory-aadconnect-health-adds.md).


## Janeiro de 2016


**Atualização do agente:**

- Agente do Azure AD Connect Health para AD FS (versão 2.6.91.1512)


**Novos recursos:**

- [Ferramenta de Teste de Conectividade para agentes do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)


## Novembro de 2015


**Novos recursos:**

- Suporte ao [Controle de Acesso Baseado em Função](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)


**Novos recursos de visualização:**

- [Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md).

**Problemas corrigidos:**

- Correções de bug para erros exibidos durante os registros de agente.

## Setembro de 2015

**Novos recursos:**

- Relatório de senha de nome de usuário incorreto para o AD FS
- Suporte para configurar o Proxy HTTP Não Autenticado
- Suporte para configurar o agente no núcleo do Servidor
- Aprimoramentos em Alertas para o AD FS
- Aprimoramentos no Agente do Azure AD Connect Health Agent para AD FS em conectividade e no upload de dados.


**Problemas corrigidos:**

- Correções de bugs em informações de uso para tipos de Erro do AD FS.


## Junho de 2015

**Versão inicial do Azure AD Connect Health para AD FS e para o Proxy do AD FS.**

**Novos recursos:**

- Alertas para o monitoramento de servidores do AD FS e do Proxy do AD FS com notificações por email.
- Acesso fácil à topologia do AD FS e a padrões nos Contadores de Desempenho do AD FS.
- A tendência em solicitações bem-sucedidas de token em servidores do AD FS agrupadas por Aplicativos, Métodos de Autenticação, Local de Rede da Solicitação etc.
- Tendências em solicitações com falha em servidores do AD FS agrupadas por Aplicativos, Tipos de Erro etc.
- Implantação mais simples de agente usando credenciais de Administrador Global do AD do Azure.




## Próximas etapas
Saiba mais sobre [Monitorar a infraestrutura de identidade local e os serviços de sincronização na nuvem](active-directory-aadconnect-health.md).

<!---HONumber=AcomDC_0720_2016-->