---
title: "Azure AD Connect: autenticação de passagem | Microsoft Docs"
description: "Este tópico fornece as informações que você precisa ter sobre como as autenticações de passagem do Azure AD funcionam com o AD (Active Directory) local para fornecer acesso ao Azure Active Directory (Azure AD) e a serviços conectados."
services: active-directory
keywords: "o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, Logon Único"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 67b832253619afe789a4dfdb95893e8c0ae62bee
ms.openlocfilehash: 17890fddf948ddc0e89a9107ac5fe65223cd05e1
ms.lasthandoff: 01/30/2017

---

# <a name="what-is-azure-ad-pass-through-authentication"></a>O que é a Autenticação de Passagem do Azure AD
Usar a mesma credencial (nome de usuário e senha) para acessar seus recursos corporativos e serviços baseados na nuvem garante que os usuários não precisem se lembrar de credenciais diferentes. Isso reduz as chances de que eles se esqueçam de como fazer login e tem a vantagem de reduzir o envolvimento do suporte técnico para eventos de redefinição de senha.

Embora muitas organizações se sintam à vontade com o uso da [Sincronização de senha](active-directory-aadconnectsync-implement-password-synchronization.md) do Azure AD para fornecer aos usuários uma única credencial para acessar serviços locais e na nuvem, outras organizações exigem que as senhas, mesmo em um formulário de hash, não deixem seus limites organizacionais internos.

A autenticação de passagem do Azure AD fornece uma solução simples para esses clientes. Garante que a validação de senha para serviços do Azure AD seja executada no Active Directory local. As senhas podem ser validadas sem a necessidade de uma infraestrutura de rede complexa e sem haver nenhum tipo de senha local na nuvem.

Quando combinada à opção de [Logon Único](active-directory-aadconnect-sso.md), os usuários não precisam digitar suas senhas para entrar no Azure AD ou em outros serviços de nuvem. Esse recurso fornece a esses clientes uma experiência totalmente integrada em seus computadores corporativos.

