<properties
   pageTitle="Criação e registro da conta do editor | Microsoft Azure"
   description="Instruções para criar uma conta de Desenvolvedor da Microsoft para que, mediante aprovação, seja possível vender vários tipos de ofertas no Azure Marketplace."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/07/2016"
   ms.author="hascipio"/>

# Criar uma conta de Desenvolvedor da Microsoft
Este artigo fornece uma orientação sobre o processo necessário de criação e registro de uma conta para se tornar um Desenvolvedor da Microsoft aprovado para o Azure Marketplace.

## 1\. Criar uma conta da Microsoft
> [AZURE.WARNING] Para iniciar o processo de publicação, será necessário criar uma conta da Microsoft. Esta conta será usada para se registrar e entrar no **Centro de Desenvolvimento da Microsoft** e no **Painel de Publicação do Azure**. Você deve ter apenas uma conta da Microsoft para suas ofertas do Azure Marketplace. Ela não deve ser específica a serviços ou ofertas.

O endereço que forma o nome de usuário deve estar no seu domínio e ser controlado por sua equipe de TI (como publishing@example.com). Informações sobre pagamento e impostos, bem como relatórios serão encaminhados através desta conta.

  >[AZURE.WARNING] Palavras como "Azure" e "Microsoft" não são aceitas para o registro da conta da Microsoft. Evite usar essas palavras para concluir a criação da conta e o processo de registro.

### Instruções

1. Crie uma lista de distribuição (DL) ou grupo de segurança (SG) no domínio da empresa.
  - Adicione sua equipe de integração à DL.
  - A DL deve estar ativa para receber o email de confirmação.
  - Use marketplace@example.com como o endereço de email para a DL.
  - Isso precisa ser concluído em seus sistemas internos.
2. Abra uma nova sessão de Janela anônima do Chrome ou InPrivate do Internet Explorer para garantir que você não esteja conectado a uma conta existente.
3. Registre-se para uma conta da Microsoft usando o email da DL.
 - Você pode registrar uma conta da Microsoft em [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx).
 - Use marketplace@example.com como o endereço de email.
 - A ID de sua conta da Microsoft agora é marketplace@example.com.
4. Ao se registrar, use um número de telefone válido. O sistema enviará um código de verificação como mensagem de texto ou uma chamada automatizada se a verificação de identidade for necessária.
5. Verifique o endereço de email enviado à DL.
6. Agora, você está pronto para usar uma nova conta da Microsoft no Centro de Desenvolvimento da Microsoft.

> [AZURE.TIP] O uso da DL permite que várias pessoas recebam notificações por email importantes para a comunicação de informações de pagamento. Ela também garante que a propriedade da conta da Microsoft possa ser transferida e não esteja vinculada a uma única pessoa.

## 2\. Criar sua conta no Centro de Desenvolvimento da Microsoft
O Centro de Desenvolvimento da Microsoft é usada para registrar as informações da empresa uma vez. O inscrito deve ser um representante válido da empresa e fornecer suas informações pessoais como uma maneira de validar sua identidade. A pessoa que fizer o registro deverá usar uma conta da Microsoft que seja compartilhada com a empresa **e a mesma conta deve ser usada no Portal de Publicação do Azure**. Você deve verificar se sua empresa ainda não tem uma conta do Centro de Desenvolvimento da Microsoft antes de tentar criar uma. Durante o processo, coletaremos informações sobre o endereço da empresa, a conta bancária e as informações fiscais. Esses dados são geralmente obtidos por meio de contatos de finanças ou negócios.

> [AZURE.IMPORTANT] Você deve completar os seguintes componentes de perfil do Desenvolvedor para prosseguir pelas diversas fases da criação e implantação da oferta.


| Perfil do desenvolvedor | Para iniciar o rascunho | Staging | Publicar modelo gratuito e de solução | Publicar comercial |
|----|----|----|----|----|
|Registro da empresa | Imprescindível | Imprescindível | Imprescindível | Imprescindível |
|ID do perfil fiscal | Opcional | Opcional | Opcional | Imprescindível |
|Conta bancária | Opcional | Opcional | Opcional | Imprescindível |


> [AZURE.NOTE] Há suporte para BYOL (Traga sua Própria Licença) apenas para máquinas virtuais, e é considerado uma oferta **gratuita**.


### Registrar sua conta da empresa
1. Abra uma nova sessão de Janela anônima do Chrome ou InPrivate do Internet Explorer para garantir que você não esteja conectado a uma conta pessoal.

2. Acesse [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure).

