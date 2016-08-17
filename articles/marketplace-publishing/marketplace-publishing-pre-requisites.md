<properties
   pageTitle="Pré-requisitos não técnicos para criar uma oferta para o Azure Marketplace | Microsoft Azure"
   description="Entenda os requisitos para criar e implantar uma oferta para o Azure Marketplace para outras pessoas comprarem."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="08/01/2016"
  ms.author="hascipio"/>

# Pré-requisitos gerais para a criação de uma oferta para o Azure Marketplace
Compreenda os pré-requisitos gerais, centrados em processos de negócios que são necessários para prosseguir com o processo de criação de oferta.

## Certifique-se de que você está registrado como um vendedor da Microsoft
Para obter instruções detalhadas sobre como registrar uma conta do vendedor com a Microsoft, vá para [Criação e registro de conta](marketplace-publishing-accounts-creation-registration.md).

- **Se sua empresa já estiver registrada como uma vendedora no Centro de Desenvolvimento e você desejar criar uma nova oferta**, faça logon no portal de Publicação com a mesma id de email com a qual o registro foi feito no Centro de Desenvolvimento. Essa etapa é necessária para que o portal de Publicação e o Centro de Desenvolvimento vinculem-se um ao outro.
- **Se sua empresa já estiver registrada como uma vendedora no Centro de Desenvolvimento e você desejar editar uma oferta existente**, faça logon no portal de Publicação com a conta de administrador ou com uma conta que é adicionada como um coadministrador no portal de Publicação. As etapas para adicionar uma conta de coadministrador são fornecidas abaixo.

## Etapas para adicionar um coadministrador no portal de Publicação
Os administradores do portal de Publicação podem adicionar os outros membros da empresa, que estão trabalhando no aplicativo, como um coadministrador no portal de Publicação. **Supondo que você seja o administrador**, abaixo estão as etapas para adicionar um coadministrador.

>[AZURE.NOTE] No caso de novos usuários, antes de adicionar um coadministrador no portal de Publicação, verifique se você criou pelo menos um aplicativo no portal de Publicação. Isso é obrigatório, pois a guia **EDITORES** aparece apenas após a criação de pelo menos um aplicativo no portal de Publicação.

1. Certifique-se de que a identificação de email do coadministrador seja uma conta da Microsoft (MSA). Caso não seja, registre-a como uma MSA usando este [link](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Certifique-se de que haja pelo menos um aplicativo na conta de administrador antes de tentar adicionar um coadministrador.
3. Depois de concluir as etapas acima, faça logon no portal de Publicação com a identificação de email de coadministrador e faça logoff.
4. Agora, faça logon no portal de Publicação com a identificação de email de administrador.
5. Navegue até Editores -> selecione sua conta -> Administradores -> Adicionar o coadministrador (captura de tela abaixo)

    ![desenho](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)

6. Verifique se as ids de email fornecidas nas várias fase do processo de publicação (por exemplo, Centro de Desenvolvimento, portal de Publicação) são monitoradas para qualquer comunicação da Microsoft.
7. Para o registro do Centro de Desenvolvimento, evite usar uma conta associada a uma única pessoa. Essa é uma sugestão para remover dependência de um indivíduo.
8. Se você enfrentar problemas com o registro do Centro de Desenvolvimento, gere um tíquete usando este [link](https://developer.microsoft.com/pt-BR/windows/support).

> [AZURE.IMPORTANT] Você não precisa concluir o imposto da empresa e informações bancárias, se você estiver planejando publicar apenas ofertas gratuitas (ou traga sua própria licença).

> O registro da empresa deve ser concluído para começar. No entanto, enquanto a sua empresa trabalha com as informações bancárias e de impostos na conta de Desenvolvedor da Microsoft, os desenvolvedores podem começar a trabalhar na criação da imagem de máquina virtual no [Portal de Publicação](https://publish.windowsazure.com), obter certificação para ela e testá-la no ambiente de preparo do Azure. Você precisará de aprovação da conta do vendedor completa apenas para a etapa final da publicação da sua oferta no Azure Marketplace.

## Adquira uma assinatura "pré-paga" do Azure
Esta é a assinatura que você usará para criar suas imagens VM e passar as imagens para o [Azure Marketplace](https://azure.microsoft.com/marketplace/). Se você não tiver uma assinatura existente, em seguida, inscreva-se em https://account.windowsazure.com/signup?offer=ms-azr-0003p

## Países de “origem de venda”
> [AZURE.WARNING]
Para vender seus serviços no Azure Marketplace, você deve garantir que a entidade registrada seja de um dos países aprovados para vender. Essa restrição é por razões de pagamento e tributação. Estamos considerando a expansão dessa lista de países em um futuro próximo, portanto, fique atento. Para obter a lista completa, consulte seção 1b das [Políticas de participação do Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).

## Próximas etapas
Depois que os pré-requisitos técnicos são atendidos, há os pré-requisitos técnicos específicos da oferta. Clique no link para o artigo para o respectivo tipo de oferta que você gostaria de criar para o Azure Marketplace.

- [Pré-requisitos técnicos de VM](marketplace-publishing-vm-image-creation-prerequisites.md)
- [Pré-requisitos técnicos de Modelo de Solução](marketplace-publishing-solution-template-creation-prerequisites.md)

## Consulte também
- [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0803_2016-->