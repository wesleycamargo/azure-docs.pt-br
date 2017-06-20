---
title: "Autenticação RADIUS e Servidor de MFA do Azure | Microsoft Docs"
description: "Esta é a página do Azure Multi-Factor Authentication que auxiliará na implantação de autenticação RADIUS e do servidor Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: f4ba0fb2-2be9-477e-9bea-04c7340c8bce
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/26/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017, it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: e696b95c9db86b062440f0c4fd788bf97223317a
ms.contentlocale: pt-br
ms.lasthandoff: 02/28/2017

---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>Integrar a autenticação RADIUS com o Servidor de Autenticação Multifator do Azure
Use a seção de Autenticação RADIUS do Servidor de MFA do Azure para habilitar e configurar a autenticação RADIUS. RADIUS é um protocolo padrão para aceitar solicitações de autenticação e processar essas solicitações. O Servidor de Autenticação Multifator do Azure atua como um servidor RADIUS. Insira-o entre o cliente RADIUS (dispositivo VPN) e o destino de autenticação, que pode ser o Active Directory (AD), um diretório LDAP ou outro servidor RADIUS, para adicionar a Autenticação Multifator do Azure. Para a MFA (Autenticação Multifator) do Azure funcionar, você deve configurar o Servidor de MFA do Azure para que ele possa se comunicar com os servidores clientes e com o destino de autenticação. O Servidor de MFA do Azure aceita solicitações de um cliente RADIUS, valida as credenciais no destino de autenticação, adiciona a Autenticação Multifator do Azure e devolve uma resposta ao cliente RADIUS. A solicitação de autenticação só terá êxito se a autenticação primária e a Autenticação Multifator do Azure tiverem êxito.

> [!NOTE]
> O servidor MFA só dá suporte a PAP (protocolo de autenticação de senha) e o MSCHAPv2 (Microsoft’s Challenge Handshake Authentication Protocol) de protocolos de RADIUS agindo como um servidor RADIUS.  Outros protocolos, como o EAP (Protocolo de Autenticação Extensível) podem ser usados quando o servidor MFA atua como um proxy RADIUS para outro servidor RADIUS que dá suporte a esse protocolo.
>
> Os tokens SMS e OATH unidirecionais não funcionam nessa configuração, pois o servidor MFA não pode iniciar uma resposta de desafio de RADIUS bem-sucedida usando protocolos alternativos.

![Autenticação Radius](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="add-a-radius-client"></a>Adicionar um cliente RADIUS
Para configurar a autenticação RADIUS, instale o servidor Azure Multi-Factor Authentication em um servidor Windows. Se você tiver um ambiente do Active Directory, o servidor deve ser associado ao domínio dentro da rede. Use o procedimento a seguir para configurar o servidor Azure Multi-Factor Authentication:

1. No Servidor de Autenticação Multifator do Azure, clique no ícone de Autenticação RADIUS no menu esquerdo.
2. Marque a caixa de seleção **Habilitar autenticação RADIUS**.
3. Na guia Clientes, altere as portas de Autenticação e de Conta se o serviço RADIUS de MFA do Azure precisa escutar solicitações de RADIUS em portas não padrão.
4. Clique em **Adicionar**.
5. Insira o endereço IP do dispositivo/servidor que fará a autenticação no Servidor de Autenticação Multifator do Azure, um nome de aplicativo (opcional) e um segredo compartilhado.

  O nome do aplicativo aparece nos relatórios da Autenticação Multifator do Azure e pode ser exibido nas mensagens de autenticação por SMS ou do Aplicativo Móvel.

  O segredo compartilhado deve ser o mesmo tanto no Servidor de Autenticação Multifator do Azure quanto no dispositivo/servidor.

6. Marque a caixa **Exigir correspondência do usuário da Autenticação Multifator** se todos os usuários foram ou vierem a ser importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da verificação em duas etapas, deixe a caixa desmarcada.
7. Marque a caixa **Habilitar token OATH de fallback** se desejar usar senhas OATH de aplicativos móveis de verificação como um fallback para chamada telefônica, SMS ou notificação por push fora de banda.
8. Clique em **OK**.

Repita as etapas 4 à 8 para adicionar tantos clientes RADIUS quanto necessário.

## <a name="configure-your-radius-client"></a>Configurar o cliente RADIUS

1. Clique na guia **Destino**.
2. Se o Servidor de MFA do Azure estiver instalado em um servidor ingressado em domínio em um ambiente do Active Directory, selecione o domínio do Windows.
3. Se os usuários devem ser autenticados em um diretório LDAP, selecione **Associação LDAP**.

  Para usar a associação LDAP, clique no ícone de Integração de Diretórios e edite a configuração de LDAP na guia Configurações para que o servidor possa se associar ao diretório. As instruções para configurar o LDAP podem ser encontradas no [guia de configuração de Proxy LDAP](multi-factor-authentication-get-started-server-ldap.md).

4. Se os usuários forem autenticados em outro servidor RADIUS, selecione servidores RADIUS.
5. Clique em **Adicionar** para configurar o servidor para o qual o Servidor de MFA do Azure fará proxy das solicitações de RADIUS.
6. Na caixa de diálogo Adicionar Servidor RADIUS, digite o endereço IP do servidor RADIUS e um segredo compartilhado.

  O segredo compartilhado deve ser o mesmo no Servidor de Autenticação Multifator do Azure e no servidor RADIUS. Altere a porta de autenticação e a porta de contabilização se forem usadas portas diferentes pelo servidor RADIUS.

7. Clique em **OK**.
8. Adicione o Servidor de MFA do Azure como um cliente RADIUS no outro servidor RADIUS para que ele possa processar as solicitações de acesso enviadas do Servidor de MFA do Azure. Use o mesmo segredo compartilhado configurado no Servidor de Autenticação Multifator do Azure.

Repita estas etapas para adicionar mais servidores RADIUS e configurar a ordem em que o Servidor de MFA do Azure deve chamá-los com os botões **Mover para Cima** e **Mover para Baixo**.

Isso conclui a configuração do servidor Azure Multi-Factor Authentication. O servidor agora está escutando nas portas configuradas solicitações de acesso RADIUS dos clientes configurados.   

## <a name="radius-client-configuration"></a>Configuração do cliente RADIUS
Para configurar o cliente RADIUS, use as diretrizes:

* Configure o dispositivo/servidor para autenticar via RADIUS para o endereço IP do servidor Azure Multi-Factor Authentication, que atuará como o servidor RADIUS.
* Use o mesmo segredo compartilhado que foi configurado anteriormente.
* Configure o tempo limite RADIUS para 30 a 60 segundos, de forma que haja tempo para validar as credenciais do usuário, executar a verificação em duas etapas, receber a resposta e responder à solicitação de acesso RADIUS.

