<properties
    pageTitle="Implantando Grandes Implantações"
    description="Saiba como implantar implantações grandes do Azure usando o Kit de Ferramentas do Azure para Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

# Implantando Grandes Implantações #

Se sua implantação for muito grande para caber na pasta approot padrão, você pode usar um recurso de armazenamento local como a pasta raiz de implantação para seu JDK e servidor de aplicativos.

## Para usar um recurso de armazenamento local como pasta raiz de implantação para grandes implantações ##

1. Crie um recurso de armazenamento local novo. O nome do recurso não importa. Os recursos de armazenamento são definidos no nível de função. É a maneira mais rápida para acessar a caixa de diálogo de configuração de armazenamento local, da qual você pode criar um novo recurso de armazenamento local usando as etapas a seguir: Clique com o botão direito na função da exibição do **Explorador de Projeto** (expanda o nó do projeto do Azure se não vir a função), clique em **Azure**, e, em seguida, clique em **Armazenamento Local**. Dentro da caixa de diálogo **Armazenamento Local**, clique em **Adicionar** para criar um novo recurso de armazenamento local.
1. Defina o tamanho desejado para pelo menos 2048 MB (algo menor pode causar os mesmos problemas de tamanho de arquivo como haveria na approot).
1. A caixa **Limpar o conteúdo quando a instância de função é reciclada** deve estar marcada; isso ajudará a evitar que a lógica de inicialização da implantação seja executada em conflito com os arquivos pré-existentes no recurso quando a instância de função é reciclada.
1. O valor de **Variável de ambiente armazenando o caminho do diretório do recurso após a implantação** deve estar definido como a cadeia de caracteres **DEPLOYROOT**. A caixa de diálogo de recurso de armazenamento local será semelhante à seguinte. ![][ic667943]

Como alternativa, se você usar **DEPLOYROOT** como o *nome* de seu recurso local e não alterar o nome da variável de ambiente gerado automaticamente (que, nesse caso, será definido como **DEPLOYROOT\_PATH**), isso funcionaria para seu aplicativo.

As informações adicionais sobre como criar um recurso de armazenamento local podem ser encontradas em [Propriedades de armazenamento local][].

## Consulte também ##

[Kit de ferramentas do Azure para Eclipse][]

[Criar um aplicativo Hello World do Azure no Eclipse][]

[Instalação do Kit de Ferramentas do Azure para Eclipse][]

Para saber mais sobre como usar o Azure com Java, confira a [Central de Desenvolvimento Java do Azure][].

<!-- URL List -->

[Central de Desenvolvimento Java do Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criar um aplicativo Hello World do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalação do Kit de Ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriedades de armazenamento local]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png

<!---HONumber=AcomDC_0629_2016-->