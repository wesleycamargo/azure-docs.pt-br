---
title: Instalar e configurar a integração de relatório de uso e custo do AWS no gerenciamento de custos do Azure
description: Este artigo orienta você, embora a instalação e configuração de integração de relatório de uso e custo do AWS no gerenciamento de custos do Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: e87e95ec9e4e20ee4785c2b1f448a7ca5f442b8a
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409204"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Instalar e configurar a integração de relatório de uso e custo do AWS

Com a integração de relatório de uso e custo do Amazon Web Services, você pode monitorar e controlar seus gastos AWS no gerenciamento de custos do Azure. A integração permite que um único local no portal do Azure onde você pode monitorar e controle de gastos para o Azure e AWS. Este artigo explica como configurar a integração e configurá-lo para que você usar os recursos de gerenciamento de custos para analisar os custos e revisar os orçamentos.

O relatório de uso e custo do AWS armazenado em um bucket S3 usando suas credenciais de acesso do AWS para obter definições de relatório e baixar arquivos GZIP CSV do relatório de custo processos de gerenciamento.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Criar um relatório de uso e custo no AWS

Usar um relatório de uso e custo é a maneira recomendada de AWS para coletar e processar os custos do AWS. Para obter mais informações, consulte o [relatório de uso e custo do AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) artigo de documentação.

Use o **relatórios** página do console do gerenciamento de custo e cobrança no AWS para criar um relatório de uso e custo com as etapas a seguir.

1. Entre no console de gerenciamento do AWS e abra o console de gerenciamento de custo e cobrança no https://console.aws.amazon.com/billing.
2. No painel de navegação, clique em **relatórios**.
3. Clique em **criar relatório**.
4. Para **nome do relatório**, digite um nome para seu relatório.
5. Para **unidade de tempo**, escolha **por hora**.
6. Para **Include**, adicione as IDs de cada recurso no relatório e selecione **IDs de recurso**.
7. Para **habilitar o suporte para**, nenhuma seleção é necessária.
8. Para **configurações de atualização de dados**, selecione **atualizar automaticamente seu custo &amp; fechado de relatório de uso, quando os encargos são detectados para os meses anteriores com letras**.
9. Clique em **Avançar**.
10. Para **bucket do Amazon S3**, digite o nome do bucket do Amazon S3 em que você deseja obter relatórios entregues a e clique em **verificar**. O número de buckets deve ter as permissões apropriadas para ser válido. Para obter mais informações sobre como adicionar permissões para o bucket, consulte [Bucket de configuração e permissões de acesso do objeto](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/set-permissions.html).
11. Para **prefixo de caminho do relatório**, digite o prefixo de caminho de relatório que você deseja ser adicionado ao nome do seu relatório.
12. Para **compactação**, selecione **GZIP**.
13. Clique em **Avançar**.
14. Depois de examinar as configurações para o relatório, clique em **revisão e concluir**.
    Observe a **nome do relatório**. Você o usará em etapas posteriores.

Pode levar até 24 horas para AWS para começar a entrega de relatórios para o bucket do Amazon S3. Após a entrega é iniciado, o AWS atualiza os arquivos de relatório de uso e custo do AWS pelo menos uma vez por dia. Você pode continuar a configurar o ambiente do AWS sem aguardar a entrega iniciar.

## <a name="create-a-role-and-policy-in-aws"></a>Criar uma função e a política no AWS

O gerenciamento de custos do Azure acessa o bucket S3 em que o relatório de uso e custo está localizado várias vezes ao dia. Gerenciamento de custos precisa acessar as credenciais para verificar se há novos dados. Criar uma função e a política no AWS para permitir o acesso pelo gerenciamento de custos.

Para habilitar o acesso baseado em função para uma conta do AWS no gerenciamento de custos do Azure, a função é criada no console do AWS. Você precisa ter o _arn da função_ e _ID externa_ do console do AWS. Posteriormente, você usá-los no criar uma página de conector do AWS no gerenciamento de custos do Azure.

Use o Assistente de criação de nova função:

1. Entrar no seu console do AWS e selecione **Services**.
2. Na lista de serviços, selecione **IAM**.
3. Selecione **funções** e, em seguida, clique em **Create Role**.
4. Na próxima página, selecione **outra conta do AWS**.
5. Na **ID da conta** , insira _432263259397_.
6. Na **opções**, selecione **exigir ID externa (melhor prática quando um terceiro assumirá que essa função)**.
7. Na **ID externa**, insira a ID externa. A ID externa é uma senha compartilhada entre a função do AWS e o gerenciamento de custos do Azure. A mesma ID externa também é usada na página novo conector no gerenciamento de custos. Por exemplo, se parece com uma ID externa _Companyname1234567890123_.
    Não altere a seleção para **exigir MFA**. Ele deve permanecer desmarcado.
