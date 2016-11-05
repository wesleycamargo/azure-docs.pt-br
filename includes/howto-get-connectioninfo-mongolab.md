Quando você configurar um banco de dados do MongoLab, o MongoLab transmitirá uma conexão de URI para o Azure no formato de cadeia de conexão padrão do MongoDB. Esse valor é usado para iniciar uma conexão por meio de sua escolha em MongoDB do MongoDB driver. Para obter mais informações sobre cadeias de conexão, consulte [conexões de](http://www.mongodb.org/display/DOCS/Connections) em mongodb.org.

**Esse URI contém seu nome de usuário do banco de dados e senha. Tratá-lo como informações confidenciais e não a compartilhe.**

Você pode recuperar esse URI no Portal do Azure usando as seguintes etapas:

1. **Selecione Complementos**. ![AddonsButton][button-addons]
2. Localize o serviço MongoLab na sua lista de complementos. ![MongolabEntry][entry-mongolabaddon]
3. Clique no nome do seu complemento para chegar à página de complemento.
4. Clique em **Informações de Conexão**. ![ConnectionInfoButton][button-connectioninfo] Seu URI do MongoLab exibe: ![ConnectionInfoScreen][screen-connectioninfo]  
5. Clique no botão da área de transferência à direita do valor MONGOLAB\_URI para copiar o valor total para a área de transferência.

[entry-mongolabaddon]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
[button-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
[screen-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
[button-addons]: ./media/howto-get-connectioninfo-mongolab/button-addons.png

<!---HONumber=Oct15_HO3-->