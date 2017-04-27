---
title: "Autenticação LDAP e Servidor Azure MFA | Microsoft Docs"
description: "Essa é a página Azure Multi-Factor Authentication que auxiliará na implantação de autenticação LDAP e do servidor Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: e1a68568-53d1-4365-9e41-50925ad00869
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1c6386dda94a3e0ca6eb340f542d04cb336159c3
ms.openlocfilehash: 0de97050e385e3efb9e63bbf934712157ab0d0af
ms.lasthandoff: 02/17/2017


---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Servidor Azure Multi-Factor Authentication e autenticação LDAP
Por padrão, o Servidor do Azure Multi-Factor Authentication é configurado para importar ou sincronizar usuários do Active Directory. No entanto, ele pode ser configurado para associar a diferentes diretórios LDAP, como um diretório ADAM ou controlador de domínio específico do Active Directory. Quando conectado a um diretório via LDAP, o servidor de Autenticação Multifator do Azure pode ser configurado para atuar como um proxy LDAP e realizar autenticações. Ele também permite o uso da associação LDAP como um destino RADIUS para pré-autenticação dos usuários com a Autenticação do IIS ou autenticação principal no portal de usuário do Azure MFA.

Para usar autenticação multifator do Azure como um proxy LDAP, insira o Servidor de Autenticação Multifator do Azure entre o cliente LDAP (por exemplo, o dispositivo ou aplicativo VPN) e o servidor do diretório LDAP. O Servidor de Autenticação Multifator do Azure deve ser configurado para se comunicar com servidores do cliente e com o diretório LDAP. Nessa configuração, o servidor Azure Multi-Factor Authentication aceita solicitações LDAP dos aplicativos e servidores clientes e os encaminha para o servidor de diretório LDAP de destino para validar as credenciais principais. Se a resposta do diretório LDAP mostrar que as credenciais primárias são válidas,a Autenticação Multifator do Azure executa a segunda verificação de identidade e envia uma resposta ao cliente LDAP. Toda a autenticação só será bem-sucedida se a autenticação do servidor LDAP e a verificação de segunda etapa forem bem-sucedidas.

## <a name="ldap-authentication-configuration"></a>Configuração de autenticação LDAP
Para configurar a autenticação LDAP, instale o servidor Azure Multi-Factor Authentication em um servidor Windows. Use este procedimento:

### <a name="add-an-ldap-client"></a>Adicionar um cliente LDAP

1. No Servidor de Autenticação Multifator do Azure, selecione o ícone de Autenticação LDAP no menu à esquerda.
2. Marque a caixa de seleção **Habilitar Autenticação LDAP**.

        ![LDAP Authentication](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)

3. Na guia Clientes, altere a porta TCP e a porta SSL se o serviço LDAP da Autenticação Multifator do Azure tiver que se associar às portas não padrão para escutar solicitações LDAP.
4. Se você planeja usar LDAPS do cliente para o servidor Azure Multi-Factor Authentication, um certificado SSL deve ser instalado no servidor que está executando o servidor. Clique no botão **Procurar...** ao lado da caixa do certificado SSL e selecione o certificado instalado que será usado para a conexão segura.
5. Clique em **Adicionar...**
6. Na caixa de diálogo Adicionar Cliente LDAP, digite o endereço IP do dispositivo, servidor ou aplicativo que fará a autenticação no servidor e um nome de Aplicativo (opcional). O nome do aplicativo aparece nos relatórios do Azure Multi-Factor Authentication e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel.
7. Marque a caixa correspondente a **Exigir correspondência de usuário de Autenticação Multifator do Microsoft Azure** se todos os usuários tiverem sido ou forem importados para o servidor e estiverem sujeitos à verificação em duas etapas. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da verificação em duas etapas, deixe a caixa desmarcada. Consulte o arquivo de ajuda para obter informações adicionais sobre esse recurso.

Repita estas etapas para adicionar outros clientes LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Configurar a conexão do diretório LDAP

