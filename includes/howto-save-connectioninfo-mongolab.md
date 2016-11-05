Enquanto é possível colar um URI MongoLab no seu código, é recomendável configurá-lo no ambiente para facilidade de gerenciamento. Dessa forma, se o URI for alterado, você poderá atualizá-lo por meio do Portal do Azure sem precisar acessar o código.

1. No Portal do Azure, selecione **Aplicativos Web**.
2. Clique no nome do aplicativo Web na lista de Aplicativos Web. ![WebAppEntry][entry-website] O painel de Aplicativos Web é exibido.
3. Clique em **Configurar** na barra de menus. ![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]
4. Role para baixo até a seção Cadeias de Conexão. ![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]
5. Para **nome**, digite MONGOLAB\_URI.
6. Para **valor**, cole a cadeia de conexão que obtivemos na seção anterior.
7. Selecione **personalizado** no **tipo** lista suspensa (em vez do padrão **SQLAzure**).
8. Clique em **Salvar** na barra de ferramentas. ![SaveWebApp][button-website-save]

**Observação:** O Azure adiciona o prefixo **CUSTOMCONNSTR\_** a esta variável, que é a razão de o código acima fazer referência a **CUSTOMCONNSTR\_MONGOLAB\_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

<!---HONumber=Oct15_HO3-->