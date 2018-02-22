---
title: Conectar uma conta do Amazon Web Services ao Gerenciamento de Custos do Azure | Microsoft Docs
description: "Conectar uma conta do Amazon Web Services para exibição dos dados de uso e custo em relatórios do Gerenciamento de Custos."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/08/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: a82d803b51859f809ca5a39ce177697a1f66a008
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="connect-an-amazon-web-services-account"></a>Conectar uma conta do Amazon Web Services

Você tem duas opções para conectar sua conta do AWS (Amazon Web Services) ao Gerenciamento de Custos do Azure. Você pode conectar a uma função IAM ou a uma conta de usuário IAM somente leitura. A função IAM é recomendada porque permite que você delegue o acesso com permissões definidas para entidades confiáveis. A função IAM não requer que você compartilhe chaves de acesso de longo prazo. Após conectar uma conta AWS ao Gerenciamento de Custos, os dados de uso e custo estão disponíveis nos relatórios de Gerenciamento de Custos. Este documento irá guiá-lo através de ambas as opções.

Para obter mais informações sobre as identidades IAM do AWS, consulte [Identidades (Usuários, Grupos e Funções)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

## <a name="aws-role-based-access"></a>Acesso baseado em função do AWS

As seções a seguir detalham como criar uma função IAM somente leitura para fornecer acesso ao Gerenciamento de Custos.

### <a name="get-your-cost-management-account-external-id"></a>Obtenha a ID externa da sua conta de Gerenciamento de Custos

A primeira etapa é obter a frase secreta de conexão exclusiva do portal de Gerenciamento de Custos do Azure. Ela é usada em AWS como a **ID Externa**.

1. Abra o portal Cloudyn a partir do Portal do Azure ou navegue até  [https://azure.cloudyn.com](https://azure.cloudyn.com) e faça logon.
2. Clique em **Configurações** (ícone de engrenagem) e selecione **Contas do Cloud**.
3. Em Gerenciamento de Contas, selecione a guia **Contas do AWS** e, em seguida, clique em **Adicionar novo +**.
4. Na caixa de diálogo **Adicionar Conta do AWS**, copie a **ID Externa** e salve esse valor para as etapas de criação da Função do AWS na próxima seção. Na imagem a seguir, a ID do exemplo é _Cloudyn_. Sua ID é diferente.  
    ![ID Externa](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Adicionar o acesso baseado em função somente leitura do AWS

1. Entre no console do AWS em https://console.aws.amazon.com/iam/home e selecione **Funções**.
2. Clique em **Criar Função** e, em seguida, selecione **Outra Conta do AWS**.
3. Cole a identidade `432263259397` no campo **ID da Conta**. Essa ID da Conta é a conta do coletor de dados do Gerenciamento de Custos que você deverá usar.
4. Próximo a **Opções**, selecione **Exigir ID externa** e, em seguida, cole o valor copiado anteriormente no campo **ID Externa** e clique em **Next: Permissions**.  
    ![Criar função](./media/connect-aws-account/create-role01.png)
5. Em **Anexar políticas de permissões**, na pesquisa de caixa de filtros **Tipo de política** digite `ReadOnlyAccess`, selecione **ReadOnlyAccess** e, em seguida, clique em **Next: Review**.  
    ![Acesso somente leitura](./media/connect-aws-account/readonlyaccess.png)
6. Na página de Revisão, assegure-se de que suas seleções estão corretas e digite um **Nome da função**. Por exemplo, *Azure-Cost-Mgt*. Insira uma **Descrição da função**. Por exemplo, _Atribuição de função do Gerenciamento de Custos do Azure_ e, em seguida, clique em **Criar função**.
7. Na lista **Funções**, clique na função criada e copie o valor da **ARN da Função** na página Resumo. Use o valor ARN da Função posteriormente ao registrar sua configuração no Gerenciamento de Custos do Azure.  
    ![ARN da Função](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Configurar o acesso da função do IAM do AWS no Gerenciamento de Custos

1. Abra o portal Cloudyn a partir do Portal do Azure ou navegue até  https://azure.cloudyn.com/ e faça logon.
2. Clique em **Configurações** (ícone de engrenagem) e selecione **Contas do Cloud**.
3. Em Gerenciamento de Contas, selecione a guia **Contas do AWS** e, em seguida, clique em **Adicionar novo +**.
4. Em **Nome da Conta**, digite um nome para a conta.
5. Próximo ao **Tipo de Acesso**, selecione **Função do IAM**.
6. No campo **ARN da Função**, cole o valor copiado anteriormente e, em seguida, clique em **Salvar**.  
    ![Status da conta do AWS](./media/connect-aws-account/aws-account-status01.png)

A conta do AWS aparece na lista de contas. A **ID do Proprietário** listada corresponde ao valor ARN da Função. O **Status da Conta** deverá ter um símbolo de marca de seleção verde.

O Gerenciamento de Custos começa coletando dados e relatórios de preenchimento. No entanto, alguns relatórios de Otimização poderão exigir dados de alguns dias antes de serem geradas recomendações precisas.

## <a name="aws-user-based-access"></a>Acesso baseado no usuário do AWS

As seções a seguir detalham como criar um usuário somente leitura para fornecer acesso ao Gerenciamento de Custos.

### <a name="add-aws-read-only-user-based-access"></a>Adicionar acesso baseado em usuário somente leitura do AWS

1. Entre no console do AWS em https://console.aws.amazon.com/iam/home e selecione **Usuários**.
2. Clique em **Adicionar Usuário**.
3. No campo **Nome de usuário**, digite um nome de usuário.
4. Para **Tipo de Acesso**, selecione **Acesso programático** e clique em **Next: Permissions**.  
    ![Adicionar usuário](./media/connect-aws-account/add-user01.png)
5. Para permissões, selecione **Anexar políticas existentes diretamente**.
6. Em **Anexar políticas de permissões**, na pesquisa de caixa de filtros **Tipo de política** digite `ReadOnlyAccess`, selecione **ReadOnlyAccess** e, em seguida, clique em **Next: Review**.  
    ![Definir permissões para o usuário](./media/connect-aws-account/set-permission-for-user.png)
7. Na página de Revisão, assegure-se de que suas seleções estão corretas e, em seguida, clique em **Criar usuário**.
8. Página de Conclusão, sua ID de Chave de Acesso e a tecla de acesso Secreta são mostradas. Essas informações são usadas para configurar o registro no Gerenciamento de Custos.
9. Clique em **Baixar CSV** e salve o arquivo credentials.csv em um local seguro.  
    ![Baixar credenciais](./media/connect-aws-account/download-csv.png)


### <a name="configure-aws-iam-user-based-access-in-cost-management"></a>Configurar o acesso baseado no usuário do AWS do IAM no Gerenciamento de Custos

1. Abra o portal Cloudyn no Portal do Azure ou navegue até https://azure.cloudyn.com/ e faça logon.
2. Clique em **Configurações** (ícone de engrenagem) e selecione **Contas do Cloud**.
3. Em Gerenciamento de Contas, selecione a guia **Contas do AWS** e, em seguida, clique em **Adicionar novo +**.
4. Para **Nome da Conta**, digite um nome da conta.
5. Próximo ao **Tipo de Acesso**, selecione o **Usuário do IAM**.
6. Em **Chave de Acesso**, cole o valor da **ID da chave de acesso**  do arquivo credentials.csv.
7. Em **Chave Secreta**, cole o valor da **Tecla de acesso secreta**  do arquivo credentials.csv e, em seguida, clique em **Salvar**.  
    ![Status da conta de usuário do AWS](./media/connect-aws-account/aws-user-account-status.png)

A conta do AWS aparece na lista de contas. O **Status da Conta** deverá ter um símbolo de marca de seleção verde.

O Gerenciamento de Custos começa coletando dados e relatórios de preenchimento. No entanto, alguns relatórios de Otimização poderão exigir dados de alguns dias antes de serem geradas recomendações precisas.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o Gerenciamento de Custos do Azure pelo Cloudyn, continue com o tutorial [Analisar o uso e os custos](tutorial-review-usage.md) para Gerenciamento de Custos.
