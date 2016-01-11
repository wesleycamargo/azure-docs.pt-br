<properties
   pageTitle="Configurando credenciais de autenticação nomeadas | Microsoft Azure"
   description="Saiba como fornecer credenciais que o Visual Studio pode usar para autenticar solicitações no Azure para publicar um aplicativo no Azure do Visual Studio ou para monitorar um serviço de nuvem existente."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/18/2015"
   ms.author="tarcher" />

# Configurando credenciais de autenticação nomeadas

Para publicar um aplicativo no Azure do Visual Studio ou para monitorar um serviço de nuvem existente, você deve fornecer credenciais que o Visual Studio pode usar para autenticar solicitações no Azure. Existem diversos lugares no Visual Studio nos quais você pode entrar para fornecer essas credenciais. Por exemplo, no Gerenciador de Servidores, você pode abrir o menu de atalho do nó do **Azure** e escolher **Conectar ao Azure**. Quando você entra, as informações de assinatura associadas à sua conta do Azure estão disponíveis no Visual Studio e não é necessário fazer mais nada.

As Ferramentas do Azure também dão suporte a uma maneira mais antiga de fornecer credenciais, usando o arquivo de assinatura (arquivo .publishsettings). Este tópico descreve esse método, que ainda tem suporte no Azure SDK 2.2.

Os itens a seguir são necessários para autenticação no Azure.

- Sua ID de assinatura

- Um certificado X.509 v3 válido

>[AZURE.NOTE]O comprimento da chave do certificado X.509 v3 deve ser pelo menos 2.048 bits. O Azure descartará qualquer certificado que não atenda a esse requisito ou que não seja válido.

O Visual Studio usa sua ID da assinatura junto com os dados do certificado como credenciais. As credenciais apropriadas são referenciadas no arquivo de assinatura (arquivo .publishsettings), que contém uma chave pública para o certificado. O arquivo de assinatura pode conter credenciais para mais de uma assinatura.

Você pode editar as informações de assinatura da caixa de diálogo **Nova/Editar Assinatura**, conforme explicado posteriormente neste tópico.

Para criar um certificado por conta própria, você pode consultar as instruções em [Criar e carregar um certificado de gerenciamento do Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) e carregar manualmente o certificado para o portal de gerenciamento.

>[AZURE.NOTE]Essas credenciais que o Visual Studio requer para gerenciar seus serviços de nuvem não são as mesmas credenciais necessárias para autenticar uma solicitação nos serviços de armazenamento do Azure.

## Modificar ou exportar credenciais de autenticação no Visual Studio

Você também pode configurar, modificar ou exportar suas credenciais de autenticação na nova caixa de diálogo **Assinatura** que aparece se você realizar uma das seguintes ações:

- No **Gerenciador de Servidores**, abra o menu de atalho para o nó do **Azure**, escolha **Gerenciar Assinaturas**, escolha a guia **Certificados** e escolha o botão **Novo** ou **Editar**.

- Quando você publica um serviço de nuvem do Azure por meio do assistente **Publicar Aplicativo do Azure**, escolha **Gerenciar** na lista **Escolher sua Assinatura**, depois a guia Certificados e, em seguida, o botão **Novo** ou **Editar**.

O procedimento a seguir pressupõe que a caixa de diálogo **Nova Assinatura** está aberta.

### Para configurar credenciais de autenticação no Visual Studio

1. Na lista **Selecionar um certificado existente para autenticação**, escolha um certificado.

1. Escolha o botão **Copiar o caminho completo**. O caminho para o certificado (arquivo .cer) é copiado na área de transferência.

    >[AZURE.IMPORTANT]Para publicar seu aplicativo Azure no Visual Studio, você deve carregar esse certificado no Portal de Gerenciamento.

1. Para carregar o certificado no Portal de Gerenciamento do Azure:

    1. Escolha o link Portal do Azure.

         O [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/?LinkID=213885) é aberto.

    1. Entre no Portal de Gerenciamento do Azure usando sua conta da Microsoft e, em seguida, escolha o botão **Serviços de Nuvem**.

    1. Escolha o serviço de nuvem que lhe interessa.

        A página do serviço se abre.

    1. Na guia **Certificados**, escolha o botão **Carregar**.

    1. Cole o caminho completo do arquivo .cer que você acabou de criar e, em seguida, digite a senha que especificou.

<!---HONumber=AcomDC_1223_2015-->