<properties
   pageTitle="Criação e registro da conta do editor | Microsoft Azure"
   description="Instruções para criar uma conta de vendedor da Microsoft para que, mediante aprovação, seja possível vender vários tipos de ofertas no Azure Marketplace."
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
   ms.date="11/17/2015"
   ms.author="hascipio"/>

# Criar uma conta de vendedor da Microsoft
Este artigo fornece uma orientação sobre o processo necessário de criação e registro de uma conta para se tornar um vendedor aprovado da Microsoft para o Azure Marketplace.

## 1\. Criar uma conta da Microsoft
> [AZURE.WARNING]Para completar o processo de publicação, será necessário criar uma conta da Microsoft. Esta conta será usada para se registrar e entrar no portal de publicação e no Painel do Vendedor. Você deve ter apenas uma conta da Microsoft para suas ofertas do Azure Marketplace. Ela não deve ser específica a serviços ou ofertas.

O endereço que forma o nome de usuário deve estar no seu domínio e ser controlado por sua equipe de TI (como publishing@example.com). Informações sobre pagamento e impostos, bem como relatórios serão encaminhados através desta conta.

  >[AZURE.WARNING]Palavras como "Azure" e "Microsoft" não são aceitas para o registro da conta da Microsoft. Evite usar essas palavras para concluir a criação da conta e o processo de registro.

1. Crie uma lista de distribuição (DL) ou grupo de segurança (SG) no domínio da empresa.
  - Adicione sua equipe de integração à DL.
  - A DL deve estar ativa para receber o email de confirmação.
  - Use marketplace@example.com como o endereço de email para a DL.
  - Isso precisa ser concluído em seus sistemas internos.
2. Abra uma nova sessão de Janela anônima do Chrome ou InPrivate do Internet Explorer para garantir que você não esteja conectado a uma conta existente
3. Registre-se para uma conta da Microsoft usando o email da DL.
 - Você pode registrar uma conta da Microsoft em [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx).
 - Use marketplace@example.com como o endereço de email.
 - A ID de sua conta da Microsoft agora é marketplace@example.com.

    <!--[drawing][img-msalive]-->

4. Ao se registrar, use um número de telefone válido. O sistema enviará um código de verificação como mensagem de texto ou uma chamada automatizada se a verificação de identidade for necessária.
5. Verifique o endereço de email enviado à DL.

    <!--[drawing][img-email]-->

6. Agora, você está pronto para usar uma nova conta da Microsoft no Painel do Vendedor.

> [AZURE.IMPORTANT]O uso da DL permite que várias pessoas recebam notificações por email importantes para a comunicação de informações de pagamento. Ela também garante que a propriedade da conta da Microsoft possa ser transferida e não esteja vinculada a uma única pessoa.

## 2\. Criar sua conta de Painel do Vendedor
O Painel do Vendedor da Microsoft é usado para registrar as informações da empresa uma vez. O inscrito deve ser um representante válido da empresa e fornecer suas informações pessoais como uma maneira de validar sua identidade. A pessoa que fizer o registro deverá usar uma conta da Microsoft que seja compartilhada com a empresa, e a mesma conta deve ser usada no portal de publicação do Azure. Você deve verificar se sua empresa ainda não tem uma conta do Painel do Vendedor antes de tentar criar uma. Durante o processo, vamos coletar informações de conta bancária, informações sobre impostos e informações de endereço da empresa. Esses dados são geralmente obtidos por meio de contatos de finanças ou negócios.

> [AZURE.IMPORTANT]Você deve completar os seguintes componentes de perfil do vendedor para prosseguir pelas diversas fases da criação e implantação da oferta.


| Perfil do vendedor | Para iniciar o rascunho | Staging | Publicar modelo gratuito e de solução | Publicar comercial |
|----|----|----|----|----|
|Registro da empresa | Imprescindível | Imprescindível | Imprescindível | Imprescindível |
|ID do perfil fiscal | Opcional | Opcional | Opcional | Imprescindível |
|Conta bancária | Opcional | Opcional | Opcional | Imprescindível |


> [AZURE.NOTE]Há suporte para BYOL (Traga sua Própria Licença) apenas para máquinas virtuais, e é considerado uma oferta **gratuita**.