3. Entre com a conta da Microsoft de registro de sua empresa (por exemplo, marketplace@example.com)).

    ![desenho][img-signin]

4. Conclua o assistente "Ajude-nos a proteger sua conta", que vai verificar sua identidade através do número de telefone ou endereço de email.

    ![desenho][img-verify]

5. Na seção "Informações de Registro de Conta", selecione o **País/região da conta** no menu suspenso.

    > [AZURE.WARNING] **Países para “Origem de venda”:** para vender seus serviços no Azure Marketplace, sua entidade registrada precisa ser de um dos países aprovados para "origem de venda". Essa restrição é por razões de pagamento e tributação. Estamos considerando a expansão dessa lista de países em um futuro próximo, portanto, fique atento. Para saber mais, confira as [políticas de participação no Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).

6. Selecione o "Tipo de conta", **Individual** ou **Empresa**.

    > [AZURE.IMPORTANT] Para entender melhor os tipos de conta e qual é a melhor para você, veja a página [Tipos de conta, locais e tarifas](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)

7. Insira o **Nome de exibição do publicador**, normalmente o nome de sua empresa.

    > [AZURE.TIP] Atualmente, o Portal de Publicação do Azure não usa o Nome de exibição do publicador. Mas isso deve ser preenchido para concluir o processo de registro.

8. Insira as informações de contato da conta.

    > [AZURE.IMPORTANT] Você deve fornecer informações de contato precisas, pois elas serão usadas em nosso processo de verificação para que sua empresa seja aprovada no Centro de Desenvolvimento.

9. Para uma conta corporativa, também será necessário fornecer informações de contato do **Aprovador da Empresa**, a pessoa que pode verificar que você tem autorização para criar a conta em nome de sua organização. Quando tiver terminado, clique em **Próximo** para chegar à **"seção Pagamento"**.

10. Insira suas informações de pagamento para pagar por sua conta. Se você tiver um código promocional que cubra o custo do registro, insira-o aqui. Caso contrário, forneça as informações de seu cartão de crédito (ou do PayPal nos mercados com suporte). Quando tiver terminado, clique em **Próximo** para chegar à **"tela Revisão"**.

11. Revise as informações de sua conta e confirme que tudo está correto. Em seguida, leia e aceite os termos e condições do **Contrato de Publicador do Microsoft Azure Marketplace**. Marque a caixa para indicar que você leu e aceitou os termos.

12. Clique em **Concluir** para confirmar seu registro. Enviaremos uma mensagem de confirmação ao seu endereço de email de desenvolvedor.

