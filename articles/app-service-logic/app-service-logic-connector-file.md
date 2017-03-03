---
title: "Usando o Conector de arquivos nos Aplicativos Lógicos | Microsoft Docs"
description: "Como criar e configurar o conector de arquivo ou o aplicativo de API e usá-lo em um Aplicativo lógico no Serviço de Aplicativo do Azure"
author: rajeshramabathiran
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 07ceb81a-f8f6-4901-a7a2-06a9ac47efd5
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: rajram
translationtype: Human Translation
ms.sourcegitcommit: 5b1b65e3d1066bea6958fa6461a157ee39fbe7dc
ms.openlocfilehash: 35bd4561f6e8f4482cb084eb293b506eb9269cf7
ms.lasthandoff: 02/15/2017


---
# <a name="get-started-with-the-file-connector-and-add-it-to-your-logic-app"></a>Introdução ao conector de arquivo e adição dele ao seu Aplicativo lógico
> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview dos Aplicativos Lógicos.
>
>

Conecte-se a um sistema de arquivos para carregar, baixar e realizar outras operações com seus arquivos em um computador host. Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados. Você pode adicionar o conector de arquivo a seu fluxo de trabalho de negócios e processar os dados como parte desse fluxo dentro de um Aplicativo lógico.

O conector de arquivo usa o Gerenciador de Conexões Híbridas para a conectividade híbrida com o sistema de arquivos do host.

## <a name="creating-a-file-connector-for-your-logic-app"></a>Criando um conector de arquivo para seu Aplicativo lógico
Para usar o conector de arquivo, primeiro você precisa criar uma instância do aplicativo de API do conector de arquivo. Isso pode ser feito da seguinte maneira:

1. Abra o Azure Marketplace usando a opção + NEW no lado esquerdo do Portal do Azure.
2. Procure "conector de arquivo".
3. Selecione **Conector de Arquivo (visualização)** nos resultados da pesquisa.
4. Selecione o botão **Criar**
5. Configure o conector de arquivo da seguinte maneira:  
   ![][1]

   * **Nome** - dê um nome para seu conector de arquivo
   * **Configurações do pacote**
     * **Pasta Raiz** - especifique o caminho da pasta raiz em seu computador host. Por exemplo, D:\FileConnectorTest
     * **Cadeia de Conexão do Barramento de Serviço** - forneça uma Cadeia de Conexão do Barramento de Serviço. Verifique se o namespace do barramento de serviço é do tipo Padrão e NÃO Básico, para permitir o uso de Retransmissões do Barramento de Serviço.  A Retransmissão do Barramento de Serviço é usada para conectar-se ao Gerenciador de Conexões Híbridas.
   * **Plano de Serviço de Aplicativo** - selecione ou crie um Plano de Serviço de Aplicativo
   * **Camada de preços** - escolha uma camada de preços para o conector
   * **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
   * **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
   * **Local** - escolha a região geográfica onde você quer que o conector seja implantado
6. Clique em Criar. Será criado um novo conector de arquivo

## <a name="configure-hybrid-connection-manager"></a>Configurar o Gerenciador de Conexão Híbrida
Depois que a instância de Aplicativo de API for criada, navegue até seu painel.  Isso pode ser feito clicando em Procurar > Aplicativos de API > selecionar seu Aplicativo de API do conector de arquivo.  Aqui, o Gerenciador de Conexões Híbridas precisa ser configurado.
Para obter mais informações sobre configuração e resolução de problemas do Gerenciador de Conexão Híbrida, consulte [Uso do Gerenciador de Conexão Híbrida].

## <a name="using-the-file-connector-in-your-logic-app"></a>Usando o conector de arquivo em seu Aplicativo lógico
Depois do aplicativo de API criado, você poderá usar o conector de arquivo como uma ação para seu Aplicativo lógico. Para fazer isso, você precisa:

1. Crie um novo Aplicativo lógico e escolha o mesmo grupo de recursos que tem o conector de arquivo. Siga as instruções para [Criar um novo Aplicativo lógico].
2. Abra "Gatilhos e Ações" no Aplicativo lógico criado para abrir o Designer de Aplicativos Lógicos e configure seu fluxo.
3. O conector de arquivo será exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito.
4. Você pode soltar o aplicativo de API do conector de arquivo no editor clicando em "conector de arquivo". O conector de arquivo exibirá um gatilho e quatro ações:   
   ![][5]
5. Cada um deles expõe determinadas propriedades. A imagem a seguir lista as propriedades do gatilho e a Ação Obter arquivo:   
   ![][6]
6. Depois que eles forem configurados, o Gatilho e a Ação poderão ser usados no seu fluxo. Da mesma forma, outras ações também podem ser configuradas.

> [!NOTE]
> O gatilho do arquivo excluirá o arquivo após este ser lido com êxito na pasta.
>
>

## <a name="file-connector-rest-apis"></a>APIs REST do conector de arquivo
Para usar o conector fora de um Aplicativo lógico, as APIs REST expostas pelo conector podem ser utilizadas. Você exibir essas Definições da API usando Procurar -> Aplicativo de Api -> conector de arquivo. Agora clique na lente Definição de API na seção Resumo para exibir todas as APIs expostas por esse conector:   
![][7]

Os detalhes das APIs podem ser encontrados em [Definição da API do conector de arquivo].

## <a name="do-more-with-your-connector"></a>Fazer mais com seu conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos lógicos?](../logic-apps/logic-apps-what-are-logic-apps.md).

> [!NOTE]
> Se você quiser começar com os Aplicativos lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar Aplicativo Lógico](https://azure.microsoft.com/try/app-service/logic/), onde poderá criar imediatamente um Aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
>
>

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Veja [Monitorar seus aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Criar um novo Aplicativo lógico]: app-service-logic-create-a-logic-app.md
[File connector API definition]: https://msdn.microsoft.com/library/dn936296.aspx
[Uso do Gerenciador de Conexão Híbrida]: ../app-service-web/web-sites-hybrid-connection-get-started.md

