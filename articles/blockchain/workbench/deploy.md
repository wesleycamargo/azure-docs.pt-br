---
title: Implantar o Azure Blockchain Workbench
description: Como implantar o Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 3ee169e4139f5fc9cd4208c53c4997d50521fed7
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48240488"
---
# <a name="deploy-azure-blockchain-workbench"></a>Implantar o Azure Blockchain Workbench

O Azure Blockchain Workbench é implantado usando um modelo de solução no Azure Marketplace. O modelo simplifica a implantação de componentes necessários para criar aplicativos de blockchain. Uma vez implantado, o Blockchain Workbench fornece acesso aos aplicativos de cliente para criar e gerenciar usuários e aplicativos de blockchain.

Para obter mais informações sobre os componentes do Blockchain Workbench, consulte [Arquitetura do Azure Blockchain Workbench](architecture.md).

## <a name="prepare-for-deployment"></a>Preparar para a implantação

O Workbench Blockchain permite que você implante uma razão blockchain juntamente com um conjunto de serviços do Microsoft Azure relevante geralmente usado para criar um aplicativo baseado em blockchain. Implantar o Blockchain Workbench resulta nos seguintes serviços do Azure que estão sendo provisionados dentro de um grupo de recursos em sua assinatura do Azure.

* 1 Tópico de Grade de Eventos do Azure
* 1 Namespace do barramento de serviço
* 1 Application Insights
* 1 Banco de Dados SQL (Standard S0)
* 2 Serviços de Aplicativos (Standard)
* 2 Cofres de Chave do Microsoft Azure
* 2 Contas de Armazenamento do Microsoft Azure (Standard LRS)
* 2 Conjuntos de escala de Máquina Virtual (para nós de validação e de trabalho)
* 2 redes virtuais (incluindo o balanceador de carga, o grupo de segurança de rede e o endereço IP público para cada rede virtual)
* Opcional: Azure Monitor

A seguir está um exemplo de implantação criado no grupo de recursos do **myblockchain**.

![Exemplo de implantação](media/deploy/example-deployment.png)

O custo do Blockchain Workbench é uma agregação do custo de serviços subjacentes do Azure. Informações sobre preços para serviços do Azure podem ser calculadas usando a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/).

O Azure Blockchain Workbench requer vários pré-requisitos antes da implantação. Os pré-requisitos incluem registros de configuração e aplicativo do Microsoft Azure Active Directory.

### <a name="blockchain-workbench-api-app-registration"></a>Registro do aplicativo de API do Blockchain Workbench

A implantação do Blockchain Workbench exige o registro de um aplicativo do Microsoft Azure Active Directory. Você precisa de um locatário do Microsoft Azure Active Directory (Azure AD) para registrar o aplicativo. Você pode usar um locatário existente ou criar um novo locatário. Se você estiver usando um locatário existente do Azure AD, precisará de permissões suficientes para registrar aplicativos e conceder permissões da API do Graph em um locatário do Azure AD. Se você não tiver permissões suficientes em um locatário existente do Azure AD, crie um novo locatário. 

