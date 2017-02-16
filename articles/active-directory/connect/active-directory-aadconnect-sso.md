---
title: "Azure AD Connect: Logon Único | Microsoft Docs"
description: "Este tópico fornece as informações que você precisa saber sobre como executar o logon único de um AD (Active Directory) local baseado para um Azure AD (Azure Active Directory) em nuvem e serviços conectados."
services: active-directory
keywords: "o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 44c5726d0bf621e463aa2f3ab061ed12d48418b0
ms.openlocfilehash: b36d639b5d6b18de3a8233f801cdd0abf3f25825


---

# <a name="what-is-single-sign-on-sso-preview"></a>O que é SSO (Logon Único) (visualização)
O logon único é uma opção que pode ser habilitada no Azure Active Directory Connect com uma [Sincronização de hash de senha](active-directory-aadconnectsync-implement-password-synchronization.md) ou uma [Autenticação de passagem](active-directory-aadconnect-pass-through-authentication.md).  Quando habilitado, os usuários precisam apenas digitar o nome de usuário e não precisam digitar a senha para entrar no Azure AD (Azure Active Directory) ou em outros serviços de nuvem quando estão usando seus computadores corporativos e estão conectados à rede corporativa.

![Logon único](./media/active-directory-aadconnect-sso/sso1.png)

Fornecendo aos usuários finais o SSO, o acesso a serviços baseados na nuvem é mais familiar e oferece à organização um processo simples e seguro que não requer componentes locais adicionais.

O SSO é um recurso que é habilitado por meio do AAD Connect e funciona com a Sincronização de hash de senha ou a Autenticação de passagem e o Active Directory local.  Para que os usuários finais usem o logon único em seu ambiente, você precisa garantir que os usuários:


- Em uma máquina associada ao domínio
- Ter uma conexão direta com um controlador de domínio, por exemplo na rede corporativa com ou sem fio ou por meio de uma conexão de acesso remoto como uma conexão VPN.
- Defina os pontos de extremidade do Kerberos na nuvem como parte da zona da Intranet dos navegadores.

Se qualquer um dos itens acima não estiver presente, por exemplo, se a máquina estiver fora da rede corporativa e o Active Directory não estiver disponível, o usuário simplesmente será solicitado a digitar sua senha como faria sem o logon único.

