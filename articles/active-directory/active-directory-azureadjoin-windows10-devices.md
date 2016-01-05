<properties 
	pageTitle="Usando dispositivos com Windows 10 no local de trabalho | Microsoft Azure" 
	description="Fornece um instantâneo dos usuários, dos recursos e da TI, realçando as diferentes maneiras em que um dispositivo pode ser provisionado e usado em uma empresa com o Windows 10." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="femila"/>

# Usando dispositivos com Windows 10 no local de trabalho

O Windows 10 oferece três modelos para que as organizações possam permitir o acesso aos recursos de trabalho pelos usuários de forma segura e conveniente.

1. **Junção do AD do Azure**, novo no Windows 10, uma experiência de provisionamento de trabalho de autoatendimento direcionada aos funcionários que acessa basicamente recursos na nuvem, como o Office 365.
1. **Ingresso no domínio**, melhor no Windows 10 quando conectado ao AD do Azure, para organizações com grandes investimentos em aplicativos e recursos locais.
1. **Uma nova experiência BYOD simplificada** que permite aos usuários adicionar uma conta corporativa ao Windows para que possam acessar facilmente os recursos de trabalho em dispositivos pessoais.

A tabela abaixo apresenta um instantâneo dos usuários, dos recursos e da TI, realçando as diferentes maneiras em que um dispositivo pode ser provisionado e usado em uma empresa com o Windows 10:

| | Ingresso no domínio | Ingresso no AD do Azure | Dispositivo pessoal |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Entrada em dispositivo com Windows usando contas corporativas | Sim | Sim | Não |
| SSO de usuário para aplicativos Office365 e Azure AD, [1] Logon Único ou a capacidade de acessar os recursos da empresa sem inserir as credenciais novamente depois de entrar uma vez. | Sim | Sim | Sim |
| SSO do usuário para aplicativos Kerberos/NTLM | Sim | Limitado | Por meio de VPN |
| Autenticação forte e entrada conveniente para a conta corporativa com o Microsoft Passport e o Windows Hello | Sim | Sim | Sim |
| Acesso à Windows Store empresarial usando conta corporativa (sem a exigência de conta da Microsoft). | Sim | Sim | Sim |
| Roaming de configurações de usuário entre dispositivos em conformidade com a empresa usando a conta corporativa | Sim | Sim | Sim |
| Restringir o acesso a aplicativos organizacionais de dispositivos em conformidade com políticas de dispositivo organizacional | Sim | Sim | Sim |
| Provisionamento de autoatendimento pelo usuário de dispositivos para o trabalho de qualquer lugar | Não | Sim | Sim |
| Capacidade de gerenciar dispositivos | Sim, via GP/SCCM | Sim | Sim |

##Dispositivos corporativos: ingresso no AD do Azure e ingresso no domínio

O Windows 10 oferece dois modelos para dispositivos corporativos para acessar recursos de trabalho:

- Ingresso no AD do Azure
- Ingresso no domínio.

 Ambas as opções são válidas dependendo dos requisitos e das necessidades da organização. Em alguns casos, as organizações podem se beneficiar habilitando os dois métodos de implantação.

## Quando usar o ingresso no Active Directory do Azure?

O ingresso no AD do Azure é uma nova experiência de provisionamento de autoatendimento no Windows 10. Ele é direcionado para funcionários que basicamente acessem recursos de trabalho na nuvem, como o Office 365. É uma maneira simples de configurar PCs, tablets e telefones para a empresa. Os dispositivos são gerenciados por MDM usando controles consistentes entre plataformas Windows.

**Use o ingresso no Azure AD por qualquer um destes motivos**:

1.	Você deseja habilitar o provisionamento de autoatendimento de dispositivos para trabalhadores em outros lugares.
2.	Você fornece aos usuários dispositivos móveis corporativos, como tablets e telefones.
3.	Você deseja gerenciar um conjunto de usuários no AD do Azure em vez de no AD, como trabalhadores temporários, prestadores de serviços ou alunos.
4.	Você deseja fornecer recursos de ingresso para trabalhadores em escritórios remotos com infraestrutura local limitada.
5.	Você não tem AD local.

Algumas organizações usam o ingresso no AD do Azure como a principal maneira de implantar dispositivos corporativos, especialmente à medida que eles estão migrando a maioria ou todos os seus recursos para a nuvem. As organizações híbridas com o AD e o Azure AD podem optar por implantar um método ou outro, dependendo do usuário ou do departamento. Escolas e universidades podem gerenciar a equipe no AD e os alunos no AD do Azure, ou algumas empresas talvez prefiram escritórios remotos ou o departamento de vendas no AD do Azure. Tanto o Ingresso no AD do Azure quanto o Ingresso no domínio são métodos que podem ser utilizados em uma organização híbrida. O ingresso no AD do Azure pode ser um ótimo complemento para a implantação de dispositivos em um ambiente de trabalho.

