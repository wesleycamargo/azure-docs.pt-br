<properties
	pageTitle="Azure AD Connect e federação | Microsoft Azure"
	description="Esta página é o local central para toda a documentação relativa às operações do AD FS usando o Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="anandy"/>


# Azure AD Connect e federação

O Azure AD Connect permite configurar a federação com o AD FS e o Azure AD locais. Com o logon federado, você pode habilitar os usuários a acessarem serviços baseados no Azure AD com suas senhas locais e, enquanto estão na rede corporativa, sem precisar digitar suas senhas novamente. A opção de federação com o AD FS permite que você implante um novo AD FS ou especifique um existente em um farm do Windows Server 2012 R2.

Este tópico é a base das informações sobre funcionalidades relacionadas à Federação para o Azure AD Connect e relaciona links para todos os outros tópicos relacionados a ela. Para obter links para o Azure AD Connect, confira Integrando suas identidades locais ao Azure Active Directory.

## Azure AD Connect - tópicos sobre federação

| Tópico | O que ele abrange e quando deve ser lido |
|:------|:-----------|
| **Opções de entrada de usuário do Azure AD Connect** ||
| [Noções básicas sobre as opções de entrada do usuário](active-directory-aadconnect-user-signin.md) | Descrição das diferentes opções de entrada do usuário e como elas afetam a experiência de entrada do usuário no Azure |
| **Instalação do AD FS usando o Azure AD Connect**||
| [Pré-requisitos](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Pré-requisitos para uma instalação bem-sucedida do AD FS usando o Azure AD Connect|
| [Configurar o farm do AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | Como instalar um novo farm do AD FS usando o Azure AD Connect |
| **Modificando a configuração do AD FS** | |
| [Reparando a confiança](active-directory-aadconnect-federation-management.md#reparing-the-trust) | Como reparar a confiança atual entre o AD FS local e o Office 365 / Azure |
| [Adicionando um novo servidor do AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | Expandindo o farm do AD FS com um servidor do AD FS adicional após a instalação inicial |
| [Adicionando um novo servidor WAP do AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | Expandindo o farm do AD FS com um servidor WAP adicional após a instalação inicial |
| [Adicionar um novo domínio federado](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Adicionar outro domínio para ser federado com o Azure AD |
|**Tarefas pós-instalação**||
| [Adicionar ilustração/logotipo personalizado da empresa](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| Modificar a experiência de entrada especificando o logotipo personalizado que será exibido na página de entrada do AD FS |
| [Adicionar a descrição de entrada](active-directory-aadconnect-federation-management.md#add-sign-in-description) | Alterando a descrição de entrada na página de entrada do AD FS | 
| [Modificando as regras de declaração do AD FS](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Modificar/adicionar regras de declaração do AD FS correspondentes à configuração de sincronização do Azure AD Connect |


## Recursos adicionais

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)
* [Implantação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md)

<!---HONumber=AcomDC_0720_2016-->