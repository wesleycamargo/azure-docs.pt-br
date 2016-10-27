<properties
    pageTitle="Novidades do Kit de Ferramentas do Azure para IntelliJ | Microsoft Azure"
    description="Saiba mais sobre os recursos mais recentes do Kit de Ferramentas do Azure para IntelliJ."
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
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>


# <a name="what's-new-in-the-azure-toolkit-for-intellij"></a>Novidades no Kit de Ferramentas do Azure para IntelliJ

## <a name="azure-toolkit-for-intellij-releases"></a>Lançamentos do Kit de Ferramentas do Azure para IntelliJ

Este artigo contém informações sobre as várias versões e atualizações mais recentes do Kit de Ferramentas do Azure para IntelliJ.

> [AZURE.NOTE] Há também um kit de ferramentas do Azure para o Eclipse IDE. Para saber mais, confira [Kit de ferramentas do Azure para Eclipse].

### <a name="august-26,-2016"></a>26 de agosto de 2016

O Kit de Ferramentas do Azure para IntelliJ, versão de agosto de 2016, inclui as seguintes melhorias:

* **Distribuições personalizadas do JDK**. Agora, o Kit de Ferramentas do Azure para IntelliJ oferece suporte a especificação e implantação de uma versão JDK arbitrária para o contêiner WebApp do Azure:
  - Além dos JDKs fornecidos pelo Azure, também é possível escolher dentre uma ampla seleção de versões do Zulu OpenJDK disponibilizadas no Azure pelo Azul Systems.
  - Você também pode especificar sua própria distribuição JDK se carregar um arquivo ZIP em sua conta de armazenamento.
* **Aprimoramentos no modo de exibição do Azure Explorer**:
  - Suporte para gerenciamento de máquinas virtuais usando o novo modelo de Resource Manager do Azure: você pode listar, criar e excluir máquinas virtuais baseadas no resource manager sem sair do IDE.
  - Suporte para gerenciamento de blobs da Conta de armazenamento usando o Resource Manager do Azure, que complementa a funcionalidade existente para gerenciar contas de armazenamento "clássico".
* **Microsoft JDBC Driver 6.0 para SQL Server**. Esta atualização inclui o driver JDBC do Microsoft SQL Server (v 6.0), que é agora incluído como uma biblioteca que você pode adicionar facilmente aos seus projetos Java, substituindo a versão mais antiga.

### <a name="june-29,-2016"></a>29 de junho de 2016

O Kit de Ferramentas do Azure para IntelliJ, versão de março de 2016, inclui os seguintes aprimoramentos:

* **Requisito do Java 8**. O Kit de ferramentas do Azure para IntelliJ agora exige o Java 8, embora esse requisito seja somente para o kit de ferramentas - seus aplicativos podem continuar a usar todas as versões do Java com suporte do Azure.
* **Suporte para os JDKs do Java mais recentes**. Agora há suporte para as versões mais recentes de JDKs do Java pelo Kit de Ferramentas do Azure para IntelliJ.
* **Suporte para o SDK do Azure v2.9.1**. A versão mais recente do SDK do Azure agora é o pré-requisito mínimo para o Kit de Ferramentas do Azure para IntelliJ.
* **Exemplos Integrados**. O Kit de Ferramentas do Azure para IntelliJ agora apresenta vários aplicativos de exemplo para ajudar os desenvolvedores a começar.
* **Integração de Ferramentas HDInsight**. As Ferramentas de HDInsight do Azure agora estão incluídas no Kit de Ferramentas do Azure para IntelliJ. Para obter mais informações, consulte [Plug-in das Ferramentas do HDInsight para IntelliJ].
* **Depuração remota de aplicativos Web do Java**. O Kit de Ferramentas do Azure para IntelliJ agora dá suporte a depuração remota de aplicativos Web do Java no Serviço de Aplicativo do Azure.

### <a name="april-12,-2016"></a>12 de abril de 2016

O Kit de Ferramentas do Azure para IntelliJ, versão de abril de 2016, inclui as seguintes melhorias:

* **Suporte para o SDK do Azure v2.9.0**. A versão mais recente do SDK do Azure agora é o pré-requisito mínimo para o Kit de Ferramentas do Azure para IntelliJ.
* **Diversas melhorias de usabilidade, capacidade de resposta e desempenho relacionadas ao suporte do aplicativo Web do Azure**. Uma série de otimizações de desempenho na forma como o Kit de Ferramentas se comunica com o Azure resulta em uma interface do usuário mais dinâmica.
* **Capacidade de excluir um contêiner do aplicativo Web existente no Azure por meio do IntelliJ**. O Kit de Ferramentas do Azure para IntelliJ agora permite a exclusão de um contêiner da Web existente do Azure sem a necessidade de sair do IntelliJ.

## <a name="see-also"></a>Consulte também ##

Para obter mais informações sobre os kits de ferramentas do Azure para Java IDEs, confira os links a seguir:

- [Kit de ferramentas do Azure para Eclipse]
  - [Instalação do Kit de Ferramentas do Azure para o Eclipse]
  - [Criar um aplicativo Web Hello World para o Azure no Eclipse]
  - [Novidades no Kit de Ferramentas do Azure para o Eclipse]
- [Kit de Ferramentas do Azure para IntelliJ]
  - [Instalação do Kit de Ferramentas do Azure para IntelliJ]
  - [Criar um aplicativo Web Hello World para o Azure no IntelliJ]
  - *Novidades no Kit de Ferramentas do Azure para o IntelliJ (Este artigo)*

Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure].

<!-- URL List -->

[Kit de ferramentas do Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Criar um aplicativo Web Hello World para o Azure no Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Criar um aplicativo Web Hello World para o Azure no IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalação do Kit de Ferramentas do Azure para o Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalação do Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novidades no Kit de Ferramentas do Azure para o Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novidades no Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Central de desenvolvimento Java do Azure]: http://go.microsoft.com/fwlink/?LinkID=699547

[Plug-in das Ferramentas do HDInsight para IntelliJ]: ./hdinsight/hdinsight-apache-spark-intellij-tool-plugin.md



<!--HONumber=Oct16_HO2-->