## <a name="supported-clients"></a>Clientes com suporte
O logon único é compatível com clientes baseados em navegador da Web e clientes do Office que dão suporte à [autenticação moderna](https://aka.ms/modernauthga) em computadores que são compatíveis com a autenticação Kerberos, como o Windows.  A matriz a seguir fornece detalhes dos clientes com base em navegador em vários sistemas operacionais.

| Sistema operacional\Navegador |Internet Explorer|Chrome|Firefox|Edge
| --- | --- |--- | --- |--- |
|Windows 10|Sim|Sim|Sim*|Não
|Windows 8.1|Sim|Sim|Sim*|N/D
|Windows 8|Sim|Sim|Sim*|N/D
|Windows 7|Sim|Sim|Sim*|N/D
|Mac|N/D|N/D|N/D|N/D

*Requer uma configuração separada.

>[!NOTE]
>Para clientes baseados em Windows 10, a recomendação é usar a [junção do Azure AD](../active-directory-azureadjoin-overview.md) para melhorar sua experiência com o Azure AD.

## <a name="how-single-sign-on-works"></a>Como funciona o logon único

Quando você habilita o logon único no Azure AD Connect, uma conta de computador chamada AZUREADSSOACCT é criada no Active Directory local, e a chave de descriptografia Kerberos é compartilhada com segurança com o Azure AD.  Além disso, os dois SPNs (nomes de entidade de serviço) Kerberos são criados para representar as URLs de nuvem que são usadas durante a autenticação entre o cliente e o Azure AD.

Quando a instalação for concluída, o processo de autenticação será o mesmo que é usado para qualquer outro aplicativo baseado na IWA (Autenticação Integrada do Windows).  Se está familiarizado com o funcionamento do IWA, você já sabe como o logon único funciona com o Azure AD.  Se você não está familiarizado, o processo do IWA é o seguinte:

![Logon único](./media/active-directory-aadconnect-sso/sso2.png)

Primeiro, o usuário tenta acessar um recurso que confia nos tokens emitidos pelo Azure AD, como o SharePoint online.  O SharePoint online então redireciona o usuário para autenticação com o Azure AD. O usuário fornece o nome de usuário para que o Azure AD possa estabelecer se o logon único está habilitado para sua organização. Supondo que o logon único esteja habilitado para a organização, ocorre o seguinte.

1.  O Azure AD desafia o cliente, por meio da resposta 401 Não autorizado, para fornecer um tíquete Kerberos.
2.  O cliente solicita um tíquete do Active Directory para o Azure AD.
3.  O Active Directory localiza a conta de computador, criada pelo Azure AD Connect, e retorna um tíquete Kerberos para o cliente, criptografado com o segredo da conta do computador. O tíquete inclui a identidade do usuário atualmente conectado ao computador.
4.  O cliente envia o tíquete Kerberos que adquiriu do Active Directory para o Azure AD.
5.  O Azure AD descriptografa o tíquete Kerberos usando a chave compartilhada anteriormente e retorna um token para o usuário ou pede que o usuário forneça provas adicionais, como autenticação multifator, conforme exigido pelo recurso.

O logon único é um recurso oportunista, o que significa que, se ele falhar por algum motivo, o usuário precisará apenas digitar sua senha na página de logon, como de costume.

## <a name="enabling-sso-with-pass-through-authentication-or-password-hash-sync"></a>Habilitar o SSO com a autenticação de passagem ou a sincronização de hash de senha
O Azure AD Connect fornece um processo simples para habilitar o logon único com a autenticação de Passagem ou sincronização de hash de Senha.  Você precisará garantir que tenha direitos de administrador de domínio para um dos domínios de cada floresta que sincronizar para permitir a configuração de SPNs (nomes de entidade de serviço) Kerberos na conta da máquina.  O nome de usuário e a senha não são armazenados no Azure AD Connect ou no Azure AD e são usados apenas para essa operação.

Ao instalar o Azure AD Connect, selecione uma instalação personalizada para que você possa selecionar a opção de logon único na página de entrada do usuário. Para obter mais detalhes, confira [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Logon único](./media/active-directory-aadconnect-sso/sso3.png)

Depois que o logon único for habilitado, você poderá continuar com o assistente até chegar à página Logon único.

![Logon único](./media/active-directory-aadconnect-sso/sso4.png)

Para cada floresta listada, forneça os detalhes de conta apropriados, e o logon único será habilitado para o diretório do Azure.

>[!NOTE]
>O Azure AD Connect precisa estar apto a se comunicar com *.msappproxy.net na porta 9090 (TCP) para configurar o SSO. Isso só é necessário durante a configuração e não é usado durante autenticações pelos usuários finais.

## <a name="ensuring-clients-sign-in-automatically"></a>Garantir que os clientes entrem automaticamente
Por padrão, os navegadores não tentarão enviar credenciais a servidores Web, a menos que a URL seja definida como estando na zona da Intranet.  Em geral, o navegador pode calcular a zona correta examinando a URL.  Por exemplo, se a URL for http://intranet/, o navegador enviará automaticamente as credenciais, pois ele mapeará a URL para a zona da intranet.  No entanto, se a URL contiver um ponto, por exemplo, http://intranet.contoso.com/, o servidor não enviará automaticamente as credenciais e a tratará como faria com qualquer site da Internet.

Como as URLs usadas para logon único no Azure AD contêm um ponto, há que são nomes de host roteáveis globalmente, precisam ser adicionadas explicitamente à zona de Intranet do computador, para que o navegador envie automaticamente as credenciais do usuário conectado no momento na forma de um tíquete Kerberos ao Azure AD.  A maneira mais fácil de adicionar as URLs necessárias à zona da Intranet é simplesmente criar uma política de grupo no Active Directory.

1.  Abrir as ferramentas de Gerenciamento de Política de Grupo
2.  Edite a política de grupo que será aplicada a todos os usuários.  Por exemplo, a política de domínio padrão.
3.  Navegue até **Configuração do Usuário\Modelos Administrativos\Componentes do Windows\Internet Explorer\Painel de Controle da Internet\Página de Segurança** e selecione **Lista de Atribuição de Site para Zona**.
![Logon Único](./media/active-directory-aadconnect-sso/sso6.png) </br>
4.  Habilite a política e insira os valores/dados a seguir na caixa de diálogo.</br>

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1'
5.  Ele deve ser semelhante ao seguinte: ![Logon único](./media/active-directory-aadconnect-sso/sso7.png)

6.  Clique em OK e em OK novamente.

Agora os usuários estão prontos para o logon único.

>[!NOTE]
>Por padrão, o Chrome usará o mesmo conjunto de URLs de sites confiáveis que o Internet Explorer.  Se tiver definido configurações diferentes para o Chrome, você precisará atualizá-los separadamente.

## <a name="troubleshooting-single-sign-on-issues"></a>Solução de problemas de logon único
É importante garantir que o cliente esteja configurado corretamente para o logon único, incluindo o seguinte:

1.  https://autologon.microsoftazuread-sso.com e https://aadg.windows.net.nsatc.net são definidos dentro da zona de Intranet.
2.  Verifique se a estação de trabalho está associada ao domínio.
3.  Verifique se o usuário está conectado com uma conta de domínio.
4.  Verifique se o computador está conectado à rede corporativa
5.  Verifique se a hora da máquina está sincronizada com o Active Directory e se a hora dos controladores de domínio está dentro do intervalo de 5 minutos após a hora correta.
6.  Limpe os tíquetes Kerberos existentes dos clientes, por exemplo, executando o comando "klist purge" em um prompt de comando.

Se foi possível confirmar os requisitos acima, você pode examinar os logs do console do navegador para obter informações adicionais.  Os logs de console podem ser encontrados em ferramentas de desenvolvedor.  Isso ajudará a determinar o problema em potencial.

## <a name="event-log-entries"></a>Entradas de log de eventos
Sempre que um usuário faz logon com o logon único, uma entrada é registrada no log de eventos do controlador de domínio, se a auditoria de êxito está habilitada.  Para localizar esses eventos, você pode examinar os logs de eventos do Evento segurança 4769 associado à conta de computador AzureADSSOAcc$.  O filtro a seguir localiza todos os eventos de segurança associados à conta do computador:

    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>



<!--HONumber=Jan17_HO1-->