8. Clique em **Avançar: Permissões.**
9. Clique **Criar política**. Uma nova guia do navegador é aberta em que você cria uma nova política.
10. Clique em **escolher um serviço**.

Configure o relatório de uso e custo de permissão:

1. Tipo de **relatório de uso e custo**.
2. Selecione **nível de acesso**, **leitura** > **DescribeReportDefinitions**. Isso permite que o gerenciamento de custos ler o que informa CUR são definidos e determinar se eles correspondem o pré-requisito de definição de relatório.
3. Clique em **adicionar permissões adicionais**.

Configure sua permissão de objetos e o bucket S3:

1. Clique em **escolher um serviço**.
2. Tipo de _S3_.
3. Selecione **nível de acesso**, **lista** > **ListBucket**. Esta ação obtém a lista de objetos no Bucket de S3.
4. Selecione **nível de acesso**, **leitura** > **GetObject**. Essa ação permite que o download de arquivos de cobrança.
5. Selecione **recursos**.
6. Selecione **bucket – adicionar ARN**.
7. Na **nome do Bucket**, insira o número de buckets usado para armazenar os arquivos CUR.
8. Selecione **objeto – adicionar ARN**.
9. Na **nome do Bucket**, insira o número de buckets usado para armazenar os arquivos CUR.
10. Na **nome do objeto**, selecione **qualquer**.
11. Clique em **adicionar permissões adicionais**.

Configure a permissão de Gerenciador de custo:

1. Clique em **escolher um serviço**.
2. Tipo de _serviço do Gerenciador de custo_.
3. Selecione **ações de todos os serviço de Gerenciador de custo (ce:\*)**. Esta ação valida se a coleção está correta.
4. Clique em **adicionar permissões adicionais**.

Adicione permissão de organizações:

1. Tipo de **organizações**.
2. Selecione **nível de acesso, lista** > **ListAccounts**. Esta ação obtém os nomes das contas.
3. Na **examinar política**, insira um nome para a nova política. Verificação para certificar-se de que você inseriu as informações corretas e, em seguida, clique em **Criar política**.
4. Volte à guia anterior e atualize a página do seu navegador da web. Na barra de pesquisa, procure a nova política.
5. Selecione **próxima: revisão**.
6. Insira um nome para a nova função. Verificação para certificar-se de que você inseriu as informações corretas e, em seguida, clique em **Create Role**.
    Observe a **arn da função** e o **ID externa** usado nas etapas anteriores, quando você criou a função. Você irá usá-los posteriormente quando você configura o conector de gerenciamento de custos do Azure.

A política JSON deve se parecer com o exemplo a seguir. Substitua _bucketname_ com o nome do seu bucket de S3.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>Configurar um novo conector do AWS no Azure

Use as informações a seguir para criar um novo conector do AWS e começar a monitorar os custos do AWS.

