---
title: Guia do Administrador de Plug-in de logon único do Microsoft Azure Active Directory | Microsoft Docs
description: Saiba como configurar o logon único entre o Azure Active Directory e logon único do Microsoft Azure Active Directory para JIRA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: af949d1db8af37a534a16364f9f0763479c436e4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-admin-guide"></a>Guia do Administrador de Plug-in de logon único do Microsoft Azure Active Directory

## <a name="table-of-contents"></a>Sumário

1. **[VISÃO GERAL](#overview)**
2. **[COMO FUNCIONA](#how-it-works)**
3. **[PÚBLICO-ALVO](#audience)**
4. **[SUPOSIÇÕES](#assumptions)**
5. **[PRÉ-REQUISITOS](#prerequisites)**
6. **[VERSÕES DO JIRA E CONFLUENCE COM SUPORTE](#supported-versions-of-jira-and-confluence)**
7. **[INSTALAÇÃO](#installation)**
8. **[CONFIGURAÇÃO DO PLUG-IN](#plugin-configuration)**
9. **[EXPLICAÇÃO DO CAMPO PARA A TELA DE CONFIGURAÇÃO DO COMPLEMENTO:](#field-explanation-for-add---on-configuration-screen:)**
10. **[SOLUÇÃO DE PROBLEMAS](#troubleshooting)**

## <a name="overview"></a>Visão geral

Esses complementos permitem que os clientes do Microsoft Azure AD utilizem o Nome de Usuário e senha da Organização para fazer logon nos produtos baseados no Atlassian Jira e Confluence Server. Implementa SSO baseado em SAML 2.0.

## <a name="how-it-works"></a>Como ele funciona

Quando os usuários desejam fazer logon no aplicativo Atlassian Jira ou Confluence, eles veem o botão **Logon com Microsoft Azure AD** na página de logon. Ao clicarem no botão, os usuários são solicitados a fazer logon na página de logon da Organização do Microsoft Azure AD.

Depois que os usuários forem autenticados, eles poderão fazer o logon no aplicativo. Se os usuários já estiverem autenticados com a ID da Organização e senha, eles farão logon diretamente no aplicativo. Além disso, observe que o logon funciona no JIRA e no Confluence. Se os usuários estiverem registrados no aplicativo JIRA e o Confluence também estiver aberto na mesma janela do navegador, será necessário fazer logon uma vez e não precisarão fornecer as credenciais novamente para outro aplicativo. Os usuários também podem obter o produto Atlassian por meio de myapps na conta do Azure e devem estar conectados sem a necessidade das credenciais.

> [!NOTE]
> O provisionamento de usuários não é feito utilizando esse complemento.

## <a name="audience"></a>Público-alvo

Administradores do JIRA e do Confluence que planejam usar esse plug-in para habilitar o SSO utilizando o Microsoft Azure AD.

## <a name="assumptions"></a>Suposições

* A instância do JIRA/Confluence está habilitada para HTTPS
* Usuários já estão criados no JIRA/Confluence
* Usuários têm função atribuída no JIRA/Confluence
* Os administradores têm acesso às informações necessárias para configurar o plug-in
* JIRA/Confluence também estão disponíveis fora da rede da empresa
* Complemento funciona apenas com a versão Local do JIRA e Confluence

## <a name="prerequisites"></a>pré-requisitos

Observe os pré-requisitos a seguir antes de prosseguir com a instalação do complemento:

* JIRA/Confluence estão instalados em uma versão do Windows de 64 bits
* As versões do JIRA/Confluence estão habilitadas para HTTPS
* Observe a versão com suporte do Plug-in na seção “Versões com Suporte” abaixo.
* JIRA/Confluence está disponível na Internet.
* Credenciais de administrador para JIRA/Confluence
* Credenciais de administrador para Microsoft Azure AD
* WebSudo deve estar desabilitado no JIRA e no Confluence

## <a name="supported-versions-of-jira-and-confluence"></a>Versões do JIRA e Confluence com suporte

A partir de agora, as versões do JIRA e do Confluence a seguir têm suporte:

* JIRA Core e Software: 6.0 a 7.2.0
* JIRA Service Desk: 3.0 a 3.2
* Confluence: 5.0 a 5.10

## <a name="installation"></a>Instalação

Para instalar o plug-in, o administrador deverá seguir as etapas indicadas abaixo:

1. Fazer logon na instância do JIRA/Confluence como administrador
    
2. Ir para a Administração do JIRA/Confluence e clicar em Complementos.
    
3. A partir do Atlassian Market Place, pesquise **Plug-in de SSO do SAML do Microsoft**
 
4. A versão apropriada do complemento aparecerá na pesquisa
 
5. Selecione o plug-in e o UPM irá instalar o plug-in.
 
6. Depois que o plug-in for instalado, ele será exibido na seção de complementos Instalados pelo Usuário da seção Gerenciar Complementos
 
7. É necessário configurar o plug-in antes de começar a utilizá-lo.
 
8. Clique no plug-in e você verá um botão de configuração.
 
9. Clique nele para fornecer entradas de configuração
    
## <a name="plugin-configuration"></a>Configuração do Plug-in

A imagem a seguir mostra a tela de configuração do complemento no JIRA e no Confluence
    
![configuração do complemento](./media/ms-confluence-jira-plugin-adminguide/jira.png)

### <a name="field-explanation-for-add-on-configuration-screen"></a>Explicação do campo para a tela de configuração do complemento:

1.   URL de metadados: URL para obter metadados de federação do Microsoft Azure AD
 
2.   Identificador: utilizado pelo Microsoft Azure AD para validar a origem da solicitação. Isso mapeia para o elemento Identificador no Microsoft Azure AD. Isso é derivado automaticamente pelo plug-in como https://<domain:port>/
 
3.   URL de resposta: utilize a URL de resposta no IdP para iniciar o logon do SAML. Isso mapeia para o elemento URL de Resposta no Microsoft Azure AD. Isso é derivado automaticamente pelo plug-in como https://<domain:port>/plugins/servlet/saml/auth
 
4.   URL de Logon: utilize a URL de Logon no IdP para iniciar o logon do SAML. Isso mapeia para o elemento Logon no Microsoft Azure AD. Isso é derivado automaticamente pelo plug-in como https://<domain:port>/plugins/servlet/saml/auth
 
5.   ID da Entidade do IdP: a ID de Entidade que o IdP usa. É preenchida quando a URL de metadados é resolvida.
 
6.   URL de Logon: a URL de Logon do IdP. É preenchida no Microsoft Azure AD quando a URL de metadados é resolvida.
 
7.   URL de Logoff: a URL de Logoff do IdP. É preenchida no Microsoft Azure AD quando a URL de metadados é resolvida.
 
8.   Certificado X.509: o certificado X.509 do IdP. É preenchida no Microsoft Azure AD quando a URL de metadados é resolvida.
 
9.   Nome do botão de logon: nome do botão de logon que sua organização deseja ver. Esse texto é exibido aos usuários no botão de logon na tela de logon.
 
10.   Localizações da ID de Usuário SAML: onde a ID do usuário é esperada na resposta SAML. Pode estar em NameID ou em um nome de atributo personalizado. Essa ID deve ser a ID de usuário do JIRA/Confluence.
 
11.   Nome do atributo: nome do atributo em que a ID de Usuário pode ser esperada.
 
12.   Habilitar Descoberta de Realm Inicial: marque esse sinalizador se a empresa estiver utilizando logon baseado em ADFS.
 
13.   Nome de domínio: forneça aqui o nome de domínio, no caso do logon baseado em ADFS
 
14.   Habilitar Logoff Único: marque essa função se você quiser fazer logoff do Microsoft Azure AD quando um usuário fizer logoff a partir do JIRA/Confluence.

### <a name="troubleshooting"></a>solução de problemas

* Se estiver recebendo vários erros de certificados
    
    * Faça logon no Microsoft Azure AD e remova os vários certificados disponíveis no aplicativo. Certifique-se de que tenha apenas um certificado presente.

* O certificado está prestes a expirar no Microsoft Azure AD.
    
    * Os complementos encarregam-se da sobreposição automática do certificado. Quando um certificado estiver prestes a expirar, o novo certificado deverá ser marcado como ativo e o certificado não utilizado deverá ser excluído. Quando um usuário tenta efetuar logon no JIRA nesse cenário, o complemento efetua fetch do novo certificado e salva no plug-in.

* Como desabilitar o WebSudo (desabilite a sessão de administrador segura)
    
    * JIRA: sessões de administrador seguras (ou seja, confirmação de senha antes de acessar as funções de administração) são habilitadas por padrão. Se você quiser desabilitá-lo na instância do JIRA, é possível desabilitar o recurso especificando a seguinte linha no arquivo: “ira.websudo.is.disabled = true”
    
    * Confluence: siga as etapas fornecidas indicadas na URL a seguir https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html

* Campos que devem ser preenchidos pela URL de metadados não estão sendo preenchidos
    
    * Verifique se a URL está correta. Verifique se o locatário e a ID do aplicativo estão mapeados corretamente.
    
    * Visite a URL do navegador e verifique se está recebendo o XML de metadados de federação.

* Erro Interno do Servidor:
    
    * Percorra os logs presentes no diretório de logs da instalação. Se você estiver recebendo o erro quando o usuário estiver tentando fazer logon utilizando o SSO do Microsoft Azure AD, é possível compartilhar os logs com as informações de Suporte fornecidas abaixo neste documento.

* Erro de ID de usuário não encontrada, quando o usuário tenta fazer logon
    
    * O usuário não é criado no JIRA/Confluence, portanto, crie o usuário.

* Erro de aplicativo não encontrado no Microsoft Azure AD
    
    * Verifique se a URL apropriada está mapeada para o aplicativo no Microsoft Azure AD.

* Detalhes do suporte: contate a: [Equipe de Integração de SSO do Microsoft Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Nós respondemos dentro de 24 a 48 horas úteis.
    
    * Também é possível criar um ticket de suporte com a Microsoft por meio do canal do Portal do Azure.