<properties
	pageTitle="Terminologia do AD do Azure | Microsoft Azure"
	description="Termos e definições relacionados ao Active Directory do Azure."
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
	ms.date="12/01/2015"
	ms.author="curtand"/>

# Terminologia do AD do Azure

O Active Directory do Microsoft Azure (AD do Azure) tem um conjunto exclusivo de terminologias relacionadas aos cenários de nuvem, híbrido e local. A tabela a seguir define esses termos para fornecer uma compreensão básica de como eles são usados.

 Termo | Definição
------------- | -------------
Verificação de segurança adicional | Uma configuração de segurança que o administrador global pode definir em uma conta de usuário no diretório da organização para exigir que ambas uma senha do usuário e uma resposta de seu telefone sejam usadas para verificar a identidade para o sistema de autenticação do Active Directory do Azure.
Active Directory do Azure | O serviço de identidade no Azure que fornece recursos de controle de acesso e gerenciamento de identidade por meio de uma API baseada em REST.
Controle de Acesso do Active Directory do Azure | O serviço do Azure que fornece autenticação federada e autorização orientada por regras, baseada em declarações para serviços Web REST.
Sistema de autenticação do Active Directory do Azure | Serviço de identidade da Microsoft na nuvem usado para autenticar e autorizar contas corporativas ou de estudante.
Graph do Active Directory do Azure | Uma funcionalidade do Active Directory do Azure que acessa objetos de usuário, grupo e função dentro de um gráfico corporativo social para destacar facilmente relações e informações de usuário.
Módulo do Active Directory do Azure para Windows PowerShell | Um grupo de cmdlets usados para administrar o Active Directory do Azure. Você pode usar esses cmdlets para gerenciar usuários, grupos, domínios, assinaturas de serviço de nuvem, licenças, sincronização de diretórios, logon único e muito mais.
Conexão do Active Directory do Azure | O assistente de Conexão do Active Directory do Azure é a única ferramenta e experiência orientada para conectar seus diretórios locais ao Active Directory do Azure. O assistente implanta e configura todos os componentes necessários para obter a integração e o funcionamento do seu diretório, incluindo os serviços de sincronização, a sincronização de senha ou os Serviços de Federação do Active Directory (AD FS), além dos pré-requisitos, como o módulo Azure AD PowerShell.
Ferramenta de Sincronização do Active Directory do Azure | O aplicativo que fornece sincronização unidirecional de objetos do serviço Active Directory local de uma empresa com o Active Directory do Azure.
Integração de diretórios | Um recurso do Active Directory do Azure que você pode configurar para melhorar a experiência administrativa associada à manutenção de identidades em seu diretório local e seu diretório de nuvem. Os cenários de integração de diretórios incluem sincronização de diretório e sincronização de diretório com logon único.
Sincronização de diretório | Usado para sincronizar objetos de diretório locais (usuários, grupos, contatos) com a nuvem para ajudar a reduzir a sobrecarga administrativa. A sincronização de diretório também é chamada de sincronização de diretório no portal do AD do Azure e no portal clássico do Azure. Depois que a sincronização de diretório foi configurada, os administradores podem provisionar objetos de diretório em um Active Directory local para uma instância do AD do Azure.
Assistente de Conexão do Microsoft Online Services | O Assistente de Conexão é um aplicativo instalado em um computador cliente que permite que um usuário entre uma vez nesse computador e, em seguida, acesse os serviços por qualquer número de vezes durante a sessão de entrada. Sem o Assistente de Conexão, os usuários finais deverão fornecer um nome e senha cada vez que tentarem acessar um serviço. O Assistente de Conexão não deve ser confundido com o logon único, que é um recurso de integração de diretórios do Active Directory do Azure que pode ser implantado para aproveitar as credenciais corporativas locais existentes de um usuário para acessar diretamente os serviços de nuvem da Microsoft.
Multi-Factor Authentication (também conhecida como autenticação de dois fatores ou 2FA) | A Multi-Factor Authentication adiciona uma segunda camada crítica de segurança para transações e entradas de usuário. Quando você habilita a autenticação multifator para uma conta de usuário no AD do Azure, esse usuário precisa então usar o seu telefone, além de suas credenciais de senha padrão, como o método de verificação de segurança adicional cada vez que ele precisa entrar e usar qualquer um dos serviços de nuvem Microsoft assinados por sua organização.
Logon único | Usado para fornecer aos usuários uma experiência de autenticação mais dinâmica quando eles acessam serviços de nuvem da Microsoft enquanto os usuários estão com logon ativo na rede corporativa. Para configurar o logon único, as organizações precisam implantar um serviço de token de segurança local. Depois que o logon único tiver sido definido, os usuários poderão usar suas credenciais corporativas do Active Directory (nome de usuário e senha) para acessar os serviços na nuvem e seus recursos locais existentes.
Id de Usuário | Uma ID de usuário é um identificador exclusivo fornecido pelo usuário na página de entrada para acessar os serviços de nuvem da Microsoft que sua organização tenha assinado.
Conta corporativa ou de estudante | Uma conta de usuário atribuída por uma organização (empresa, escola, sem fins lucrativos) a um de seus constituintes (um funcionário, estudante, cliente) que fornece acesso por entrada a um ou mais dos serviços de nuvem da Microsoft assinados pela organização, como o Office 365 ou Azure. Essas contas são armazenadas no diretório do AD do Azure da organização e normalmente são excluídas quando o usuário deixa a organização. Contas corporativas ou de estudante diferem das contas da Microsoft por que são criadas e gerenciadas por administradores na organização, e não pelo usuário.

## O que vem a seguir
- [Inscrever-se no Azure como uma organização](sign-up-organization.md)
- [Como as assinaturas do Azure estão associadas ao AD do Azure](active-directory-how-subscriptions-associated-directory.md)
- [Restrições e limites de serviço do AD do Azure](active-directory-service-limits-restrictions.md)

<!---HONumber=AcomDC_1203_2015-->