> [!IMPORTANT]
> O Workbench não precisa ser implantado no mesmo locatário que está sendo utilizado para registrar um aplicativo do Azure AD. O Workbench deve ser implantado em um locatário no qual você tenha permissões suficientes para implantar recursos. Para obter mais informações sobre os locatários do Microsoft Azure Active Directory, consulte [Como obter um locatário do Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) e [Integrando aplicativos com o Microsoft Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione sua conta no canto superior direito e alterne para o locatário do Microsoft Azure Active Directory desejado. O locatário deve ser o locatário do administrador da assinatura onde o Workbench é implantado e você tem permissões suficientes para registrar aplicativos.
3. No painel de navegação esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Registros do aplicativo** > **Novo registro de aplicativo**.

    ![Registro do aplicativo](media/deploy/app-registration.png)

4. Forneça um **Nome** e uma **URL de Logon** para o aplicativo. Você pode usar valores de espaço reservado, pois os valores são alterados durante a implantação. 

    ![Criar o Registro do aplicativo](media/deploy/app-registration-create.png)

    |Configuração  | Valor  |
    |---------|---------|
    |NOME | `Blockchain API` |
    |Tipo de aplicativo |Aplicativo Web / API|
    |URL de logon | `https://blockchainapi` |

5. Selecione **Criar** para registrar o aplicativo do Microsoft Azure Active Directory.

### <a name="modify-application-manifest"></a>Modificar o manifesto do aplicativo

Em seguida, você precisa modificar o manifesto do aplicativo para usar as funções de aplicativo no Microsoft Azure Active Directory para especificar administradores do Blockchain Workbench.  Para obter mais informações sobre manifestos de aplicativos, consulte [Manifesto de aplicativo do Microsoft Azure Active Directory](../../active-directory/develop/reference-app-manifest.md).

1. Para o aplicativo que você registrou, selecione **Manifesto** no painel de detalhes do aplicativo registrado.
2. Gere um GUID. Você pode gerar um GUID usando o comando do PowerShell [guid] :: NewGuid () ou o cmdlet New-GUID. Outra opção é usar um site gerador de GUID.
3. Você atualizará a seção **appRoles** do manifesto. No painel de Editar manifesto, selecione **Editar** e substitua `"appRoles": []` pelo JSON fornecido. Certifique-se de substituir o valor do campo **id** pelo GUID gerado. 

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    ![Editar manifesto](media/deploy/edit-manifest.png)

    > [!IMPORTANT]
    > O valor de **Administrador** é necessário para identificar administradores do Blockchain Workbench.

4.  Clique em **Salvar** para salvar as alterações do manifesto do aplicativo.

### <a name="add-graph-api-required-permissions"></a>Adicionar permissões necessárias de API do Graph

O aplicativo de API precisa solicitar a permissão do usuário para acessar o diretório. Defina a seguinte permissão necessária para o aplicativo de API:

1. No registro do aplicativo de API de Blockchain, selecione **Configurações > Permissões necessárias > Selecionar uma API > Microsoft Graph**.

    ![Selecionar uma API](media/deploy/client-app-select-api.png)

    Clique em **Selecionar**.

2. Em **Habilitar acesso** em **Permissões de aplicativo**, escolha **Ler os perfis completos de todos os usuários**.

    ![Habilitar acesso](media/deploy/client-app-read-perms.png)

    Clique em **Selecionar** e, em seguida, clique em **Concluído**.

3. Em **Permissões necessárias**, selecione **Conceder permissões** e, em seguida, selecione **Sim** na solicitação de verificação.

   ![Conceder permissões](media/deploy/client-app-grant-permissions.png)

   A concessão de permissão permite que o Blockchain Workbench acesse os usuários no diretório. A permissão de leitura é necessária para pesquisar e adicionar membros ao Blockchain Workbench.

### <a name="add-graph-api-key-to-application"></a>Adicionar chave de API do Graph ao aplicativo

O Blockchain Workbench usa o Microsoft Azure Active Directory como principal sistema de gerenciamento de identidade para os usuários que interagem com aplicativos de blockchain. Para que o Blockchain Workbench acesse o Microsoft Azure Active Directory e recupere informações de usuário, como nomes e emails, você precisa adicionar uma chave de acesso. O Blockchain Workbench usa a chave para autenticar com o Microsoft Azure Active Directory.

1. Para o aplicativo que você registrou, selecione **Configurações** no painel de detalhes do aplicativo registrado.
2. Selecione **Chaves**.
3. Adicione uma nova chave especificando uma **descrição** de chave e escolhendo o valor de duração **expira**. 

    ![Chave Create](media/deploy/app-key-create.png)

    |Configuração  | Valor  |
    |---------|---------|
    | DESCRIÇÃO | `Service` |
    | Expira | Escolha uma duração de expiração |

4. Clique em **Salvar**. 
5. Copie o valor da chave e guarde-o para mais tarde. Você precisará dele para a implantação.

    > [!IMPORTANT]
    >  Se você não salvar a chave para a implantação, você precisará gerar uma nova chave. Você não pode recuperar o valor da chave pelo portal depois.

### <a name="get-application-id"></a>Obter ID do aplicativo

As informações de locatário e ID do aplicativo são necessárias para a implantação. Coletar e armazenar as informações para uso durante a implantação.

1. Para o aplicativo que você registrou, selecione **Configurações** > **Propriedades**.
2.  No painel **Propriedades**, copie e guarde os seguintes valores para uso posterior durante a implantação.

    ![Propriedades do aplicativo de API](media/deploy/app-properties.png)

    | Configurando para armazenar  | Usar na implantação |
    |------------------|-------------------|
    | ID do aplicativo | Configuração do Azure Active Directory > ID do aplicativo |

### <a name="get-tenant-domain-name"></a>Obter nome de domínio do locatário

Colete e guarde o nome de domínio do locatário do Active Directory em que os aplicativos são registrados. 

No painel de navegação esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Personalizar nomes de domínio**. Copie e guarde o nome de domínio.

![Nome de domínio](media/deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Implantar o Blockchain Workbench

Depois de concluir as etapas de pré-requisito, você estará pronto para implantar o Blockchain Workbench. As seções a seguir descrevem como implantar a estrutura.

1.  Entre no [Portal do Azure](https://portal.azure.com).
2.  Selecione sua conta no canto superior direito e alterne para o locatário desejado do Microsoft Azure Active Directory em que você deseja implantar o Azure Blockchain Workbench.
3.  No painel esquerdo, selecione **Criar um recurso**. Pesquise por `Azure Blockchain Workbench` na barra de pesquisa **Pesquisar no Marketplace**. 

    ![Barra de pesquisa do Marketplace](media/deploy/marketplace-search-bar.png)

4.  Selecione **Azure Blockchain Workbench**.

    ![Resultados de pesquisa do Marketplace](media/deploy/marketplace-search-results.png)

4.  Selecione **Criar**.
5.  Conclua as configurações básicas.

    ![Criar Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Configuração | DESCRIÇÃO  |
    |---------|--------------|
    | Prefixo de recursos | Identificador exclusivo curto para sua implantação. Esse valor é usado como base para nomear recursos. |
    | Nome de usuário da VM | O nome de usuário é usado como administrador para todas as máquinas virtuais (VM). |
    | Tipo de autenticação | Selecione se deseja usar uma senha ou chave para se conectar a VMs. |
    | Senha | A senha é usada para se conectar a VMs. |
    | SSH | Use uma chave pública RSA no formato de única linha começando com **ssh-rsa** ou use o formato PEM de várias linha. É possível gerar chaves SSH usando `ssh-keygen` no Linux e OS X ou usando PuTTYGen no Windows. Para obter mais informações sobre como usar chaves SSH, veja [Como usar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Senha do banco de dados / Confirmar senha do banco de dados | Especifique a senha a ser usada para acessar o banco de dados criado como parte da implantação. |
    | Resultados da implantação | Especifique onde implantar os recursos Blockchain Workbench. Para melhor disponibilidade, isso deve corresponder à configuração do **Local**. |
    | Assinatura | Especifique a assinatura do Azure que você deseja usar para sua implantação. |
    | Grupos de recursos | Crie um novo grupo de recursos selecionando **Criar novo** e especifique um nome exclusivo para o grupo de recursos. |
    | Local padrão | Especifique a região em que você deseja implantar a estrutura. |

6.  Selecione **OK** para concluir a seção de configuração básica.

7.  Conclua a **configuração do Azure Active Directory**.

    ![Configuração do Microsoft Azure Active Directory](media/deploy/blockchain-workbench-settings-aad.png)

    | Configuração | DESCRIÇÃO  |
    |---------|--------------|
    | Nome de domínio | Use o locatário do Microsoft Azure Active Directory coletado na seção de pré-requisito [Obter nome de domínio de locatário](#get-tenant-domain-name). |
    | ID do aplicativo | Use a ID do aplicativo do registro de aplicativo cliente de Blockchain coletada na seção de pré-requisito [Obter ID do aplicativo](#get-application-id). |
    | Chave do aplicativo | Use a chave do aplicativo do registro de aplicativo cliente de Blockchain coletada na seção de pré-requisito [Adicionar chave de API do Graph ao aplicativo](#add-graph-api-key-to-application). |


8.  Clique em **OK** para concluir a seção de configuração de Parâmetros do Microsoft Azure Active Directory.

9.  Em **Configurações de Rede e Desempenho**, escolha se você quer criar uma nova rede de blockchain ou usar uma rede de blockchain de prova de autoridade existente.

    Para **Criar novo**:

    A opção *criar novo* cria um conjunto de nós de PoA (prova de autoridade) do Ethereum dentro da assinatura de um único membro. 

    ![Configurações de rede e desempenho](media/deploy/blockchain-workbench-settings-network-new.png)

    | Configuração | DESCRIÇÃO  |
    |---------|--------------|
    | Número de nós de blockchain | Escolha o número de nós de validador Ethereum PoA a ser implantado em sua rede. |
    | Desempenho de armazenamento | Escolha o desempenho de armazenamento de VM preferido para sua rede de blockchain. |
    | Tamanho da máquina virtual | Escolha o tamanho de VM preferido para sua rede de blockchain. |

    Para **Usar existente**:

    A opção *usar existente* permite que você especifique uma rede de blockchain de PoA (Prova de Autoridade) do Ethereum. Os pontos de extremidade têm os seguintes requisitos.

    * O ponto de extremidade deve ser uma rede de blockchain de PoA (Prova de Autoridade) do Ethereum.
    * O ponto de extremidade deve estar publicamente acessível pela rede.
    * A rede de blockchain de PoA deve ser configurada para definir o preço do gás como zero (Nota: as contas do Blockchain Workbench não são financiadas. Se fundos forem necessários, as transações falharão).

    ![Configurações de rede e desempenho](media/deploy/blockchain-workbench-settings-network-existing.png)

    | Configuração | DESCRIÇÃO  |
    |---------|--------------|
    | Ponto de extremidade RPC do Ethereum | Fornecer o ponto de extremidade RPC de uma rede de blockchain de PoA existente. O ponto de extremidade começa com http:// e termina com um número da porta. Por exemplo, `http://contoso-chain.onmicrosoft.com:8545` |
    | Desempenho de armazenamento | Escolha o desempenho de armazenamento de VM preferido para sua rede de blockchain. |
    | Tamanho da máquina virtual | Escolha o tamanho de VM preferido para sua rede de blockchain. |

10. Selecione **OK** para concluir as configurações de rede e desempenho.

11. Conclua as configurações do **Azure Monitor**.

    ![Azure Monitor](media/deploy/blockchain-workbench-settings-oms.png)

    | Configuração | DESCRIÇÃO  |
    |---------|--------------|
    | Monitoramento | Escolha se você deseja que o Azure Monitor monitore a sua rede do blockchain |
    | Conecte-se à instância do Log Analytics existente | Escolha se você quer usar uma instância do Log Analytics existente ou criar uma nova. Se usar uma instância existente, insira a ID do workspace e a chave primária. |

12. Clique em **OK** para concluir a seção do Azure Monitor.

13. Revise o resumo para verificar se os parâmetros são precisos.

    ![Resumo](media/deploy/blockchain-workbench-summary.png)

14. Selecione **Criar** para concordar com os termos e implantar o Azure Blockchain Workbench.

A implantação pode levar até 90 minutos. Você pode usar o Portal do Azure para monitorar o progresso. No grupo de recursos criado recentemente, selecione **Implantações > Visão geral** para ver o status dos artefatos implantados.

## <a name="blockchain-workbench-web-url"></a>URL da Web do Blockchain Workbench

Concluída a implantação do Blockchain Workbench, um novo grupo de recursos conterá seus recursos do Blockchain Workbench. Os serviços do Blockchain Workbench são acessados por meio de uma URL da Web. As etapas a seguir mostram como recuperar a URL da Web do framework implantado.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No painel de navegação à esquerda, selecione **Grupos de recursos**
3. Escolha o nome do grupo de recursos especificado ao implantar o Blockchain Workbench.
4. Clique no título da coluna **TIPO** para classificar a lista em ordem alfabética por tipo.
5. Há dois recursos com o tipo **Serviço de Aplicativo**. Selecione o recurso do tipo **Serviço de Aplicativo** *sem* o sufixo "-api".

    ![Lista de Serviços de Aplicativo](media/deploy/resource-group-list.png)

6.  Na seção **Conceitos Básicos** do Serviço de Aplicativo, copie o valor da **URL**, que representa a URL da Web para o Blockchain Workbench implantado.

    ![Conceitos básicos do Serviço de Aplicativo](media/deploy/app-service.png)

Para associar um nome de domínio personalizado ao Blockchian Workbench, consulte [Configurando um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo do Azure usando o Gerenciador de Tráfego](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="configuring-the-reply-url"></a>Configurar a URL de resposta

Quando o Azure Blockchain Workbench tiver sido implantado, a próxima etapa será verificar se o aplicativo cliente do Microsoft Azure Active Directory (Azure AD) está registrado na correta **URL de resposta** da URL da Web do Blockchain Workbench implantado.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se que você está no locatário em que registrou o aplicativo cliente do Microsoft Azure Active Directory.
3. No painel de navegação esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Registros do Aplicativo**.
4. Selecione o aplicativo cliente do Microsoft Azure Active Directory registrado na seção de pré-requisitos.
5. Selecione **Configurações > URLs de resposta**.
6. Especifique a URL da Web principal da implantação do Azure Blockchain Workbench recuperada na seção **Obter a URL da Web do Azure Blockchain Workbench**. A URL de resposta é prefixada com `https://`. Por exemplo, `https://myblockchain2-7v75.azurewebsites.net`

    ![URLs de resposta](media/deploy/configure-reply-url.png)

7. Selecione **Salvar** para atualizar o registro do cliente.

## <a name="remove-a-deployment"></a>Remove uma implantação

Quando uma implantação não é mais necessária, você pode remover uma implantação, excluindo o grupo de recursos do Workbench Blockchain.

1. No portal do Azure, navegue até **Grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que você deseja excluir. 
2. Selecione **Excluir grupo de recursos**. Verifique a exclusão digitando o nome do grupo de recursos e selecione **Excluir**.

    ![Excluir grupo de recursos](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo de procedimentos, você implantou o Workbench do Azure Blockchain. Para saber como usar o aplicativo blockchain, continue no próximo artigo de instruções.

> [!div class="nextstepaction"]
> [Criar um aplicativo blockchain no Azure Blockchain Workbench](create-app.md)
