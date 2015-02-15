Enquanto é possível colar um URI MongoLab no seu código, é recomendável configurá-lo no ambiente para facilidade de gerenciamento. Dessa forma, se o URI for alterado, você poderá atualizá-lo por meio do Portal do Azure sem precisar acessar o código.


1. No Portal do Azure, selecione **Sites**.
1. Clique no nome do site na lista de sites.  
![WebSiteEntry][entry-website]  
O Painel do Site é exibido.

1. Clique em **configurar** na barra de menus.  
![WebSiteDashboardConfig][focus-mongolab-websitedashboard-config]

1. Role para baixo até a seção de cadeias de caracteres de conexão.  
![WebSiteConnectionStrings][focus-mongolab-websiteconnectionstring]

1. Para **nome**, digite MONGOLAB_URI.
1. Para **valor**, cole a cadeia de conexão que obtivemos na seção anterior.
1. Selecione **personalizado** no **tipo** lista suspensa (em vez do padrão **SQLAzure**).
1. Clique em **salvar** na barra de ferramentas.  
![SaveWebSite][button-website-save]

**Observação:** O Windows Azure adiciona o prefixo **CUSTOMCONNSTR\_** a esta variável, que é a razão de o código acima fazer referência a **CUSTOMCONNSTR\_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

<!--HONumber=42-->
