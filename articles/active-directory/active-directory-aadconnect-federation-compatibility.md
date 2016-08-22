<properties
	pageTitle="Lista de compatibilidade de federação do AD do Azure"
	description="Esta página contém provedores de identidade de terceiros que podem ser usados para implementar o logon único."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="billmath"/>

# Lista de compatibilidade de federação do AD do Azure
O Azure Active Directory fornece logon único e segurança aprimorada de acesso ao aplicativo para o Office 365 e outros serviços do Microsoft Online para implementações híbridas e apenas de nuvem, sem a necessidade de qualquer solução de terceiros. O Office 365, como a maioria dos serviços online da Microsoft, é integrado ao Azure Active Directory para autorização, autenticação e serviços de diretório. O Azure Active Directory também fornece logon único a milhares de aplicativos SaaS e aplicativos Web locais. Consulte a galeria de aplicativos do Azure Active Directory para aplicativos SaaS com suporte.

Para organizações que investiram em soluções de federação de terceiros, este tópico contém orientações sobre como configurar o logon único para seus usuários do Active Directory do Windows Server com os serviços do Microsoft Online usando provedores de identidade de terceiros na "Lista de compatibilidade de federação do Azure Active Directory" abaixo.


![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
O [Oxford Computer Group](http://oxfordcomputergroup.com/), um terceiro, em nome da Microsoft, testou essas experiências de logon único usando provedores de identidade de terceiros com base em um conjunto de casos de uso comuns com o Azure Active Directory.

Para obter informações sobre como você pode obter o provedor de identidade do terceiro listado aqui, contate o Oxford Computer Group em [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

>[AZURE.IMPORTANT] O Oxford Computer Group testou apenas a funcionalidade de federação desses cenários de logon único. O Oxford Computer Group não realizou qualquer teste de sincronização, autenticação de dois fatores, etc., que são componentes desses cenários de logon único.

>O uso da Entrada com ID Alternativa ao UPN também não foi testado neste programa.



- [Active Directory do Azure](#azure-active-directory)
- [Optimal IDM Virtual Identity Server Federation Services](#optimal-idm-virtual-identity-server-federation-services)
- [PingFederate 6.11](#pingfederate-611)
- [PingFederate 7.2](#pingfederate-72)
- [PingFederate 8.x](#pingfederate-8x)
- [Centrify](#centrify)
- [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622)
- [SecureAuth IdP 7.2.0](#secureauth-idp-720)
- [CA SiteMinder 12.52](#ca-siteminder-1252)
- [RadiantOne CFS 3.0](#radiantone-cfs-30)
- [Okta](#okta)
- [OneLogin](#onelogin)
- [NetIQ Access Manager 4.0.1](#netiq-access-manager-401)
- [BIG-IP com Access Policy Manager BIG-IP ver. 11.3x – 11.6x](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x)
- [VMware Workspace Portal versão 2.1](#vmware-workspace-portal-version-21)
- [Sign&go 5.3](#signampgo-53)
- [IceWall Federation versão 3.0](#icewall-federation-version-30)
- [CA Secure Cloud](#ca-secure-cloud)
- [Dell One Identity Cloud Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71)
- [AuthAnvil Single Sign On 4.5](#authavil-single-sign-on-45)

>[AZURE.IMPORTANT] Como esses são produtos de terceiros, a Microsoft não fornece suporte à implantação, configuração, solução de problemas, práticas recomendadas etc. problemas e questões relacionadas a esses provedores de identidade. Para obter suporte e consultar perguntas sobre esses provedores de identidade, entre em contato diretamente com os terceiros.

>Esses provedores de identidade de terceiros foram testados quanto à interoperabilidade com os serviços de nuvem da Microsoft usando apenas os protocolos WS-Federation e WS-Trust. Os testes não incluíram a utilização do protocolo SAML.

## Azure Active Directory 
O Azure Active Directory pode autenticar usuários ao estabelecer uma federação com seu Active Directory local ou sem um servidor de federação local com o uso de sincronização de senha.

Veja a seguir a matriz de suporte de cenário para esta experiência de logon:


| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Nenhum|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|
|Aplicativos modernos com ADAL, como Office 2016| Suportado|Nenhum|

Para saber mais sobre como usar o Azure Active Directory com o AD FS, confira [ADFS (Serviços de Federação do Active Directory)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)

Para saber mais sobre como usar o Azure Active Directory com sincronização de senha, confira [Azure AD Connect](active-directory-aadconnect.md).


## Optimal IDM Virtual Identity Server Federation Services 
O Optimal IDM Virtual Identity Server Federation Services pode autenticar usuários que residem nos Active Directories locais do cliente.

Veja a seguir a matriz de suporte de cenário nesta experiência de logon único:


| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Autenticação Integrada do Windows|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Para saber mais sobre as políticas de acesso do cliente, confira [Limitando o acesso aos Serviços do Office 365 baseado no Local do cliente](https://technet.microsoft.com/library/hh526961.aspx).|



## PingFederate 6.11 

O PingFederate 6.11 implementa o padrão de identidade amplamente utilizado, WS Federation, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:


| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Nenhum (versões anteriores devem ser atualizadas para 6.11)|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para obter instruções do PingFederate sobre como configurar esse STS a fim de fornecer a experiência de logon único para seus usuários do Active Directory, baixe o pdf [aqui.](http://go.microsoft.com/fwlink/?LinkID=266321)

## PingFederate 7.2 
O PingFederate 7.2 implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:


| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Nenhum|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para obter instruções do PingFederate sobre como configurar esse STS a fim de fornecer a experiência de logon único para seus usuários do Active Directory, clique [aqui.](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)

## PingFederate 8.x 
O PingFederate 8 implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:


| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Nenhum|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para obter instruções do PingFederate sobre como configurar esse STS a fim de fornecer a experiência de logon único para seus usuários do Active Directory, clique [aqui.](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## Centrify 
O Centrify ajuda a fornecer uma experiência de logon único federado para o Office 365, sem a necessidade de hospedar um servidor de federação local.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:


| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Nenhum|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Não há suporte para o Controle de Acesso do cliente 

Para saber mais sobre o Centrify, clique [aqui.](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)|

## IBM Tivoli Federated Identity Manager 6.2.2 
O IBM Tivoli Federated Identity Manager 6.2.2 com o IBM Security Access Manager for Microsoft Applications 1.4 implementa o padrão de identidade amplamente usado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:

| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Nenhum|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para saber mais sobre o IBM Tivoli Federated Identity Manager, confira [IBM Security Access Manager for Microsoft Applications.](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)

## SecureAuth IdP 7.2.0 
O SecureAuth IdP 7.2.0 implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma experiência de logon único e uma estrutura de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:

| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Nenhum|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para saber mais sobre o SecureAuth, confira [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).

## CA SiteMinder 12.52 
O CA SiteMinder Federation 12.52 implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:

| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Autenticação Integrada do Windows|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Autenticação Integrada do Windows|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para saber mais sobre o CA SiteMinder, confira [CA SiteMinder Federation.](http://www.ca.com/us/products/ca-single-sign-on.html)

## RadiantOne CFS 3.0 
O RadiantOne Cloud Federation Service (CFS) 3.0 implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:

| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Autenticação Integrada do Windows|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para saber mais sobre o RadiantOne CFS, confira [RadiantOne CFS.](http://www.radiantlogic.com/products/radiantone-cfs/)


## Okta 
O Okta implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:


| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |A Autenticação Integrada do Windows exige a instalação de um servidor Web adicional e do aplicativo Okta.|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Autenticação Integrada do Windows|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para saber mais sobre o Okta, confira [Okta.](https://www.okta.com/)
 
## OneLogin 
O OneLogin, conforme teste em maio de 2014, implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:

| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Autenticação Integrada do Windows|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Autenticação Integrada do Windows|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para saber mais sobre o OneLogin, confira [OneLogin.](https://www.onelogin.com/)

## NetIQ Access Manager 4.0.1 
O NetIQ Access Manager 4.0.1 implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:

| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |*Contratos Kerberos com suporte|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Não há suporte para a Autenticação Integrada do Windows|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

*O NetIQ oferece suporte à autenticação Kerberos por meio da configuração de um Contrato Kerberos. Para obter assistência com essa configuração, entre em contato com a NetIQ ou leia o guia de instalação. Para saber mais sobre o NetIQ Access Manager, confira [NetIQ Access Manager.](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html)

## BIG-IP com Access Policy Manager BIG-IP ver. 11.3x – 11.6x 
O BIG-IP com Access Policy Manager, (APM) BIG-IP ver. 11.3x – 11.6x implementa o padrão de identidade amplamente utilizado, SAML, para fornecer uma experiência de logon único e uma estrutura de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:

| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Sem suporte |Sem suporte|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para saber mais sobre o BIG-IP Access Policy Manager, confira [BIG-IP Access Policy Manager.](https://f5.com/products/modules/access-policy-manager)

Para obter instruções do BIG-IP Access Policy Manager sobre como configurar esse STS a fim de fornecer a experiência de logon único para seus usuários do Active Directory, baixe o pdf [aqui.](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)

## VMware Workspace Portal versão 2.1 
O VMware Workspace Portal versão 2.1 implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:

| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Não há suporte para a Autenticação Integrada do Windows|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Não há suporte para a Autenticação Integrada do Windows|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para saber mais sobre o VMware Workspace Portal versão 2.1, baixe o pdf [aqui.](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)

## Sign&go 5.3 
O Sign&go 5.3 implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:

| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Contratos Kerberos com suporte |
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Nenhum|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|


O Sign&go 5.3 oferece suporte à autenticação Kerberos por meio da configuração de um Kerberos Contract. Para obter assistência com essa configuração, contate a Ilex ou leia o guia de instalação [aqui.](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)


## IceWall Federation versão 3.0 
O IceWall Federation Version 3.0 implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:

| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Não há suporte para a Autenticação Integrada do Windows|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Não há suporte para a Autenticação Integrada do Windows|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para saber mais sobre a IceWall Federation, veja [aqui](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) e [aqui.](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)

## CA Secure Cloud 

O CA Secure Cloud implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:

| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Não há suporte para a Autenticação Integrada do Windows|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Não há suporte para a Autenticação Integrada do Windows|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para saber mais sobre o CA Secure Cloud, confira [CA Secure Cloud.](http://www.ca.com/us/products/security-as-a-service.aspx)

## Dell One Identity Cloud Access Manager v7.1 
O Dell One Identity Cloud Access Manager implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:

| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Nenhum|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|

Para saber mais sobre o Dell One Identity Cloud Access Manager, confira [Dell One Identity Cloud Access Manager.](http://software.dell.com/products/cloud-access-manager)

 Para obter instruções sobre como configurar esse STS a fim de fornecer a experiência de logon único para seus usuários do Office 365, confira como [configurar usuários do Office 365.](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365)

## AuthAnvil Single Sign On 4.5 
O AuthAnvil Single Sign On 4.5 implementa o padrão de identidade amplamente utilizado, WS Federation/WS-Trust, para fornecer uma estrutura de logon único e de troca de atributos.

A seguir, a matriz de suporte de cenário para esta experiência de logon único:

| Cliente |Suporte |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Suportado |Não há suporte para a Autenticação Integrada do Windows|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office, o CRM | Suportado |Não há suporte para a Autenticação Integrada do Windows|
| Clientes de email avançados, como o Outlook e o ActiveSync | Suportado |Nenhum|


Para saber mais, confira o artigo sobre o [logon único do AuthAnvil](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)

<!---HONumber=AcomDC_0810_2016-->