
Em seguida, você precisa fazer alterações ao se registrar para notificações para assegurar que o usuário está autenticado antes da tentativa de registro.


1. No Gerenciador de Projetos no Android Studio, abra o arquivo ToDoActivity.java e localize o método `onCreate`. Mova o seguinte código do método `onCreate` para o início do método `createTable`.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

     O método `createTable` é chamado quando o método `authenticate` é concluído. Todo 0 seu método `createTable` deve ser semelhante ao seguinte.

        private void createTable() {
        
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        
            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);
            
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
            
            // Create an adapter to bind the items with the view
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
            
            // Load the items from the Mobile Service
            refreshItemsFromTable();
        }	

<!---HONumber=July15_HO4-->