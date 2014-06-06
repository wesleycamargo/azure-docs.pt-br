Enquanto é possível colar um URI MongoLab no seu código, é recomendável configurá-lo no ambiente para facilidade de gerenciamento. Dessa forma, se o URI é alterado, você pode atualizá-lo por meio do Portal do Azure sem precisar ir até o código.


1. No Portal do Azure, selecione **Sites**
1. Na guia Sites, clique no nome do seu site na lista do site.  
![WebSiteEntry][entry-website]  
Exibe o painel de controle do Site da Web.

1. Clique em **configurar** na barra de menus.  
![WebSiteDashboardConfig][focus-mongolab-websitedashboard-config]

1. Role para baixo até a seção de cadeias de caracteres de conexão.  
![WebSiteConnectionStrings][focus-mongolab-websiteconnectionstring]

1. Para **nome**, digite MONGOLAB_URI.
1. Para **valor**, cole a string de conexão que obtivemos na seção anterior.
1. Selecione **personalizado** no **tipo** lista suspensa (em vez do padrão **SQLAzure**).
1. Clique em **salvar** na barra de ferramentas.  
![SaveWebSite][button-website-save]

**Observação:** O Azure adiciona o **CUSTOMCONNSTR\_** prefixo a esta variável, que é a razão dos códigos de referência acima **CUSTOMCONNSTR\_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

