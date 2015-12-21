<properties
   pageTitle="Biblioteca de documentos para gerenciamento de aplicativos | Microsoft Azure"
   description="Tópicos de gerenciamento de aplicativo do Active Directory do Azure, com links de referência técnica para instruções, soluções de problemas e perguntas frequentes"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/03/2015"
   ms.author="kgremban"/>

# Biblioteca de documentos para gerenciamento de aplicativos

Esta página contém links úteis para os principais recursos de logon único no Active Directory do Azure. Os links estão organizados nas categorias a seguir:

- **Visão geral:** uma visão geral do recurso, com casos de uso e introduções ao tópico.
- **Instruções:** instruções detalhadas para cenários específicos.
- **Solução de problemas:** dicas para diagnosticar problemas comuns.
- **Perguntas frequentes:** respostas para perguntas e preocupações típicas.  

## Cloud App Discovery

| | |
| ------ | ------ |
| Visão geral | [Como descobrir aplicativos na nuvem não aprovados, usados em minha organização](active-directory-cloudappdiscovery-whatis.md) |
| Instruções | [Getting started with Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) <br><br> [Considerações de segurança e privacidade do Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) <br><br> [Cloud App Discovery group policy deployment guide](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx) <br><br> [Cloud App Discovery System Center deployment guide](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx) <br><br> [Configurações de registro do Cloud App Discovery para serviços de proxy](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| Perguntas frequentes | [Cloud App Discovery—frequently asked questions](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |

## Atribuição de aplicativo

| | |
| ------ | ------ |
| Visão geral | [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md) |
| Instruções | [Criando uma regra simples para configurar associações dinâmicas para um grupo](active-directory-accessmanagement-simplerulegroup.md) <br><br> [Usando um grupo para gerenciar o acesso a aplicativos SaaS](active-directory-accessmanagement-group-saasapps.md) <br><br> [Configurando o AD do Azure para gerenciamento de acesso de aplicativos de autoatendimento](active-directory-accessmanagement-self-service-group-management.md) <br><br> [Gerenciamento de proprietários em um grupo](active-directory-accessmanagement-managing-group-owners.md) <br><br> [Gerenciamento de grupos de segurança no Active Directory do Azure](active-directory-accessmanagement-manage-groups.md) <br><br> [Grupos dedicados no Active Directory do Azure](active-directory-accessmanagement-dedicated-groups.md) <br><br> [Uso de atributos para criar regras avançadas](active-directory-accessmanagement-groups-with-advanced-rules.md) <br><br> [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Active Directory do Azure](active-directory-saas-app-provisioning.md) <br><br> [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md) |

## Logon único federado

| | |
| ------ | ------ |
| Visão geral |[Configurando a federação com o AD FS](active-directory-aadconnect-get-started-custom.md)
| Instruções | [DirSync com logon único](https://msdn.microsoft.com/library/azure/dn441213.aspx) <br><br> [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory/) <br><br> [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md) <br><br> [Marketplace o Active Directory](https://azure.microsoft.com/marketplace/active-directory/) |
| Solucionar problemas | [Solução de problemas do Assistente de configuração da ferramenta DirSync do Azure AD](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx) <br><br> [Como solucionar erros do Assistente de instalação e configuração da ferramenta de Sincronização do Microsoft Azure Active Directory](https://support.microsoft.com/kb/2684395) <br><br> [Solucionar problemas de sincronização de diretório](https://msdn.microsoft.com/library/azure/jj151787.aspx) <br><br> [Solucionar problemas de logon único](https://msdn.microsoft.com/library/azure/jj151834.aspx) |
| Perguntas frequentes | [Gerenciamento de certificados para logon único federado no Active Directory do Azure](active-directory-sso-certs.md) <br><br>[Perguntas frequentes do Azure Active Directory Connect](active-directory-aadconnect-faq.md) |

## Logon único com senha

| | |
| ------ | ------ |
| Instruções | [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md) <br><br>[DirSync com sincronização de diretório](https://msdn.microsoft.com/library/azure/dn441214.aspx) |
| Solucionar problemas | [Solução de problemas do Assistente de configuração da ferramenta de Sincronização de Diretório do Azure AD](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx) <br><br> [Como solucionar erros do Assistente de instalação e configuração da ferramenta de Sincronização do Active Directory do Azure](https://support.microsoft.com/kb/2684395) <br><br> [Solucionar problemas de sincronização de diretório](https://msdn.microsoft.com/library/azure/jj151787.aspx) |
| Perguntas frequentes | [Perguntas frequentes do Azure Active Directory Connect](active-directory-aadconnect-faq.md) |

## Provisionamento avançado de aplicativo

| | |
| ------ | ------ |
| Visão geral | [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Active Directory do Azure](active-directory-saas-app-provisioning.md) |
| Instruções | [Tutorial: Integração do Active Directory do Azure com o Concur](active-directory-saas-concur-tutorial.md) <br><br> [Tutorial: Integração do Active Directory do Azure com o DocuSign](active-directory-saas-docussign-tutorial.md) <br><br> [Tutorial: Integração do Active Directory do Azure com o Dropbox for Business](active-directory-saas-dropboxforbusiness-tutorial.md) <br><br> [Tutorial: Como integrar o Google Apps ao Active Directory do Azure](active-directory-saas-google-apps-tutorial.md) <br><br> [Tutorial: Como integrar o Salesforce ao Active Directory do Azure](active-directory-saas-salesforce-tutorial.md) <br><br> [Tutorial: Integração do Active Directory do Azure com o ServiceNow](active-directory-saas-servicenow-tutorial.md) <br><br> [Tutorial: Configuração do Workday para sincronização de entrada](active-directory-saas-workday-inbound-tutorial.md) |

## Galeria de aplicativos do Azure AD

| | |
| ------ | ------ |
| Instruções | [Listagem de seu aplicativo na galeria de aplicativos do Active Directory do Azure](active-directory-app-gallery-listing.md) <br><br> ["Bring your own app" with Azure AD self-service SAML configuration](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |

## Aplicativos personalizados

| | |
| ------ | ------ |
| Visão geral | ["Bring your own app" with Azure AD self-service SAML configuration](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |
| Instruções | [Tutorial: Como integrar o Salesforce ao Active Directory do Azure](active-directory-saas-salesforce-tutorial.md) |

## Proxy do aplicativo

| | |
| ------ | ------ |
| Visão geral | [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md) |
| Instruções | [Habilitando o proxy de aplicativo do AD do Azure](active-directory-application-proxy-enable.md) <br><br> [Publicar aplicativos usando o proxy de aplicativo do AD do Azure](active-directory-application-proxy-publish.md) <br><br> [Trabalho com acesso condicional](active-directory-application-proxy-conditional-access.md) <br><br> [Trabalho com domínios personalizados no proxy de aplicativo do Azure AD](active-directory-application-proxy-custom-domains.md) <br><br> [Trabalho com aplicativos com reconhecimento de declarações no proxy de aplicativo](active-directory-application-proxy-claims-aware-apps.md) <br><br> [Logon único com o proxy de aplicativo](active-directory-application-proxy-sso-using-kcd.md) |
| Solucionar problemas | [Solucionar problemas de Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md) |

## Experiência de inicialização de aplicativo

| | |
| ------ | ------ |
| Visão geral | [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md) |
| Instruções | [Azure Active Directory Access Panel - EMS](http://blogs.msdn.com/b/haddy_el-haggan_blog/archive/2015/04/02/azure-active-directory-access-panel-ems.aspx) <br><br> [Integração com o Active Directory do Azure](active-directory-how-to-integrate.md) |

<!---HONumber=AcomDC_1210_2015-->