### Registrar sua conta da empresa
1. Abra uma nova sessão de Janela anônima do Chrome ou InPrivate do Internet Explorer para garantir que você não esteja conectado a uma conta pessoal.

2. Acesse [http://sellerdashboard.microsoft.com](http://sellerdashboard.microsoft.com)

    ![desenho][img-sd-url]

3. Entre com a conta da Microsoft de registro de sua empresa (por exemplo, marketplace@example.com)).

    ![desenho][img-signin]

4. Conclua o assistente "Ajude-nos a proteger sua conta", que vai verificar sua identidade através do número de telefone ou endereço de email.

    ![desenho][img-verify]

5. Acesse os **Detalhes da Conta**. Nesta tela, você vai inserir suas informações pessoais, que serão usadas apenas para a verificação de identidade. Isso significa seu nome, endereço de email, endereço residencial e número de telefone pessoal. Essas informações não são compartilhadas com ninguém fora da Microsoft.

    ![desenho][img-sd-top]

    <!--[drawing][img-sd-info]-->

6. Registre-se em nome de sua empresa, especificando o tipo de conta como **Empresa**, e não como **Indivíduo**. Clique em **Próximo**.

    ![desenho][img-sd-type]

7. Preencha os detalhes da empresa. As informações devem ser precisas: sede e referência serão verificados por uma equipe da Microsoft.

    ![desenho][img-sd-mktg1]

8. O nome da empresa é usado pelo portal de publicação e, portanto, deve ser preciso.

    <!--[drawing][img-sd-mktg2]-->

9. Use o endereço da sede de sua empresa.

    ![desenho][img-sd-addr]

10. Use uma referência acessível e reconhecida como um representante da empresa

    ![desenho][img-sd-legal]

11. Clique em **Enviar para Aprovação** e pronto.

    ![desenho][img-sd-submit]


Se você estiver planejando publicar somente ofertas gratuitas, poderá pular para a seção 3, “Registrar sua conta no portal de publicação”.

Se você estiver planejando publicar ofertas comerciais, você deve preencher as informações bancárias e sobre impostos em sua conta do Painel do Vendedor.

> [AZURE.IMPORTANT]Você não poderá testar corretamente suas ofertas em preparo e não poderá enviar suas ofertas para produção sem preencher as informações sobre impostos e dados da conta bancária.

Se você preferir atualizar posteriormente suas informações fiscais e bancárias, vá para a seção 3, "Registrar sua conta no portal de publicação" e volte mais tarde usando os links no portal de publicação.

### Adicionar informações sobre impostos e dados bancários
 Se você quiser publicar ofertas comerciais para compra, também será preciso adicionar informações de pagamento e informações sobre impostos, e enviá-las para validação no Painel do Vendedor. Se você publicará apenas ofertas grátis (ou ofertas BYOL), não é necessário adicionar essas informações. Você pode adicioná-las mais tarde, mas leva algum tempo para validar as informações sobre impostos. Se você souber que irá oferecer ofertas comerciais para compra, recomendamos adicioná-las assim que possível.

