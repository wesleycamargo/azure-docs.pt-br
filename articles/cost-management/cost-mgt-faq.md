---
title: Perguntas frequentes sobre o Gerenciamento de Custos do Azure | Microsoft Docs
description: Fornece respostas para algumas perguntas comuns sobre o Gerenciamento de Custos do Azure.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 12/14/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: f62e5a224c2fb33714a80bc47b98238208b787e5
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="frequently-asked-questions-for-azure-cost-management"></a>Perguntas frequentes sobre o Gerenciamento de Custos do Azure

Este artigo aborda algumas perguntas comuns sobre o Gerenciamento de Custos do Azure (também conhecido como Cloudyn). Se você tiver dúvidas sobre o Gerenciamento de Custos, faça perguntas nas [Perguntas frequentes sobre o Gerenciamento de Custos do Azure do Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-azure-cost-management-by-cloudyn?forum=Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Como resolver problemas comuns de configuração de empresa indireta?

Ao usar o portal do Cloudyn pela primeira vez, você poderá ver as seguintes mensagens se for um usuário do Contrato Enterprise ou Provedor de Solução de Nuvem (CSP):

- "A chave de API especificada não é uma chave de registro de nível superior" exibido no assistente de **Configurar Gerenciamento de Custos do Azure**.
- "Inscrição direta – Não" exibido no portal do Contrato Enterprise.
- "Nenhum dado de uso encontrado nos últimos 30 dias. Entre em contato com o distribuidor para ter certeza de que a marcação foi habilitada para sua conta do Azure" exibido no portal do Cloudyn.

As mensagens anteriores indicam que você adquiriu um Contrato Enterprise do Azure por meio de um revendedor ou CSP. Seu revendedor ou CSP precisa habilitar o _markup_ para que você possa exibir os dados da conta do Azure no Cloudyn.

Veja como corrigir os problemas:

1. Seu revendedor precisa habilitar _marcação_ para sua conta. Para obter instruções, consulte o [Guia de integração de cliente indireto](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Você gera a chave de Contrato Enterprise do Azure para usar com o Cloudyn. Para obter as instruções, consulte [Adicionando o Azure EA](https://support.cloudyn.com/hc/en-us/articles/210429585-Adding-Your-AZURE-EA) ou [Como localizar sua ID de registro de EA e chave de API](https://youtu.be/u_phLs_udig).

Somente um administrador do serviço Azure pode habilitar o Gerenciamento de Custos. As permissões de coadministrador são insuficientes.

Habilite a API de cobrança do Azure antes de gerar a chave de API do Contrato Enterprise do Azure para configurar o Cloudyn seguindo as instruções em:

- [Visão geral das APIs de Relatórios para clientes Enterprise](../billing/billing-enterprise-api.md)
- [API de relatórios do Microsoft Azure Enterprise Portal](https://ea.azure.com/helpdocs/reportingAPI) em **Habilitando o acesso a dados para a API**


Você também precisa conceder permissões para administradores de departamento, proprietários de conta e administradores de empresa para _exibir encargos_ com a API de cobrança.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Por que não vejo as recomendações do Otimizador?

As informações de recomendação só estão disponíveis para as contas que estão ativadas. Você não verá as informações de recomendação nas categorias do relatório do **Otimizador** para as contas que estão *desativadas*, incluindo:

- Gerenciador de otimização
- Otimização de dimensionamento
- Ineficiências

Se você não consegue visualizar nenhum dado de recomendação do Otimizador, provavelmente, você tem contas que não estão ativadas. Para ativar uma conta, você precisa registrá-la com suas credenciais do Azure.

Para ativar uma conta:

1.  No portal da Cloudyn, clique em **Configurações** no canto superior direito e selecione **Contas de Nuvem**.
2.  Na guia Contas do Microsoft Azure, procure as contas que têm uma assinatura **desativada**.
3.  À direita de uma conta não ativada, clique no símbolo **editar** se parece com um lápis.
4.  Sua ID de locatário e a ID da taxa são detectadas automaticamente. Clique em **Próximo**.
5.  Você será redirecionado para o portal do Azure. Entre no portal e autorize o Coletor Cloudyn a acessar os dados do Azure.
6.  Em seguida, você será redirecionado para a página de gerenciamento das Contas do Cloudyn e sua assinatura será atualizada com o status de conta **ativo**. Ela mostra um símbolo de marca de seleção verde.
7.  Se você não vir um símbolo de marca de seleção verde para uma ou mais assinaturas, isso significa que você não tem permissões para criar um aplicativo de leitor (o CloudynCollector) para a assinatura. Um usuário com permissões mais altas para a assinatura precisa repetir as etapas 3 e 4.  

Depois de concluir as etapas anteriores, você pode exibir as recomendações do Otimizador dentro de um a dois dias. No entanto, pode levar até cinco dias antes que os dados de otimização completa fiquem disponíveis.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Como fazer para habilitar usuários suspensos ou bloqueados?

Se você receber um alerta solicitando para permitir o acesso de um usuário, precisará ativar a conta do usuário.

Para ativar a conta do usuário:

1. Entre no Cloudyn usando a conta de usuário administrativo do Azure que você usou para configurar o Cloudyn. Ou entre usando uma conta de usuário que recebeu acesso de administrador.

2. Selecione o símbolo de engrenagem no canto superior direito e selecione **Gerenciamento de Usuários**.

3. Localize o usuário, selecione o símbolo de lápis e edite o usuário.

4. Em **Status do usuário**, altere o status de **Suspenso** para **Ativo**.

As contas de usuário do Cloudyn se conectam usando o logon único do Azure. Se um usuário digitar incorretamente sua senha, ele poderá ficar bloqueado no Cloudyn mesmo que ainda possa acessar o Azure.

Se você alterar seu endereço de email no Cloudyn do endereço padrão do Azure, sua conta poderá ser bloqueada. Ela pode mostrar “status initiallySuspended.” Se sua conta de usuário estiver bloqueada, contate um administrador alternativo para redefinir a sua conta.

Recomendamos que você crie pelo menos duas contas de administrador do Cloudyn caso uma das contas fique bloqueada.

Se não puder entrar no portal do Cloudyn, verifique se você está usando a URL de Gerenciamento de Custos do Azure correta para entrar no Cloudyn. Use [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Evite usar a URL de direcionamento do Cloudyn https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Como ativar contas não ativadas com as credenciais do Azure?

Assim que suas contas do Azure são descobertas pelo Cloudyn, os dados de custo são fornecidos imediatamente nos relatórios baseados em custo. No entanto, para o Cloudyn fornecer dados de uso e desempenho, você precisa registrar suas credenciais do Azure das contas. Para obter instruções, consulte [Adicionar Azure Resource Manager](https://support.cloudyn.com/hc/en-us/articles/212784085-Adding-Azure-Resource-Manager).

Para adicionar as credenciais do Azure a uma conta, no portal do Cloudyn, selecione o símbolo de edição à direita do nome da conta, não da assinatura.

Até que suas credenciais do Azure sejam adicionadas ao Cloudyn, a conta aparece como _inativa_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Como adicionar várias contas e entidades a uma assinatura existente?

Outras entidades são usadas para adicionar outros Contratos Enterprise a uma assinatura do Cloudyn. Os links a seguir descrevem como adicionar outras entidades:

- Artigo [Adicionando uma entidade](https://support.cloudyn.com/hc/en-us/articles/212016145-Adding-an-Entity)
- Vídeo [Definindo sua hierarquia com Entidades de Custo](https://support.cloudyn.com/hc/en-us/articles/115005142529-Video-Defining-your-hierarchy-with-Cost-Entities)

Para os CSPs:

Para adicionar contas do CSP adicionais a uma entidade, selecione **Acesso MSP** em vez de **Enterprise** ao criar a nova entidade. Se sua conta está registrada como um Contrato Enterprise e você deseja adicionar as credenciais do CSP, a equipe de suporte do Cloudyn talvez precise modificar as configurações da conta. Se você for um assinante pago do Azure, poderá criar uma nova solicitação de suporte no portal do Azure. Selecione **Ajuda + suporte** e, em seguida, selecione **Nova solicitação de suporte**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Símbolos de moeda em relatórios do Cloudyn

Você pode ter várias contas do Azure usando moedas diferentes. No entanto, os relatórios de custos no Cloudyn não mostram mais de um tipo de moeda por relatório.

Se você tiver várias assinaturas usando moedas diferentes, uma entidade pai e suas moedas de entidades filho serão exibidas com o símbolo **$**. A melhor prática sugerida é evitar o uso de moedas diferentes na mesma hierarquia de entidades. Em outras palavras, todas as suas assinaturas organizadas em uma estrutura de entidade devem usar a mesma moeda.

O Cloudyn detecta a moeda da assinatura do Contrato Enterprise e apresenta-a da forma correta nos relatórios automaticamente.  No entanto, o Cloudyn apenas exibe o símbolo **$** para contas do Azure diretas da Web e de CSP.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Quais são os cronogramas de atualização de dados do Cloudyn?

O Cloudyn tem os seguintes cronogramas de atualização de dados:

- **Inicial**: pode levar até 24 horas para exibir dados de custo no Cloudyn depois de configurado. Também pode levar até 10 dias para o Cloudyn coletar dados suficientes para exibir as recomendações de dimensionamento.
- **Diariamente**: do décimo dia ao final de cada mês, o Cloudyn deve mostrar os dados atualizados do dia anterior até aproximadamente UTC+3 no dia seguinte.
- **Mensalmente**: do primeiro até o décimo dia de cada mês, o Cloudyn pode mostrar apenas os dados até o final do mês anterior.

O Cloudyn processa os dados do dia anterior, quando os dados completos do dia anterior estão disponíveis. Os dados do dia anterior ficam geralmente disponíveis no Cloudyn todos os dias por volta de UTC+3. Alguns dados, como marcas, podem levar mais 24 horas para serem processados.

Os dados para o mês atual não estão disponíveis para coleta no início de cada mês. Durante o período, os provedores de serviço finalizam sua cobrança do mês anterior. Os dados do mês anterior aparecem no Cloudyn de 5 a 10 dias após o início de cada mês. Durante esse tempo, talvez você veja apenas os custos amortizados do mês anterior. Talvez você não veja dados de cobrança ou de uso diário. Quando os dados se tornam disponíveis, o Cloudyn os processa retroativamente. Após o processamento, todos os dados mensais são exibidos entre o 5º e o 10º dia de cada mês.

Se houver um atraso no envio de dados do Azure para o Cloudyn, os dados serão registrados no Azure. Os dados são transferidos para o Cloudyn quando a conexão é restaurada.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Como um CSP direto pode configurar o acesso ao Cloudyn para clientes ou parceiros de CSP indireto?

Consulte [configurar acesso de CSP indireto no Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn) para obter instruções.

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>O que faz com que o item de menu Otimizador apareça?

Após adicionar o acesso do Azure Resource Manager e os dados forem coletados, você deverá visualizar a opção **Otimizador**. Para ativar o acesso do Azure Resource Manager, consulte [Como ativar contas não ativadas com as credenciais do Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cost-managementcloudyn-agent-based"></a>É baseado em agente de Gerenciamento de Custos/Cloudyn?

Nº Os agentes não são usados. Os dados da métrica da máquina virtual do Azure para VMs são coletados da API do Microsoft Insights. Se você quiser reunir dados de métrica de VMs do Azure, eles precisam ter as configurações de diagnóstico habilitadas.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Relatórios Cloudyn mostram mais de um locatário do AD por relatório?

Sim. É possível [criar uma correspondência de entidade de conta de nuvem](tutorial-user-access.md#create-entities) para cada locatário do AD que você possui. Então, você poderá visualizar todos os dados de locatário do Azure Active Directory e outros provedores de plataforma de nuvem, incluindo Amazon Web Services e Google Cloud Platform.