13. Se você estiver planejando publicar somente ofertas gratuitas, clique em **Ir para o Portal de Publicação do Azure Marketplace** e ignore a seção 3 deste documento, [Registrar sua conta no portal de publicação](#3-register-your-account-in-the-publishing-portal).

14. Se você estiver planejando publicar ofertas comerciais, clique em **Atualizar as informações de sua conta** e preencha as informações bancárias e fiscais em sua conta do Centro de Desenvolvimento.

> [AZURE.IMPORTANT] Você não poderá testar corretamente suas ofertas em preparo e não poderá enviar suas ofertas para produção sem preencher as informações sobre impostos e dados da conta bancária.

Se você preferir atualizar posteriormente suas informações fiscais e bancárias, vá para a seção 3, [Registrar sua conta no portal de publicação](#3-register-your-account-in-the-publishing-portal) e volte mais tarde usando os links no Portal de Publicação do Azure.

### Adicionar informações sobre impostos e dados bancários
 Se você quiser publicar ofertas comerciais para compra, também será preciso adicionar informações de pagamento e informações sobre impostos, e enviá-las para validação no Centro de Desenvolvimento da Microsoft. Se você publicará apenas ofertas grátis (ou ofertas BYOL), não é necessário adicionar essas informações. Você pode adicioná-las mais tarde, mas leva algum tempo para validar as informações sobre impostos. Se você souber que irá oferecer ofertas comerciais para compra, recomendamos adicioná-las assim que possível.

**Informações bancárias**

1. Entre no [Centro de Desenvolvimento da Microsoft](http://dev.windows.com/registration?accountprogram=azure) com sua conta da Microsoft.

2. Clique em **Conta de pagamento** no menu à esquerda, em **Escolher método de pagamento** clique em **Conta bancária** ou em **PayPal**.

    > [AZURE.IMPORTANT] Se você tiver ofertas comerciais que os clientes compram no Marketplace, essa será a conta em que você receberá o pagamento para essas compras.

3. Insira as informações de pagamento e clique em **Salvar** quando estiver satisfeito.

    > [AZURE.IMPORTANT] Se você precisar atualizar ou alterar sua conta de pagamento, execute as mesmas etapas acima, substituindo as informações atuais por novas informações. A alteração da conta de pagamento pode atrasar os pagamentos em até um ciclo de pagamento. Esse atraso ocorre porque precisamos confirmar a alteração da conta, como fizemos na primeira configuração da conta de pagamento. Você ainda receberá o valor total após a verificação de sua conta; os pagamentos atrasados do ciclo de pagamento atual serão adicionados ao próximo.

4. Clique em **Próximo**.

**Informações fiscais**

1. Entre no [Centro de Desenvolvimento da Microsoft](http://dev.windows.com/registration?accountprogram=azure) com sua conta da Microsoft (se for necessário).

2. Clique em **Perfil fiscal** no menu à esquerda.

3. Na página **Configure seu formulário de imposto**, selecione o país ou região onde reside de forma permanente e, em seguida, selecione o país ou região onde você mantém cidadania principal. Clique em **Próximo**.

4. Insira seus detalhes fiscais e, em seguida, clique em **Próximo**.

> [AZURE.WARNING] Você não poderá enviar suas ofertas comerciais por push para produção sem preencher as informações sobre impostos e os dados de conta bancária em sua conta do Centro de Desenvolvimento da Microsoft.

## 3\. Registrar sua conta no portal de publicação
O portal de publicação do Azure é usado para publicar e gerenciar suas ofertas. Você encontrará algumas informações úteis no portal de publicação que guiarão você pelo processo de criação da oferta.

> [AZURE.WARNING] A mesma conta corporativa da Microsoft usada no registro do Centro de Desenvolvimento da Microsoft deve ser usada aqui. Usuários adicionais podem ser adicionados para ajudar após a criação de conta do editor mestre.

1.	Abra uma nova sessão de Janela anônima do Chrome ou navegação InPrivate do Internet Explorer para garantir que você não esteja conectado a uma conta pessoal.

2.	Acesse [http://publish.windowsazure.com](http://publish.windowsazure.com).

3.	Entre com a conta da Microsoft de registro de sua empresa (ou seja, marketplace@example.com)) e adicione coadministradores conforme necessário.

  >[AZURE.TIP] As políticas de participação são descritas no [site do Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

  > Se você tiver problemas com o registro no Centro de Desenvolvimento, registre um tíquete de suporte, conforme explicado abaixo: 1. Contate o [Suporte](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&supportregion=pt-BR&pesid=15635&ccsid=635847950577064286). 2. Escolha **Centro de Desenvolvedores**. 3. Escolha **Perfil**. 4. Escolha um método de contato.






## Próximas etapas
Agora que sua conta foi criada e registrada, clique no tipo de artefato (máquina virtual, serviço do desenvolvedor, serviço de dados, modelo de solução) que você deseja publicar no Azure Marketplace. Visite um dos seguintes artigos para saber como publicar sua respectiva oferta:

| Imagem de máquina virtual | Serviço do desenvolvedor | Serviço de dados | Modelo de solução |
|----|-----|-----|-----|-----|
|**Etapa 2: Criar sua oferta** |[Pré-requisitos gerais não técnicos](marketplace-publishing-pre-requisites.md)| [Pré-requisitos gerais não técnicos](marketplace-publishing-pre-requisites.md)| [Pré-requisitos gerais não técnicos](marketplace-publishing-pre-requisites.md)| [Pré-requisitos gerais não técnicos](marketplace-publishing-pre-requisites.md)|
| [Pré-requisitos técnicos de imagem de VM][link-single-vm-prereq] | Pré-requisitos técnicos de serviço de desenvolvedor | [Pré-requisitos técnicos de serviço de dados](marketplace-publishing-data-service-creation-prerequisites.md) | [Pré-requisitos técnicos de modelo de solução](marketplace-publishing-solution-template-creation-prerequisites.md) |
| [Guia de publicação de imagem de VM][link-single-vm] | Guia de publicação do serviço do desenvolvedor | [Guia de publicação do serviço de dados](marketplace-publishing-data-service-creation.md) | [Guia de publicação de modelo de solução](marketplace-publishing-solution-template-creation.md) |
| [Guia de conteúdo de marketing do Azure Marketplace][link-pushstaging] | [Guia de conteúdo de marketing do Azure Marketplace][link-pushstaging] | [Guia de conteúdo de marketing do Azure Marketplace][link-pushstaging] | [Guia de conteúdo de marketing do Azure Marketplace][link-pushstaging] |

## Consulte também
- [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md

<!---HONumber=AcomDC_0128_2016-->