1. Entre no [Painel do Vendedor](http://sellerdashboard.microsoft.com) com sua conta da Microsoft.

2. Clique na guia **CONTA** e clique em **pagamento e impostos**.

3. Clique em **ADICIONAR INFORMAÇÕES DE PAGAMENTO E IMPOSTOS**.

4. Na página **Escolher um método de pagamento**, em **Novo método de pagamento**, clique em **Conta bancária** ou em **PayPal**.

> [AZURE.IMPORTANT]Se você tiver ofertas comerciais que os clientes compram no Marketplace, essa será a conta em que você receberá o pagamento para essas compras.

5. Insira os detalhes de uma conta bancária ou uma conta PayPal.

6. Clique em **PRÓXIMO**.

7. Na página **Informações sobre impostos**, selecione o país ou região onde você tenha residência permanente e, em seguida, selecione o país ou região onde você mantém cidadania principal. Clique em **PRÓXIMO**.

8. Insira seus detalhes fiscais e, em seguida, clique em **PRÓXIMO**.

9. Clique em **Enviar**. Se você ainda não estiver pronto para enviar suas informações de impostos para a validação, poderá clicar em **Salvar** ou em **Salvar e Sair**. Demora algum tempo para validar as informações de impostos, portanto, é recomendável que você as envie para validação assim que possível.

> [AZURE.WARNING]Você não poderá enviar suas ofertas comerciais por push para produção sem preencher as informações sobre impostos e os dados de conta bancária em sua conta do Painel do Vendedor.

## 3\. Registrar sua conta no portal de publicação
O portal de publicação do Azure é usado para publicar e gerenciar suas ofertas. Você encontrará algumas informações úteis no portal de publicação que guiarão você pelo processo de criação da oferta.

> [AZURE.WARNING]A mesma conta da Microsoft da empresa que foi usada no registro do Painel do Vendedor deve ser usada aqui. Usuários adicionais podem ser adicionados para ajudar após a criação de conta do editor mestre.

1.	Abra uma nova sessão de Janela anônima do Chrome ou navegação InPrivate do Internet Explorer para garantir que você não esteja conectado a uma conta pessoal.
2.	Acesse [http://publish.windowsazure.com](http://publish.windowsazure.com).
3.	 Entre com a conta da Microsoft de registro de sua empresa (ou seja, marketplace@example.com)) e adicione coadministradores conforme necessário.
4.	Leia e aceite os termos do contrato do editor (primeira entrada no portal de publicação) e pronto.

  >[AZURE.TIP]As políticas de participação são descritas no [site do Azure](http://azure.microsoft.com/support/legal/marketplace/participation-policies/).

  > Se você tiver problemas com a conclusão do Registro do Vendedor, registre um tíquete de suporte, conforme explicado abaixo: 1. [Entre em contato com o Suporte](http://go.microsoft.com/fwlink?LinkId=272975) 2. Escolha o **Registro do Painel do Vendedor e sua conta**. 3. Escolha **Registrar-se em uma conta de desenvolvedor**. 4. Escolha um método de contato.

### Países de “origem de venda”

> [AZURE.WARNING]Para vender seus serviços no Azure Marketplace, sua entidade registrada precisa estar dentro dos países aprovados para "origem de venda". Essa restrição é por razões de pagamento e tributação. Estamos considerando a expansão dessa lista de países em um futuro próximo, portanto, fique atento. Para obter a lista completa, confira seção 1b das [Políticas de participação do Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).




## Próximas etapas
Agora que sua conta foi criada e registrada, clique no tipo de artefato (máquina virtual, serviço do desenvolvedor, serviço de dados, modelo de solução) que você deseja publicar no Azure Marketplace. Visite um dos seguintes artigos para saber como publicar sua respectiva oferta:

|| Imagem de máquina virtual| Serviço do desenvolvedor | Serviço de dados | Modelo da solução | |----|-----|-----|-----|-----| |**Etapa 2: criar sua oferta** |[Pré-requisitos gerais não técnicos](marketplace-publishing-pre-requisites.md)| [Pré-requisitos gerais não técnicos](marketplace-publishing-pre-requisites.md)| [Pré-requisitos gerais não técnicos](marketplace-publishing-pre-requisites.md)| [Pré-requisitos gerais não técnicos](marketplace-publishing-pre-requisites.md)| || [Pré-requisitos técnicos da imagem de VM][link-single-vm-prereq] | Pré-requisitos técnicos do serviço do desenvolvedor | [Pré-requisitos técnicos do serviço de dados](marketplace-publishing-data-service-creation-prerequisites.md) | || [Pré-requisitos técnicos do modelo da solução](marketplace-publishing-solution-template-creation-prerequisites.md) | || [Guia de publicação da imagem de VM][link-single-vm] | Guia de publicação do serviço do desenvolvedor | [Guia de publicação do serviço de dados](marketplace-publishing-data-service-creation.md) | [Guia de publicação do modelo da solução](marketplace-publishing-solution-template-creation.md) | || [Guia de conteúdo de marketing do Azure Marketplace][link-pushstaging] | [Guia de conteúdo de marketing do Azure Marketplace][link-pushstaging] | [Guia de conteúdo de marketing do Azure Marketplace][link-pushstaging] | [Guia de conteúdo de marketing do Azure Marketplace][link-pushstaging] |

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

[link-msdndoc]: https://msdn.microsoft.com/pt-BR/library/jj552460.aspx
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

<!---HONumber=AcomDC_1203_2015-->