**Se a maior parte do acesso aos recursos da empresa é por meio da nuvem, uma organização pode ter benefícios adicionais**:

- Você poderá remover dependências na infraestrutura de identidade local.
- Você pode simplificar o seu modelo de implantação de dispositivos afastando-se das soluções de geração de imagens, permitindo a configuração de autoatendimento.
- Você pode usar o MDM para gerenciar todos os seus dispositivos em diferentes plataformas.

Para saber mais sobre o Ingresso no AD do Azure, confira [Estendendo recursos de nuvem para dispositivos com Windows 10 usando o Ingresso no Active Directory do Azure](active-directory-azureadjoin-overview.md).

## Quando usar o Ingresso no domínio (ou continuar usando)

O ingresso no domínio é a maneira tradicional usada pelas organizações para conectar dispositivos de trabalho pelo menos nos últimos 15 anos. Ele permitiu que usuários entrassem em seus dispositivos usando suas contas corporativas do AD e que o departamento de TI gerenciasse esses dispositivos de maneira total e centralizada. As organizações geralmente dependem de métodos de geração de imagens para provisionar dispositivos e geralmente usam o SCCM (System Center Configuration Manager) ou a política de grupo para gerenciá-los.

**Você poderia usar o ingresso no domínio da sua empresa por qualquer um desses motivos**:

- Você tem aplicativos Win32 implantados nesses dispositivos que usam o Kerberos/NTLM.
- Você precisa de GP ou SCCM/DCM para gerenciar dispositivos.
- Você deseja continuar a usar soluções de geração de imagens para configurar dispositivos de seus funcionários.

**O ingresso no domínio do Windows 10 também lhe oferece os seguintes benefícios depois de estar conectado ao AD do Azure**:

- Autenticação forte associada ao dispositivo e entrada conveniente para a conta corporativa com o Microsoft Passport e o Windows Hello.
- Acesso à Windows Store empresarial usando contas corporativas (sem a exigência de conta da Microsoft).
- Roaming de configurações de usuário entre dispositivos em conformidade com a empresa usando a conta corporativa (sem a exigência de conta da Microsoft).
- Capacidade de restringir o acesso a aplicativos organizacionais de dispositivos em conformidade com as políticas de dispositivo organizacionais.
- Para saber mais sobre o Ingresso no AD do Azure, confira [Estendendo recursos de nuvem para dispositivos com Windows 10 usando o Ingresso no Active Directory do Azure](active-directory-azureadjoin-overview.md).

##Habilitando dispositivos pessoais para trabalhar com contas corporativas

Para oferecer suporte a BYOD na empresa, o Windows 10 dá ao usuário a capacidade de "adicionar uma conta corporativa ou de estudante" a seu computador, tablet ou telefone. A adição de uma conta corporativa registra os dispositivos no AD do Azure e, opcionalmente, registra o dispositivo no MDM configurado pela organização para os dispositivos. O diretório mostrará esses dispositivos com o status 'Registrado' vs. 'Ingressado no AD do Azure'. O departamento de TI pode aplicar políticas diferentes com base nessas informações, com mais informalidade em um dispositivo pessoal do que em um dispositivo corporativo, se desejar.

Os usuários podem adicionar uma conta corporativa a seus dispositivos pessoais de forma muito conveniente: ao acessar um aplicativo corporativo pela primeira vez, ou manualmente usando o aplicativo Configurações. Essa conta corporativa fornecerá SSO aos recursos organizacionais.

