---
Título: Implantar o serviço Web do Studio em várias regiões – titleSuffix: Descrição do Azure Machine Learning Studio: Etapas para implantar (copiar) um novo serviço Web para outras regiões. Implante um serviço Web com facilidade em várias regiões sem precisar de várias assinaturas ou workspaces.
serviços: machine-learning ms.service: machine-learning ms.component: studio ms.topic: artigo

autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 19/04/2017
---
# <a name="deploy-an-azure-machine-learning-studio-web-service-to-multiple-regions"></a>Implantar um serviço Web do Azure Machine Learning Studio em várias regiões

Os novos serviços Web do Azure permitem implantar facilmente um serviço Web do Azure Machine Learning Studio em várias regiões, sem precisar de várias assinaturas ou workspaces. 

O preço é específico de cada região; portanto, você deverá definir um plano de faturamento para cada região na qual implantará o serviço Web.

## <a name="to-create-a-plan-in-another-region"></a>Para criar um plano em outra região
1. Entre nos [serviços Web de Microsoft Azure Machine Learning](https://services.azureml.net/).
2. Clique na opção de menu **Planos** .
3. Na página de visão geral de Planos, clique em **Novo**.
4. No menu suspenso **Assinatura** , selecione a assinatura na qual o novo plano residirá.
5. No menu suspenso **Região** , selecione uma região para o novo plano. As opções de plano para a região selecionada serão exibidas na seção **Opções de Plano** da página.
6. No menu suspenso **Grupo de Recursos** , selecione um grupo de recursos para o plano. Em mais informações sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. Em **Nome do Plano** , digite o nome do plano.
8. Em **Opções do Plano**, clique no nível de cobrança para o novo plano.
9. Clique em **Criar**.

## <a name="deploying-the-web-service-to-another-region"></a>Implantando o serviço Web em outra região
1. Clique na opção de menu **Serviços Web** .
2. Selecione o serviço Web que você está implantando em uma nova região.
3. Clique em **Copiar**.
4. Em **Nome do Serviço Web**, digite um novo nome para o serviço Web.
5. Em **Descrição do serviço Web**, digite uma descrição para o serviço Web.
6. No menu suspenso **Assinatura** , selecione a assinatura na qual o novo serviço Web residirá.
7. No menu suspenso **Grupo de Recursos** , selecione um grupo de recursos para o serviço Web. Em mais informações sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. No menu suspenso **Região** , selecione a região na qual implantar o serviço Web.
9. No menu suspenso **Conta de armazenamento** , selecione uma conta de armazenamento na qual armazenar o serviço Web.
10. Na lista suspensa **Plano de preços** , selecione um plano na região que você selecionou na etapa 8.
11. Clique em **Copiar**.

