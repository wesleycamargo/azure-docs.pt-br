<properties
   pageTitle="Perguntas frequentes"
   description="Perguntas Frequentes sobre o Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="06/28/2016"
   ms.author="owend"/>

# Perguntas Frequentes sobre o Power BI Embedded

1. **O que você anunciou sobre o Power BI ao Compilar?**

    A Microsoft anunciou que o Microsoft Power BI Embedded Service Public Preview está agora disponível.

2.	**O que é o Microsoft Power BI Embedded?**

    O Microsoft Power BI Embedded é um serviço do Azure que permite que os desenvolvedores de aplicativos insiram visualizações e relatórios impressionantes e totalmente interativos em seus aplicativos voltados para o cliente, sem o tempo nem as despesas de compilar seus próprios controles desde o início. Você pode escolher dentre uma ampla variedade de visualizações de dados que vêm integradas ou compilar facilmente visualizações personalizadas para atender às necessidades exclusivas do seu aplicativo. O Power BI Embedded destina-se a uso pelos desenvolvedores de aplicativos/ISV que desejam entregar o BI aos seus clientes como parte de seu aplicativo maior.

3.	**Quem gostaria de usar o Microsoft Power BI Embedded e por que?**

    O Microsoft Power BI Embedded é para desenvolvedores de aplicativos que desejam oferecer experiências de visualização de dados impressionantes e interativas para os seus usuários em qualquer um dos seus dispositivos, sem necessidade de compilá-las por conta própria. Com o Power BI Embedded, os desenvolvedores podem fornecer exibições sempre atualizadas com a **Consulta Direta**. Os desenvolvedores podem, também programaticamente, implantar, gerenciar e automatizar o Power BI com as APIs do ARM do Azure e APIs do Power BI. Assim como acontece com todas as coisas do Power BI, o serviço inserido automaticamente é dimensionado para atender às necessidades e ao uso do seu aplicativo. O serviço do Power BI Embedded apresenta um modelo de preços Pré-pago baseado em consumo. Para obter os preços, consulte [Quanto custa esse serviço?](#price).

4.	**Qual a relação entre o Power BI Embedded e o serviço do Power BI?**

    O Power BI Embedded e o serviço do Power BI são ofertas separadas. O Power BI Embedded conta com um modelo de cobrança baseado em consumo, é implantado por meio do portal do Azure e foi projetado para habilitar os ISVs a inserir visualizações de dados em aplicativos para que sejam usadas por seus respectivos clientes. O serviço do Power BI é cobrado e implantado por meio do portal do O365 e é uma oferta autônoma de BI para uso geral, destinada principalmente ao uso corporativo interno. Para obter mais informações sobre o serviço do Power BI, visite [www.powerbi.com](www.powerbi.com).

5.	**Como o Power BI Embedded aprimora meu aplicativo?**

    Os aplicativos são significativamente mais poderosos quando você aproveita visualizações de dados para informar decisões do usuário diretamente em seu aplicativo. O Power BI Embedded permite que você aprimore seu aplicativo com visualizações de dados interativas, avançadas e sempre atualizadas para que você possa aumentar a utilidade, a satisfação do usuário e a fidelidade do seu aplicativo, além de entregar análise contextual com facilidade em qualquer dispositivo.

6.	**Existem regras ou restrições sobre como é possível usar o Power BI Embedded no meu aplicativo?**

    Você poderá usar o serviço do Power BI Embedded em um aplicativo desenvolvido por você somente se o aplicativo: (1) adicionar funcionalidade primária e significativa ao nosso serviço do Power BI Embedded e não for basicamente um substituto de algum serviço do Power BI e (2) for fornecido exclusivamente para usuários externos à sua empresa. Você não pode usar o serviço do Power BI Embedded em aplicativos de negócios internos.

7.	**É possível usar o Power BI Embedded para criar aplicativos internos?**

    Não, o Power BI Embedded é destinado apenas para uso por usuários externos e não pode ser usado em aplicativos de negócios internos. Para inserir conteúdo do Power BI para uso em aplicativos de negócios internos, você deve usar o serviço do Power BI e todos os usuários consumindo esse conteúdo devem ter licença de assinatura de usuário do Power BI Gratuito ou do Power BI Pro. Mais informações sobre inserção interna usando o serviço do Power BI estão disponíveis em [https://dev.powerbi.com](https://dev.powerbi.com).

8.	**Este serviço está disponível globalmente?**

    O serviço do Power BI Embedded está disponível na América do Norte desde nosso comunicado no BUILD 2016 (no data center do Centro-Sul dos EUA). Você pode esperar que façamos a distribuição desse serviço para o restante dos data centers do Azure pouco tempo depois.

9. **Qual é o SLA disponível para o serviço?**

    O Power BI Embedded está agora disponível como uma preview de serviço do Azure sem um SLA formal. Um SLA será fornecido quando o serviço passar de preview para disponibilidade geral.

10. <a name="price"/>**Como esse serviço é cobrado?**

    O Power BI Embedded está atualmente em preview e estará disponível sem custos até 1º de maio de 2016. A partir de 1º de maio de 2016, o serviço será cobrado por renderização. Os clientes poderão comprar o serviço por meio de dois veículos de licenciamento principais: o MOSP (Microsoft Online Subscription Program) ou o Enterprise VL Program.

    Você poderá usar o serviço do Power BI Embedded em um aplicativo desenvolvido por você somente se o aplicativo: (1) adicionar funcionalidade primária e significativa ao nosso serviço e não for primariamente um substituto de algum serviço do Power BI e (2) for fornecido exclusivamente para usuários externos. Você não pode usar o serviço do Power BI Embedded em aplicativos de negócios internos.


   ![](media\power-bi-embedded-faq\price.png)

11. **O que é uma renderização e como ela é cobrada?**

    Uma renderização ocorre quando uma visualização é exibida para um usuário final que exige uma consulta para o serviço. Esse serviço do Power BI tenta armazenar conteúdo renderizado em cache sempre que possível para reduzir o número de renderizações pelas quais o seu aplicativo será cobrado. No entanto, ações do usuário como filtragem podem resultar em uma consulta ao nosso serviço, o que constitui uma nova renderização.

    Por exemplo, se um usuário exibir um relatório contendo quatro visuais, isso poderá resultar em quatro renderizações. Se o usuário atualizasse o relatório e mais consultas fossem enviadas ao serviço, isso resultaria em mais quatro renderizações. O proprietário do serviço terá controle sobre a extensão pela qual os usuários finais podem conduzir novas consultas que resultam em renderizações pagas para limitar a exposição do custo e minimizar os custos em cenários de dados estáticos.

    As renderizações são cobradas a cada 1.000 unidades. Para quantidades de renderizações inferiores a 1.000, a cobrança ocorre proporcionalmente. Os clientes recebem 1.000 renderizações gratuitas por mês. Os clientes que compram por contratos de Licenciamento por Volume devem consultar seu vendedor ou parceiro da Microsoft para obter informações sobre preços.

12. **Você oferece ferramentas ou diretrizes para ajudar a estimar quantas renderizações devem ser esperadas? Como saberei quantas renderizações foram concluídas?**

    O Portal do Azure fornecerá detalhes de cobrança sobre quantas renderizações foram executadas para a sua assinatura.

13. **Eu preciso de uma assinatura do Power BI para desenvolver aplicativos com o Power BI Embedded? Como começar?**

    Como desenvolvedor de aplicativos, você não precisa ter uma assinatura do Power BI para compilar relatórios e visualizações que deseja usar em seu aplicativo. Você precisará de uma assinatura do Microsoft Azure e do aplicativo Power BI Desktop gratuito.

    Consulte nossa documentação do serviço para obter detalhes sobre como usar o serviço do Power BI Embedded.

14. **Tenho uma assinatura do Azure. Posso usar o Power BI Embedded usando minha assinatura existente?**

    Sim. Você pode usar sua assinatura do Azure existente para provisionar e usar o serviço do Microsoft Power BI Embedded.

15. **O usuário final do meu aplicativo precisa de uma licença do Power BI?**

    Não. Os usuários finais do aplicativo não precisarão comprar uma assinatura separada do Power BI para acessar as visualizações de dados no aplicativo. No modelo do Power BI Embedded, o Provedor do Aplicativo, será cobrado pelo serviço por meio do medidor de consumo do Azure. Consulte a [página Preços e licenciamento](http://go.microsoft.com/fwlink/?LinkId=760527).

16. **Como a autenticação do usuário funciona com o Power BI Embedded?**

    O serviço Power BI Embedded usa tokens de aplicativo para autenticação e autorização, em vez de usar autenticação explícita do usuário final. No modelo de Token de Aplicativo, seu aplicativo gerencia a autenticação e autorização para os usuários finais. Então, quando necessário, seu aplicativo cria e envia os Tokens de Aplicativo que dizem ao nosso serviço para renderizar o relatório solicitado. Esse design não requer que o aplicativo use o Azure Active Directory para autorização e autenticação de usuário, embora você possa fazer isso. Para saber mais sobre Tokens de Aplicativo, veja a página de documentação [Tokens de Aplicativo](https://azure.microsoft.com/pt-BR/documentation/articles/power-bi-embedded-get-started-sample/#key-flow).

17. **Para quais fontes de dados há suporte com o Power BI Embedded?**

    Durante a preview pública do serviço, daremos suporte ao acesso a fontes de dados em nuvem que usem credenciais básicas por meio de Consulta Direta. Isso significa que fontes como BD SQL do Azure, HDInsight Spark e DW do Azure SQL têm suporte no momento. Adicionaremos suporte para outras fontes de dados e tipos de acesso nos próximos meses. Anunciaremos novas fontes de dados com suporte no site do desenvolvedor do Power BI em [http://dev.powerbi.com](http://dev.powerbi.com/).

18. **Como funciona o modelo de locação para o Power BI Embedded?**

    No modelo do Power BI Embedded, não há nenhum requisito explícito para ter seus clientes em locatários do Azure Active Directory (Azure AD). Você pode optar por exigir ou não o Azure AD dos seus clientes. Como resultado, a arquitetura do seu aplicativo e a infraestrutura serão os fatores determinantes para o modelo de locação exigido para o Power BI Embedded.

    Os desenvolvedores/funcionários trabalhando para compilar seu aplicativo precisarão ter uma conta de usuário do Azure AD para que possam gerenciar sua assinatura do Azure e Coleções de Espaço de Trabalho por meio do Portal do Azure. APIs programáticas que permitem aos desenvolvedores importar relatórios, modificar cadeias de conexão e fazer com que as URLs de inserção usem tokens de aplicativo para autenticação em seu lugar, de modo que, como resultado, não exigem o Azure AD. Detalhes sobre como usar nossas APIs e o Portal do Azure podem ser encontrados na [página de documentação do Power BI Embedded no Azure.com](https://azure.microsoft.com/pt-BR/documentation/services/power-bi-embedded/).

19. **Onde posso saber mais?**

    Você pode visitar a [página de documentação do Power BI Embedded](http://go.microsoft.com/fwlink/?LinkId=760526). Você pode manter-se atualizado sobre esse serviço visitando o [blog do desenvolvedor do Power BI](http://blogs.msdn.com/powerbidev) ou visitando o centro de desenvolvedores do Power BI em dev.powerbi.com. Você também pode fazer perguntas no [Stackoverflow](http://stackoverflow.com/questions/tagged/powerbi).

20. **Como começar?**

    Você pode começar gratuitamente agora! Se você tiver uma assinatura do Azure, você poderá então provisionar o Power BI Embedded diretamente do portal do Azure. Você também pode criar uma [conta gratuita do Azure](https://azure.microsoft.com/free/). Depois de provisionar o serviço Power BI Embedded, você poderá usar com facilidade APIs REST do Power BI diretamente ou usar o SDK de desenvolvedor disponível no [GitHub](http://go.microsoft.com/fwlink/?LinkID=746472). São fornecidas amostras de como utilizar o SDK de desenvolvedor.

## Consulte também

- [O que é o Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Introdução ao Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0629_2016-->