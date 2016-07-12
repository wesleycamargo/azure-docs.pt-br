<properties
    pageTitle="Pontos de extremidade do Azure"
    description="Descreve as configurações dos Pontos de Extremidade do Azure no Kit de ferramentas do Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="06/24/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# Pontos de extremidade do Azure #

Os pontos de extremidade do Azure de serviço que determinam se o aplicativo será implantado e gerenciado pela plataforma global do Azure, operado pelo Azure pela 21Vianet na China ou por uma plataforma privada do Azure. A caixa de diálogo **Pontos de Extremidade de Serviço** permite que você especifique quais pontos de extremidade de serviço deseja usar. Para abrir a caixa de diálogo **Pontos de extremidade de Serviço** no Eclipse, clique em **Window**, clique em **Preferências**, expanda **Azure** e, em seguida, clique em **Pontos de Extremidade de Serviço**. A definição do campo **Conjunto Ativo** determina quais pontos de extremidade de serviço do Azure serão usados para os projetos do Azure no seu espaço de trabalho atual.

A imagem a seguir mostra a caixa de diálogo **Pontos de Extremidade de Serviço**.

![][ic719493]

## Para definir pontos de extremidade de serviço ##

Na caixa de diálogo **Pontos de Extremidade de Serviço**, execute uma das seguintes ações:

* Se deseja usar a plataforma global do Azure, na lista suspensa **Conjunto Ativo**, selecione **windowsazure.com** e clique em **OK**.
* Se deseja usar o Azure operado pela 21Vianet na China, a partir da lista suspensa **Conjunto Ativo**, selecione **windowsazure.cn (China)** e clique em **OK**.
* Se quiser usar uma plataforma privada do Azure:
    1. Clique em **Editar**.
    2. Uma caixa de diálogo será aberta, informando que a caixa de diálogo **Pontos de Extremidade de Serviço** será fechada e o arquivo de definições de preferência será aberto. Clique em **OK**.
    3. No arquivo preferencesets.xml, crie um novo elemento `preferenceset`. Para esse novo elemento, crie atributos `name`, `blob`, `management`, `portalURL` e `publishsettings` e adicione valores para que correspondam à sua plataforma privada do Azure. Você pode usar os valores fornecidos para os elementos `preferenceset` existentes como modelos. **Observação**: O valor usado para o atributo `blob` deve conter o texto "blob" na URL.
    4. Salve e feche preferencesets.xml.
    5. Reabra a caixa de diálogo **Pontos de Extremidade de Serviço**.
    6. Na lista suspensa **Conjunto Ativo**, selecione o ativo definido que você criou e clique em **OK**.
    7. Depois de criar seu elemento `preferenceset` da plataforma privada do Azure, você pode alterar os valores atribuídos a ele clicando no botão **Editar** na caixa de diálogo **Ponto de Extremidade de Serviços**. Se desejar, você também pode criar vários elementos `preferenceset` de plataformas privadas do Azure.

## Consulte também ##

[Kit de ferramentas do Azure para Eclipse][]

[Instalação do Kit de Ferramentas do Azure para o Eclipse][]

[Criação de um aplicativo Hello World do Azure no Eclipse][]

Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure][].

<!-- URL List -->

[Central de desenvolvimento Java do Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criação de um aplicativo Hello World do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalação do Kit de Ferramentas do Azure para o Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png

<!---HONumber=AcomDC_0629_2016-->