![Autenticação de Passagem](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

A autenticação de passagem pode ser configurada por meio do Azure AD Connect e utiliza um agente local simples que aceita solicitações de validação de senha. O agente pode ser implantado facilmente em várias máquinas para fornecer alta disponibilidade e balanceamento de carga. Como todas as comunicações são apenas de saída, não há requisitos para uma DMZ ou para o conector para ser instalado em uma DMZ. Os requisitos da máquina para o conector são os seguintes:

- Windows Server 2012 R2 ou posterior
- Associado a um domínio na floresta em que os usuários serão validados

>[!NOTE]
>Ambientes de várias florestas podem ser compatíveis se houver relações de confiança entre as florestas e o roteamento de sufixo de nome estiver corretamente configurado.

## <a name="supported-clients-in-the-preview"></a>Clientes com suporte na visualização

A autenticação de passagem tem suporte por meio de clientes baseados em navegador da Web e clientes do Office compatíveis com a [autenticação moderna](https://aka.ms/modernauthga). Para clientes incompatíveis, como clientes herdados do Office e o Exchange ActiveSync (ou seja, clientes de email nativos em dispositivos móveis), os clientes são incentivados a usar a autenticação moderna equivalente. Isso permite a autenticação de passagem e que o acesso condicional seja aplicado, como a autenticação multifator.

Para clientes que usam o Windows 10 associado ao Azure AD, a autenticação de passagem não tem suporte atualmente. No entanto, os clientes podem utilizar a sincronização de senha como um fallback automático para Windows 10, além dos clientes herdados mencionados acima.

>[!NOTE]
>Durante a visualização, a sincronização de senha está habilitada por padrão quando a autenticação de passagem for selecionada como opção de login no Azure AD Connect. Isso pode ser desabilitado na página Opções do Azure AD Connect.

## <a name="how-azure-ad-pass-through-authentication-works"></a>Como funciona a autenticação de passagem do Azure AD
Quando um usuário insere seu nome de usuário e senha na página de logon do Azure AD, este coloca o nome de usuário e a senha na fila do conector local apropriada para validação. Um dos conectores disponíveis no local recupera o nome de usuário e a senha e as valida em relação ao Active Directory. A validação ocorre em APIs padrão do Windows de maneira semelhante à forma como os Serviços de Federação do Active Directory validam a senha.

O Controlador de domínio local avalia, então, a solicitação e retorna uma resposta para o conector, que, por sua vez, a retorna ao Azure AD. O Azure AD avalia a resposta e responde ao usuário conforme apropriado, por exemplo, emitindo um token ou pedindo a Autenticação Multifator. Este diagrama mostra as diversas etapas:

![Autenticação de Passagem](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="azure-ad-pass-through-prerequisites"></a>Pré-requisitos de passagem do Azure AD
Antes de habilitar e usar a autenticação de passagem do Azure AD, você precisa ter:

- Azure AD Connect
- Um locatário do Azure AD para o qual você é um administrador global.

>[!NOTE]
>É recomendável que a conta seja uma conta de administrador somente de nuvem, para que você possa gerenciar a configuração do locatário se os serviços locais falharem ou ficarem indisponíveis.

- Um servidor executando o Windows Server 2012 R2 ou superior no qual executar a ferramenta Azure AD Connect. Essa máquina deve ser membro da mesma floresta em que o usuário será validado.
- Observe que, se você tiver mais de uma floresta que contém usuários a serem validados com o Azure AD, as florestas deverão ter relações de confiança entre elas.
- UserPrincipalName local deve ser usado como o nome de usuário do Azure AD.
- Um segundo servidor executando o Windows Server 2012 R2 ou superior no qual executar um segundo conector para alta disponibilidade e balanceamento de carga. Abaixo estão incluídas instruções sobre como implantar esse conector.
- Se houver um firewall entre o conector e o Azure AD, verifique o seguinte:
    - Se a filtragem de URL estiver habilitada, verifique se o conector pode se comunicar com as URLs de acompanhamento:
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net.  
        -  O conector também estabelece uma conexão em conexões IP diretas com os [intervalos de IP do data center do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).
    - Verifique se o firewall não executa a inspeção SSL, pois o conector usa certificados de cliente para se comunicar com o Azure AD.
    - Verifique se o conector pode fazer solicitações HTTPS (TCP) para o Azure AD nas portas abaixo.

|Número da porta|Descrição
| --- | ---
|80|Habilita o tráfego HTTP de saída para a validação de segurança como as listas de certificados revogados de SSL.
|443|    Habilita a autenticação de usuário no Azure AD.
|8080/443|    Habilita a sequência de inicialização do Conector e habilite a atualização automática do Conector.
|9090|    Habilita o registro do Conector (necessário somente para o processo de registro do Conector).
|9091|    Habilita a renovação automática de certificados de confiança do Conector.
|9352, 5671|    Habilita a comunicação entre o Conector e o serviço do Azure AD para solicitações de entrada.
|9350|    [Opcional] Habilita o melhor desempenho para as solicitações de entrada.
|10100–10120|    Habilita respostas do conector para o Azure AD.

Se o firewall reforça o tráfego de acordo com os usuários de origem, abra essas portas para o tráfego proveniente de serviços do Windows em execução como um serviço de rede. Além disso, certifique-se de habilitar a porta 8080 para Autoridade NT\Sistema.

## <a name="enabling-pass-through-authentication"></a>Habilitar a autenticação de passagem
A autenticação de passagem do Azure AD é habilitada por meio do Azure AD Connect. Habilitar a autenticação de passagem implanta o primeiro conector no mesmo servidor que o Azure AD Connect. Ao instalar o Azure AD Connect, selecione uma instalação personalizada e a autenticação de passagem na página de opções de logon. Para saber mais, confira [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Logon único](./media/active-directory-aadconnect-sso/sso3.png)

Por padrão, o primeiro conector de autenticação de passagem será instalado no servidor do Azure AD Connect. Você deve implantar um segundo conector em outro computador para garantir que tenha alta disponibilidade e balanceamento de carga de solicitações de autenticação.

Para implantar o segundo conector, siga as instruções abaixo:

### <a name="step-1-install-the-connector-without-registration"></a>Etapa 1: Instalar o Conector sem registro

1.    Baixe o [conector](https://go.microsoft.com/fwlink/?linkid=837580) mais recente.
2.    Abra um prompt de comando como administrador.
3.    Execute o seguinte comando, no qual /q significa instalação silenciosa: a instalação não solicitará que você aceite o Contrato de Licença de Usuário Final.

```
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
```

### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>Etapa 2: registre o conector no Azure AD para autenticação de passagem

1.    Abra uma janela do PowerShell como administrador
2.    Navegue até **C:\Program Files\Microsoft AAD App Proxy Conector** e execute o script.  
`.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    Quando solicitado, insira as credenciais de sua conta de Administrador de locatário do Azure AD.

## <a name="troubleshooting-pass-through-authentication"></a>Solucionar problemas de autenticação de passagem
Ao solucionar problemas de autenticação de passagem, há algumas categorias diferentes de problemas que podem ocorrer. Dependendo do tipo de problema, convém procurar em locais diferentes.

Para erros relacionados ao conector, verifique o Log de eventos do computador conector em **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**. Se necessário, logs mais detalhados estão disponíveis na exibição dos logs de análise e depuração, bem como na habilitação do log de sessão de conector. Observe que não é recomendável executar com esse log habilitado por padrão e os conteúdos ficam visíveis apenas depois que o log for desabilitado.

Informações adicionais também podem ser encontradas nos logs de rastreamento do conector em **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**. Esses logs também incluem o motivo pelo qual a autenticação de passagem falhou para um usuário individual, como a seguinte entrada abaixo que inclui o código de erro 1328:

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Você pode obter os detalhes do erro iniciando um prompt de comando e executando o seguinte comando: (substitua "1328" pelo número do erro na solicitação).

`Net helpmsg 1328`

O resultado será semelhante à resposta a seguir:

![Autenticação de Passagem](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

Se o log de auditoria estiver habilitado, informações adicionais também podem ser encontradas nos logs de segurança dos Controladores de domínio. Uma consulta simples para solicitações de autenticação pelo conector seria o seguinte:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

Outros erros relatados na tela de logon do Azure AD são detalhados abaixo, junto às etapas de resolução apropriadas.

|Erro|Descrição|Resolução
| --- | --- | ---
|AADSTS80001|Não é possível conectar ao Active Directory|Verifique se as máquinas de conector foram associadas ao domínio e podem se conectar ao Active Directory.  
|AADSTS8002|Um tempo limite ao estabelecer conexão com o Active Directory|Verifique se o Active Directory está disponível e respondendo a solicitações do conector.
|AADSTS80004|O nome de usuário passado para o conector não era válido|Verifique se o usuário está tentando fazer logon com o nome de usuário correto.
|AADSTS80005|Validação encontrou WebException imprevisível|Esse é um problema temporário. Tente novamente a solicitação. Caso a falha persista, contate o suporte da Microsoft.
|AADSTS80007|Erro na comunicação com o Active Directory|Confira os logs de conector para obter mais informações e verifique se o Active Directory está funcionando conforme o esperado.

