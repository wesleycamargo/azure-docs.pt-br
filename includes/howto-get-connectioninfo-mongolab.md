Quando você configurar um banco de dados do MongoLab, o MongoLab transmitirá uma conexão de URI para o Azure no formato de cadeia de conexão padrão do MongoDB. Esse valor é usado para iniciar uma conexão por meio de sua escolha em MongoDB do MongoDB driver. Para obter mais informações sobre cadeias de conexão, consulte [conexões de](http://www.mongodb.org/display/DOCS/Connections) em mongodb.org.

**Esse URI contém o nome de usuário do seu banco de dados e senha.  Trate-o como informação confidencial e não o compartilhe.**

Você pode recuperar esse URI no Portal do Azure usando as seguintes etapas:

1. Selecione **Complementos**.  
![AddonsButton][button-addons]
1. Localize o serviço MongoLab na sua lista de complementos.  
![MongolabEntry][entry-mongolabaddon]
1. Clique no nome do seu complemento para chegar à página de complemento.
1. Clique em **Informações de Conexão**.  
![ConnectionInfoButton][button-connectioninfo]  
Sua URI do MongoLab exibe:  
![ConnectionInfoScreen][screen-connectioninfo]  
1.  Clique no botão da área de transferência à direita do valor MONGOLAB_URI para copiar o valor total para a área de transferência.

[entry-mongolabaddon]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
[button-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
[screen-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
[button-addons]: ./media/howto-get-connectioninfo-mongolab/button-addons.png

<!--HONumber=49-->