1. Entrar no portal do Azure em https://portal.azure.com.
2. Navegue até **gerenciamento + cobrança de custos** > **gerenciamento de custos**.
3. Sob **as configurações**, clique em **conectores (visualização) de nuvem**.  
    ![Exemplo mostrando os conectores de nuvem (configuração de versão prévia)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Clique em **+ adicionar** na parte superior da página para criar um novo conector.
5. Em criar uma página de conector do AWS, digite uma **nome de exibição** para nomear seu conector.  
    ![Exemplo de como criar uma página de conector do AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Opcionalmente, selecione o grupo de gerenciamento padrão. Ele armazenará descobertas todas as contas vinculadas. Você pode configurá-lo mais tarde.
7. Sob o **cobrança** seção, selecione **cobrar automaticamente o % 1 na disponibilidade geral** se você quiser garantir a operação contínua quando o preview expira. Se você selecionar a opção automática, você deve selecionar uma cobrança **assinatura**.
8. Insira o **arn da função**. É o valor que você usou quando definiu a função no AWS.
9. Insira o **ID externa**. É o valor que você usou quando definiu a função no AWS.
10. Insira o **nome do relatório** que você criou no AWS.
11. Clique em **próxima** e, em seguida, clique em **criar**.

Pode levar algumas horas para novos escopos AWS, a conta do AWS consolidados e contas vinculadas do AWS e seus dados de custo são exibidos.

Depois de criar o conector, é recomendável que você atribua o controle de acesso para o conector. Os usuários recebem permissões para os escopos de descoberta recentemente: AWS consolidados conta e o AWS contas vinculadas. O usuário que cria o conector é o proprietário do conector, conta consolidada e contas de todos os links.

Atribuindo permissões de conector aos usuários após a descoberta não atribuir permissões para os escopos do AWS existentes. Em vez disso, somente novas contas vinculadas são atribuídas permissões.

## <a name="additional-steps"></a>Etapas adicionais

- [Configurar grupos de gerenciamento](../governance/management-groups/index.md#initial-setup-of-management-groups), se você ainda não fez isso.
- Verifique se novos escopos são adicionados ao seu seletor de escopo. Não se esqueça de clicar **Refresh** para exibir os dados mais recentes.
- Na página de conector de nuvem, selecione o conector e clique em **ir para a conta de cobrança** para atribuir a conta vinculada a grupos de gerenciamento.

## <a name="manage-cloud-connectors"></a>Gerenciar conectores de nuvem

Quando você seleciona um conector na página de conectores de nuvem, você pode:

- Clique em **ir para a conta de cobrança** para exibir informações de conta do AWS consolidados.
- Clique em **controle de acesso** para gerenciar a atribuição de função para o conector.
- Clique em **editar** para atualizar o conector. Você não pode alterar o número de conta do AWS, como ele aparece no arn da função. No entanto, você pode criar um novo conector.
- Clique em **Verify** para executar novamente o teste de verificação para certificar-se de que o gerenciamento de custos pode coletar dados usando as configurações do conector.

![Exemplo mostrando a lista de conectores criados do AWS](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="role-of-azure-management-groups"></a>Função de grupos de gerenciamento do Azure

Para criar um único lugar para exibir informações do provedor de nuvem, você precisa colocar suas assinaturas do Azure e contas do AWS vinculados no mesmo grupo de gerenciamento. Se você ainda não tiver configurado seu ambiente do Azure com grupos de gerenciamento, consulte [instalação de grupos de gerenciamento inicial](../governance/management-groups/index.md#initial-setup-of-management-groups).

Se você desejar separar os custos, você pode criar um grupo de gerenciamento que contém apenas as contas do AWS vinculado.

## <a name="aws-consolidated-account"></a>Conta AWS consolidados

A conta AWS consolidado é usada para consolidar a cobrança e pagamento para várias contas do AWS. Sua conta do AWS consolidados também atua como uma conta vinculada do AWS.

![Exemplo dos detalhes da conta do AWS consolidados](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Na página, você pode:

- Clique em **atualizar** para atualização em massa a associação de contas AWS vinculado com um grupo de gerenciamento.
- Clique em **controle de acesso** para definir a atribuição de função para o escopo.

### <a name="aws-consolidated-account-permissions"></a>Permissões de conta do AWS consolidados

Por padrão, o AWS consolidados conta as permissões são definidas no momento da criação, com base nas permissões de conector do AWS. O criador do conector é o proprietário.

Você pode gerenciar o nível de acesso usando a página de nível de acesso da conta AWS consolidados. No entanto, as permissões para o AWS consolidados conta não são herdados por contas vinculadas do AWS.

## <a name="aws-linked-account"></a>Conta vinculada do AWS

A conta vinculada do AWS é onde os recursos do AWS são criados e gerenciados. Uma conta vinculada também atua como um limite de segurança.

Nessa página, você pode:

- Clique em **atualizar** para atualizar a associação da conta AWS vinculado com um grupo de gerenciamento.
- Clique em **controle de acesso** para definir uma atribuição de função para o escopo.

![Exemplo de página da conta vinculada do AWS](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="aws-linked-account-permissions"></a>Permissões de conta do AWS vinculado

Por padrão, as permissões de conta AWS vinculados são definidas no momento da criação, com base nas permissões de conector do AWS. O criador do conector é o proprietário. Você pode gerenciar o nível de acesso usando a página de nível de acesso da conta AWS vinculado. Permissões para a conta AWS consolidados não são herdadas por contas do AWS vinculado.

As contas vinculadas do AWS sempre herdam permissões do grupo de gerenciamento pertencem.

## <a name="next-steps"></a>Próximas etapas

- Agora que você ter instalado e configurado a integração de relatório de uso e custo do AWS, vá para [uso e os custos do AWS gerenciar](aws-integration-manage.md).
- Se você estiver familiarizado com a análise de custo, consulte [explorar e analisar os custos com análise de custo](quick-acm-cost-analysis.md) guia de início rápido.
- Se você estiver familiarizado com orçamentos no Azure, consulte [criar e gerenciar orçamentos Azure](tutorial-acm-create-budgets.md) tutorial.
