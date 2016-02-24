<properties
	pageTitle="Políticas e restrições de senha do Active Directory do Azure | Microsoft Azure"
	description="Descreve as políticas que se aplicam a senhas no Active Directory do Azure, incluindo caracteres permitidos, comprimento e expiração"
  services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="curtand"/>


# Políticas e restrições de senha do Active Directory do Azure

Este artigo descreve as políticas de senha e requisitos de complexidade associados a contas de usuário armazenadas no diretório do AD do Azure.

## Políticas UserPrincipalName que se aplicam a todas as contas de usuário

Cada conta de usuário que precisa entrar no sistema de autenticação do AD do Azure deve ter um valor de atributo UPN (nome principal do usuário) exclusivo associado a essa conta. A tabela a seguir descreve as políticas que se aplicam a contas de usuário de origem do Active Directory no local (sincronizadas na nuvem) e a contas de usuário somente em nuvem.

| Propriedade | Requisitos de UserPrincipalName |
|   ----------------------- |   ----------------------- |
| Caracteres permitidos | <ul> <li>A – Z</li> <li>a -z </li><li>0 – 9</li> <li> . - \_ ! # ^ ~</li></ul> |
| Caracteres não permitidos | <ul> <li>@</li> <li>Não pode conter um caractere de ponto '.' imediatamente antes do símbolo ' @'</li></ul> |
| Restrições de comprimento | <ul> <li>O comprimento total não deve exceder 113 caracteres</li><li>64 caracteres antes do símbolo ' @'</li><li>48 caracteres depois do símbolo '@'</li></ul>

## Políticas de senha que se aplicam somente a contas de usuário na nuvem

A tabela a seguir descreve as configurações de política de senha disponíveis que podem ser aplicadas a contas de usuário que são criadas e gerenciadas no AD do Azure.

| Propriedade | Requisitos |
|   ----------------------- |   ----------------------- |
| Caracteres permitidos | <ul><li>A – Z</li><li>a -z </li><li>0 – 9</li> <li>@ # $ % ^ & * - \_ ! + = [ ] { } | \\ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Caracteres não permitidos | <ul><li>Caracteres Unicode</li><li>Espaços</li><li>espaços</li><li> **Somente senhas fortes**: não pode conter um caractere de ponto '.' imediatamente antes do símbolo ' @'</li></ul> |
| Restrições de senha | <ul><li>Mínimo de 8 caracteres e máximo de 16 caracteres</li><li>**Somente senhas fortes**: necessário 3 de 4 dos seguintes requisitos:<ul><li>Caracteres minúsculos</li><li>Caracteres maiúsculos</li><li>Números (0-9)</li><li>Símbolos (consulte as restrições de senha acima)</li></ul></li></ul> |
| Tempo de expiração da senha | <ul><li>Valor padrão: **90** dias </li><li>O valor é configurável usando o cmdlet Set-MsolPasswordPolicy do Módulo do Active Directory do Azure para Windows PowerShell.</li></ul> |
| Notificação de expiração de senha | <ul><li>Valor padrão: **14** dias (antes da expiração da senha)</li><li>O valor é configurável usando o cmdlet Set-MsolPasswordPolicy.</li></ul> |
| Expiração de senha | <ul><li>Valor padrão: **falso** dias (indica que a expiração da senha está habilitada) </li><li>O valor pode ser configurado para contas de usuário individuais usando o cmdlet Set-MsolUser. </li></ul> |
| Histórico de senha | A última senha não pode ser usada novamente. |
| Duração do histórico de senha | Para sempre |
| Bloqueio de conta | Após 10 tentativas malsucedidas de entrar (senha incorreta), o usuário será bloqueado por um minuto. Mais tentativas de entrar incorretas farão com que o usuário seja bloqueado por durações cada vez maiores. |


## Próximas etapas

* [Gerenciar suas senhas de qualquer lugar](active-directory-passwords.md)
* [Como funciona o gerenciamento de senhas](active-directory-passwords-how-it-works.md)
* [Introdução ao gerenciamento de senhas](active-directory-passwords-getting-started.md)
* [Personalizar o gerenciamento de senhas](active-directory-passwords-customize.md)
* [Práticas recomendadas de gerenciamento de senhas](active-directory-passwords-best-practices.md)
* [Como obter percepções operacionais com relatórios de gerenciamento de senhas](active-directory-passwords-get-insights.md)
* [Perguntas frequentes sobre Gerenciamento de Senhas](active-directory-passwords-faq.md)
* [Solucionar problemas do Gerenciamento de Senhas](active-directory-passwords-troubleshoot.md)
* [Saiba mais](active-directory-passwords-learn-more.md)

<!---HONumber=AcomDC_0114_2016-->