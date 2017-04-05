---
title: Usar servidores NPS existentes para fornecer recursos ao Azure MFA| Microsoft Docs
description: "A extensão do Servidor de Políticas de Rede para a Autenticação Multifator do Azure é uma solução simples para adicionar recursos de verificação em duas etapas baseados em nuvem à sua infraestrutura de autenticação existente."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 34919221a82a024bd3a1d09c1def6040ff6c55e1
ms.lasthandoff: 03/23/2017

---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication---public-preview"></a>Integrar sua infraestrutura existente do NPS à Autenticação Multifator do Azure - Visualização pública

A extensão do Servidor de Políticas de Rede (NPS) para o Azure MFA adiciona recursos MFA baseados em nuvem à sua infraestrutura de autenticação usando os seus servidores existentes. Com a extensão do NPS, você pode adicionar verificação por chamada telefônica, SMS ou aplicativo ao fluxo de autenticação existente sem a necessidade de instalar, configurar e manter novos servidores. 
 
Ao usar a extensão do NPS para o Azure MFA, o fluxo de autenticação inclui os seguintes componentes: 

1. **Servidor VPN/NAS** recebe solicitações de clientes VPN e converte-os em solicitações RADIUS para servidores NPS. 
2. **Servidor NPS** conecta-se ao Active Directory para executar a autenticação primária das solicitações RADIUS e, ao obter êxito, passa a solicitação para quaisquer extensões instaladas.  
3. **Extensão do NPS** dispara uma solicitação ao Azure MFA para a autenticação secundária. Quando a extensão receber a resposta, e se o desafio de MFA for bem-sucedido, ela concluirá a solicitação de autenticação, fornecendo ao servidor NPS os tokens de segurança que incluem uma declaração MFA, emitida pelo STS do Azure.  
4. O **Azure MFA** comunica-se com o Azure Active Directory para recuperar os detalhes do usuário e executa a autenticação secundária usando um método de verificação configurado para o usuário.

O diagrama a seguir ilustra esse fluxo de solicitação de autenticação de alto nível: 

