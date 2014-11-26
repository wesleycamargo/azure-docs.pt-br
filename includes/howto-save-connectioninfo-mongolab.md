Enquanto é possível colar um URI MongoLab no seu código, é recomendável configurá-lo no ambiente para facilidade de gerenciamento. Dessa forma, se o URI for alterado, você poderá atualizá-lo por meio do Portal do Azure sem precisar acessar o código.

1.  No Portal do Azure, selecione **Sites**
2.  Clique no nome do site na lista de sites.  
    ![WebSiteEntry][WebSiteEntry]
    O Painel do Site é exibido.

3.  Clique em **Configurar** na barra de menus.  
    ![WebSiteDashboardConfig][WebSiteDashboardConfig]

4.  Role para baixo até a seção de cadeias de conexão.  
    ![WebSiteConnectionStrings][WebSiteConnectionStrings]

5.  Para **nome**, digite MONGOLAB\_URI.
6.  Para **valor**, cole a cadeia de conexão que obtivemos na seção anterior.
7.  Selecione **personalizado** no **tipo** lista suspensa (em vez do padrão **SQLAzure**).
8.  Clique em **Salvar** na barra de ferramentas.  
    ![SaveWebSite][SaveWebSite]

**Observação:** O Azure adiciona o prefixo **CUSTOMCONNSTR\_** a esta variável, que é o motivo pelo qual o código acima faz referência a **CUSTOMCONNSTR\_MONGOLAB\_URI.**

  [WebSiteEntry]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
  [WebSiteDashboardConfig]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
  [WebSiteConnectionStrings]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
  [SaveWebSite]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
