Quando você configurar um banco de dados do MongoLab, o MongoLab transmitirá uma conexão de URI para o Azure no formato de cadeia de conexão padrão do MongoDB. Esse valor é usado para iniciar uma conexão por meio de sua escolha em MongoDB do MongoDB driver. Para obter mais informações sobre cadeias de conexão, consulte [conexões de][conexões de] em mongodb.org.

**Esse URI contém seu nome de usuário do banco de dados e senha. Tratá-lo como informações confidenciais e não a compartilhe.**

Você pode recuperar esse URI no Portal do Azure usando as seguintes etapas:

1.  Selecione **Complementos**.  
    ![AddonsButton][AddonsButton]
2.  Localize o serviço MongoLab na sua lista de complementos.  
    ![MongolabEntry][MongolabEntry]
3.  Clique no nome do seu complemento para chegar à página de complemento.  
4.  Clique em **Informações de Conexão**.  
    ![ConnectionInfoButton][ConnectionInfoButton]  
    O URI do MongoLab exibe:  
    ![ConnectionInfoScreen][ConnectionInfoScreen]  
5.  Clique no botão da área de transferência à direita do valor MONGOLAB\_URI para copiar o valor total para a área de transferência.

  [conexões de]: http://www.mongodb.org/display/DOCS/Connections
  [AddonsButton]: ./media/howto-get-connectioninfo-mongolab/button-addons.png
  [MongolabEntry]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
  [ConnectionInfoButton]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