![Diagrama de fluxo de autenticação](./media/multi-factor-authentication-nps-extension/auth-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

A extensão do NPS deve trabalhar com sua infraestrutura existente. Verifique se você cumpre os seguintes pré-requisitos antes de iniciar.

### <a name="licenses"></a>Licenças

A extensão do NPS para o Azure MFA está disponível para clientes com [licenças para Autenticação Multifator do Azure](multi-factor-authentication.md) (incluído no Azure AD Premium, EMS ou uma assinatura de MFA).

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 ou superior com o componente NPS habilitado.

### <a name="libraries"></a>Bibliotecas

-    [Pacotes redistribuíveis do Visual C++ para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Módulo Microsoft Azure Active Directory para Windows PowerShell versão 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

### <a name="azure-active-directory"></a>Azure Active Directory

Todos que usam a extensão do NPS devem estar sincronizados com o Azure Active Directory usando o Azure AD Connect e devem estar habilitados para MFA.

Ao instalar a extensão, você precisa das credenciais de administrador e a ID de diretório para seu locatário do Azure AD. Você pode encontrar a ID de diretório no [Portal do Azure](https://portal.azure.com). Entre como administrador, selecione o ícone **Azure Active Directory** à esquerda e selecione **Propriedades**. Copie o GUID da caixa **ID de diretório** e salve.

![Localize sua ID de diretório em Propriedades do Azure Active Directory](./media/multi-factor-authentication-nps-extension/find-directory-id.png)

## <a name="install-the-nps-extension"></a>Instalar a extensão NPS

> [!IMPORTANT]
> Instale a extensão do NPS em um servidor diferente do ponto de acesso VPN. 

Para instalar a extensão do NPS para o Azure MFA:

1.    [Baixe a extensão do NPS](https://aka.ms/npsmfa) do Centro de Download da Microsoft
2.    Copie o binário para o Servidor de Políticas de Rede que você deseja configurar
3.    Execute o arquivo *setup.exe* e siga as instruções de instalação

Depois de concluir a instalação, o instalador cria um script do PowerShell neste local: `C:\Program Files\Microsoft\AzureMfa\Config` (em que C:\ é a sua unidade de instalação). O script do PowerShell executa as ações a seguir:

-    Crie um certificado autoassinado.
-    Associa a chave pública do certificado à entidade de serviço no Azure AD.
-    Armazena o certificado no repositório de certificados do computador local.
-    Concede acesso à chave privada do certificado ao usuário de rede.
-    Reinicia o NPS.

A menos que você deseje usar seus próprios certificados (em vez dos certificados autoassinados gerados pelo script do PowerShell), execute o Script do PowerShell para concluir a instalação. Se você instalar a extensão em vários servidores, cada um deve ter seu próprio certificado para que você não tenha tempo de inatividade ao renovar os certificados. 

## <a name="configure-your-nps-extension"></a>Configurar sua extensão do NPS

Esta seção inclui considerações sobre o design e sugestões para implantações bem-sucedidas da extensão do NPS.

### <a name="configurations-limitations"></a>Limitações de configurações

- A extensão do NPS destina-se a novas implantações e não ao trabalho com a implantação existente. Por esse motivo, a extensão do NPS para o Azure MFA não inclui ferramentas para migrar usuários e configurações do servidor MFA para a nuvem.

- A extensão do NPS usa o UPN do Active Directory local para identificar o usuário no Azure MFA para realizar a autenticação secundária. A extensão não pode ser configurada para usar um identificador diferente, como a ID de logon alternativa ou campo personalizado do AD que não seja o UPN.  

### <a name="control-radius-clients-that-require-mfa"></a>Clientes RADIUS de controle que exigem MFA

Depois que você habilita a MFA para um cliente RADIUS usando a extensão do NPS, todas as autenticações desse cliente são necessárias para executar a MFA. Se você deseja habilitar a MFA para alguns clientes RADIUS, mas outros não, você pode configurar dois servidores NPS e instalar a extensão em apenas um deles. Configure clientes RADIUS para os quais você deseja que a MFA envie solicitações ao servidor NPS configurado com a extensão e outros clientes RADIUS para o servidor NPS não configurado com a extensão.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Preparar usuários que não são registrados na MFA

Se você tiver usuários que não são registrados na MFA, determine o que acontece quando eles tentam fazer a autenticação. Use a configuração de Registro *REQUIRE_USER_MATCH* no caminho do Registro *HKLM\Software\Microsoft\AzureMFA* para controlar o comportamento do recurso. Essa configuração tem uma opção de configuração única:

| Chave | Valor | Padrão |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | Não definido (equivalente a TRUE) |

A finalidade dessa configuração é determinar o que fazer quando um usuário não estiver inscrito na MFA. Quando a chave não existir, não estiver definida ou estiver definida como TRUE e o usuário não estiver registrado, a extensão falha no desafio da MFA. Quando a chave estiver definida como FALSE e o usuário não estiver registrado, a autenticação continuará sem executar a MFA.

Você pode optar por criar essa chave e defini-la como FALSE durante a integração do usuário. Como definir a chave permite que os usuários que não são registrados na MFA se conectem sem passar pelo desafio, você deve remover essa chave antes de ir para a produção.

## <a name="troubleshooting"></a>Solucionar problemas

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Como verificar se o certificado do cliente está instalado conforme o esperado?

Procure o certificado autoassinado criado pelo instalador no repositório de certificados e verifique se a chave privada tem permissões concedidas ao usuário **NETWORK SERVICE**. O certificado terá um nome de entidade igual a **CN \<tenantid\>, OU = extensão NPS da Microsoft**

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Como verificar se o certificado do cliente está associado ao meu locatário no Azure Active Directory?

Abra um prompt de comando no PowerShell e execute os seguintes comandos:

```
> import-module MSOnline
> Connect-MsolService
> Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

Esses comandos imprimem todos os certificados associados ao seu locatário com a instância da extensão do NPS em sua sessão do PowerShell. Procure seu certificado exportando o certificado do cliente como um arquivo "codificado em Base 64 X.509(.cer)" sem a chave particular e compare-a com a lista do PowerShell.

Os carimbos de data/hora Válido-de e Válido-até, que estão em formato legível, poderão ser usados para filtrar desvios óbvios se o comando retornar mais de um certificado.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Por que minhas solicitações estão falhando, com erro de token ADAL?

Esse erro pode ser causado por vários motivos. Use estas etapas para solucionar o problema:

1. Reinicie o servidor NPS.
2. Verifique se o certificado do cliente está instalado conforme o esperado.
3. Verifique se o certificado está associado ao seu locatário no Azure AD.
4. Verifique se o link https://login.windows.net/ pode ser acessado no servidor que está executando a extensão.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Por que a autenticação falha, com um erro nos logs de HTTP, informando que o usuário não foi encontrado?

Verifique se o AD Connect está em execução e se o usuário está presente no Windows Active Directory e no Azure Active Directory.

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Por que vejo erros de conexão HTTP nos logs, com todas as minhas autenticações falhando?

Verifique se o link https://adnotifications.windowsazure.com pode ser acessado por meio do servidor que executa a extensão do NPS.

## <a name="next-steps"></a>Próximas etapas

Consulte como integrar o Azure MFA com o [Active Directory](multi-factor-authentication-get-started-server-dirint.md), a [autenticação RADIUS](multi-factor-authentication-get-started-server-radius.md) e a [autenticação LDAP](multi-factor-authentication-get-started-server-ldap.md).

