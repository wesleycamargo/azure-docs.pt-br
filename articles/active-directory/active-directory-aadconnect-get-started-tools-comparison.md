<properties
	pageTitle="Azure AD Connect: comparação de ferramentas de integração de diretório | Microsoft Azure"
	description="Esta é a página que fornecerá as tabelas abrangentes que comparam as várias ferramentas de integração de diretório."
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
	ms.date="01/21/2016"
	ms.author="billmath"/>

# Comparação de ferramentas de integração de diretório

Ao longo dos anos, as ferramentas de integração de diretório cresceram e evoluíram. Este documento tem o objetivo de ajudar a fornecer uma visão consolidada dessas ferramentas e uma comparação dos recursos que estão disponíveis em cada uma.

>[AZURE.NOTE] O Azure AD Connect incorpora componentes e funcionalidades lançados anteriormente como Dirsync e AAD Sync. Essas ferramentas não são mais lançadas individualmente e todas as melhorias futuras serão incluídas nas atualizações do Azure AD Connect, para que você sempre saiba onde obter a funcionalidade mais atual.
>
>Atualmente, o Dirsync ainda tem suporte, mas em algum momento no futuro ele será substituído. Depois da substituição, só terá suporte por um período de tempo. Após esse período de tempo, o suporte para o Dirsync encerrará.


Use a seguinte chave para cada uma das tabelas.

● = Disponível agora</br>FR = versão futura</br>PP = visualização pública</br>


## Local para a sincronização de nuvem

| Recurso | Conexão do Active Directory do Azure | Serviços de sincronização do Active Directory do Azure (sincronização do AAD) | Ferramenta de sincronização do Active Directory do Azure (DirSync)| O Forefront Identity Manager 2010 R2 (FIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Conectar-se a única floresta do AD local | ● | ● | ● | ● |
| Conectar-se a várias florestas do AD local |● | ● | | ● |
| Conectar-se a várias organizações de Exchange local | ● | | | |
| Conectar-se a um único diretório LDAP local | FR | | | ● |
| Conectar-se a vários diretórios LDAP local |FR | | | ● |
| Conectar-se a diretórios AD locais e LDAP locais |FR | | | ● |
| Conecte-se aos sistemas personalizados (por exemplo, SQL, Oracle, MySQL, etc.) | FR | | | ● |
| Sincronize atributos definido pelo cliente (extensões de diretório) | ● | | | |

## Nuvem para sincronização no local

| Recurso | Conexão do Active Directory do Azure | Serviços de sincronização do Active Directory do Azure | Ferramenta de sincronização do Active Directory do Azure (DirSync) | O Forefront Identity Manager 2010 R2 (FIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Write-back de dispositivos | ● | | ● | |
| Write-back de atributo (para implantação híbrida do Exchange) | ● | ● | ● | ● |
| Write-back de objetos de usuários e grupos | ●| | | |
| Write-back de senhas (de redefinição de senha de autoatendimento (SSPR) e alteração de senha) | ● | ● | | |



## Suporte ao recurso de autenticação

| Recurso | Conexão do Active Directory do Azure | Serviços de sincronização do Active Directory do Azure | Ferramenta de sincronização do Active Directory do Azure (DirSync) | O Forefront Identity Manager 2010 R2 (FIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Sincronização de senha para a floresta única do AD local | ● | ● | ● | |
| Sincronização de senha para várias florestas do AD local | ●| ● | | |
| Logon único com federação | ● | ● | ● | ● |
| Write-back de senhas (de alteração SSPR e senha) |● | ● | | |



## Instalação e configuração

| Recurso | Conexão do Active Directory do Azure | Serviços de sincronização do Active Directory do Azure | Ferramenta de sincronização do Active Directory do Azure (DirSync) | O Forefront Identity Manager 2010 R2 (FIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Oferece suporte à instalação em um controlador de domínio | ● | ● | ● | |
| Oferece suporte à instalação usando o SQL Express | ● | ● | ● | |
| Atualização fácil de DirSync |● | | | |
| Idiomas do Windows Server de localização | FR | ● | ● | |
| Suporte para Windows Server 2008 e Windows Server 2008 R2 | ● para sincronização, não para federação| ● | ● | ● |
| Suporte para o Windows Server 2012 e Windows Server 2012 R2 | ● | ● | ● | Somente 2012 |


## Filtragem e configuração

Recurso | Conexão do Active Directory do Azure | Serviços de sincronização do Active Directory do Azure | Ferramenta de sincronização do Active Directory do Azure (DirSync) | O Forefront Identity Manager 2010 R2 (FIM)  
:-------- |:--------:|:--------:|:--------:|:--------:|
Filtrar em domínios e unidades organizacionais | ● | ● | ● | ●  
Filtrar os valores de atributo de objetos | ● | ● | ● | ●
Permitir que um conjunto mínimo de atributos seja sincronizado (MinSync) | ● | ● | |
Permitir que os modelos de serviço diferentes a sejam aplicados para fluxos de atributo |● | ● | |
Permitir a remoção de atributos do fluxo do AD para o AD do Azure | ● | ● | |  
Permitir a personalização avançada para fluxos de atributo | ● | ● | | ●  

## Próximas etapas
Saiba mais sobre a [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->