Quando o Azure multi-Factor Authentication é configurado para receber autenticações LDAP, ele deve fazer o proxy dessas autenticações para o diretório LDAP. Portanto, o guia Destino exibe apenas uma única opção esmaecida para usar um destino LDAP.

1. Para configurar a conexão do diretório LDAP, clique no ícone **Integração de Diretórios**.
2. Na guia Configurações, selecione o botão de opção **Usar configuração de LDAP específica**.
3. Selecione **Editar...**
4. Na caixa de diálogo Editar Configuração de LDAP, popule os campos com as informações exigidas para se conectar ao diretório LDAP. As descrições dos campos estão incluídas no arquivo de ajuda do servidor da Autenticação Multifator do Azure.

    ![Integração de diretórios](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)

5. Teste a conexão LDAP clicando no botão **Testar**.
6. Se o teste de conexão LDAP foi bem-sucedido, clique no botão **OK**.
7. Clique na guia **Filtros**. O Servidor está pré-configurado para carregar contêineres, usuários e grupos de segurança do Active Directory. Se estiver se associando a um diretório LDAP diferente, provavelmente será necessário editar os filtros exibidos. Clique no link **Ajuda** para saber mais sobre filtros.
8. Clique na guia **Atributos** . O servidor está pré-configurado para mapear os atributos do Active Directory.
9. Se você estiver se associando a um diretório LDAP diferente ou para alterar os mapeamentos de atributos pré-configurados, clique no botão **Editar…**
10. Na caixa de diálogo Editar Atributos, modifique os mapeamentos de atributos LDAP para seu diretório. Os nomes de atributo podem ser digitados ou selecionados clicando no botão **...** ao lado de cada campo. Clique no link **Ajuda** para saber mais sobre atributos.
11. Clique no botão **OK**.
12. Em seguida, clique no ícone **Configurações da Empresa** e selecione a guia **Resolução de Nome de Usuário**.
13. Se estiver se conectando ao Active Directory de um servidor ingressado em domínio, deixe o botão de opção **Usar os identificadores de segurança do Windows (SIDs) para nomes de usuário correspondentes** selecionado. Caso contrário, selecione o botão de opção **Usar atributo de identificador exclusivo LDAP para correspondência de nomes de usuário**. 

Quando o botão de opção **Usar atributo de identificador exclusivo LDAP para correspondência de nomes de usuário** selecionado, o Servidor de Autenticação Multifator do Azure tenta resolver cada nome de usuário para um identificador exclusivo no diretório LDAP. Uma pesquisa LDAP é executada nos atributos de nome de usuário definidos na guia Integração do Diretório -> Atributos. Quando um usuário é autenticado, o nome de usuário é resolvido para o identificador exclusivo no diretório LDAP e o identificador exclusivo é usado para corresponder ao usuário no arquivo de dados do Autenticação Multifator do Azure. Isso permite comparações que diferenciam maiúsculas de minúsculas, bem como formatos de nome de usuário curtos e longos.

Depois de concluir estas etapas, o Servidor MFA começará a escutar nas portas configuradas para solicitações de acesso LDAP dos clientes configurados e estará definido como proxy dessas solicitações ao diretório LDAP para autenticação.

## <a name="ldap-client-configuration"></a>Configuração do cliente LDAP
Para configurar o cliente LDAP, use as diretrizes:

* Configure seu dispositivo, servidor ou aplicativo para autenticar via LDAP para o Servidor de Autenticação Multifator do Azure como se fosse seu diretório LDAP. Use as mesmas configurações que normalmente usa para se conectar diretamente ao diretório LDAP, exceto o nome do servidor ou endereço IP que será o Servidor de Autenticação Multifator do Azure.
* Configure o tempo limite LDAP para 30 a 60 segundos, de forma que haja tempo para validar as credenciais do usuário com o diretório LDAP, executar a verificação de segunda etapa, receber a resposta e responder à solicitação de acesso LDAP.
* Se estiver usando LDAPS, o dispositivo ou o servidor que faz as consultas LDAP deve confiar no certificado SSL instalado no servidor Azure Multi-Factor Authentication.