Para saber mais sobre o Ingresso no AD do Azure, confira [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## Habilitando o ingresso no domínio ou no AD do Azure 

Todos os métodos descritos acima (Ingresso no domínio, Ingresso no AD do Azure e Adicionar conta corporativa) têm pontos de entrada na experiência do usuário do Windows 10. No entanto, todos requerem que o departamento de TI habilite a funcionalidade na infraestrutura para que ela funcione.

##Ingresso no AD do Azure

Para implantar o ingresso no AD do Azure para um conjunto de usuários, você precisa do seguinte:
---------------------------------------------------------------------------
- Ter uma assinatura do AD do Azure.
- Mais recursos exigirão uma assinatura do Azure AD Premium, como a inscrição automática do MDM.
- Ter uma assinatura válida do Azure AD Premium.
- Ter um MDM (assinatura Intune ou o MDM para Office365 ou de qualquer um dos fornecedores terceirizados de MDMs que integram com o AD do Azure. Confira a seção de Perguntas frequentes para obter detalhes)
- Se você for híbrido, é altamente recomendável fazer o seguinte:
- Implantar o Azure AD Connect para estender o diretório local ao AD do Azure.

##Ingresso no domínio

O ingresso no domínio continua a funcionar como sempre. No entanto, para obter os benefícios do AD do Azure, você precisará do seguinte:

- Ter uma assinatura do AD do Azure.
- Implantar o Azure AD Connect para estender o diretório local ao AD do Azure.
- Definir a política para conectar dispositivos ingressados no domínio ao AD do Azure.
- Para saber mais sobre o Ingresso no domínio no Windows 10, confira <link-to-DJ-in-Win10-deployment-guide>.
- Para acesso condicional, você pode criar uma política que permite o acesso a dispositivos 'ingressados no domínio'. Para habilitar regras que exijam dispositivos compatíveis, você precisa do seguinte:
- Versão 1509 para visualização técnica do System Center Configuration Manager (confira a documentação do TechNet e as postagens no blog).

##BYOD e Adicionar conta corporativa

Para habilitar o BYOD com contas corporativas, você precisa do seguinte:

- Ter uma assinatura do AD do Azure.
- Mais recursos exigirão uma assinatura do Azure AD Premium, como a inscrição automática do MDM.
- Ter uma assinatura válida do Azure AD Premium.
- Ter um MDM (assinatura Intune ou o MDM para Office365 ou de qualquer um dos fornecedores terceirizados de MDMs que integram com o AD do Azure. Confira a seção de Perguntas frequentes para obter detalhes)

##Microsoft Passport

Para habilitar o Microsoft Passport, você precisará também do seguinte:

- Infraestrutura de PKI para suporte a autenticação baseada em certificado usando o Microsoft Passport.
- Assinatura do Intune para suporte a autenticação baseada em certificado usando o Microsoft Passport para ingresso no AD do Azure AD e Contas Corporativas.
- Versão 1509 para visualização técnica do System Center Configuration Manager (confira a documentação do TechNet e as postagens no blog) para suporte de autenticação baseada em certificado usando o Microsoft Passport para Ingresso no Domínio.
- Definir a política para habilitar o Microsoft Passport na organização.

Como alternativa a ter uma infraestrutura PKI, você pode habilitar o Microsoft Passport baseado em chave usando o seguinte procedimento:

- Implante controladores de domínio do Windows Server 2016 ‘Visualização de Produção 1’ (não há necessidade de domínio ou o nível funcional de floresta; alguns controladores de domínio de redundância servindo cada site do AD serão suficientes).
- Definir a política para habilitar o Microsoft Passport na organização.
- Para saber mais sobre o Microsoft Passport e o Windows Hello no Windows 10, confira <link-to-MS-Passport-and-Windows-Hello-document>.

## Perguntas frequentes

###Quais produtos de fornecedores de MDM terceirizados se integram ao AD do Azure?

Os seguintes produtos de fornecedores se integram com o AD do Azure para o registro unificado e o acesso condicionado no Windows 10:

- AirWatch da VMware
- Citrix Xenmobile
- Lightspeed Mobile Manager
- MDM SOTI local

###E quanto a Ingressar no local de trabalho no Windows 10?
O ingresso no local de trabalho no Windows 8.1 foi usado para habilitar o BYOD. No Windows 10, o BYOD é habilitado usando Adicionar conta corporativa, conforme explicado anteriormente neste documento. Para organizações que não integram o MDM ao AD do Azure, os usuários podem registrar o dispositivo no gerenciamento manualmente em **Configurações** > **Contas** > **Acesso corporativo**.

###O usuário pode conectar sua conta da Microsoft (MSA) à Conta de domínio no Windows 10?
Não no Windows 10. No Windows 8.1, os usuários de dispositivos ingressados no domínio poderiam "conectar" seu MSA (por exemplo, Hotmail, Live, Outlook, XBox, etc.) à sua conta de domínio para permitir determinadas experiências como o SSO para serviços Live, o uso da Windows Store e a mobilidade das configurações do usuário entre vários dispositivos. No Windows 10, a funcionalidade Conectar MSA foi desativada. O usuário pode adicionar um ou mais MSAs como contas adicionais para habilitar SSO para serviços de consumidor, como a Windows Store. Isso é feito em **Configurações** > **Contas** > **Sua conta**.

Os usuários atualizando de dispositivos ingressados no domínio do Windows 8.1 que tenham sua MSA conectada automaticamente terão essa MSA adicionada à lista de contas adicionais usadas por eles.


## Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->