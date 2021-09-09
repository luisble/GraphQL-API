# GraphQL-API
Exemplo de CRUD utilizando **GraphQL** com **Python** e **Flask**

Baseado no artigo: https://www.apollographql.com/blog/graphql/python/complete-api-guide/


Crie um ambiente virtual e ative.

```
python -m venv .venv
```

Depois na pasta .venv\Scripts execute o activate

No **PowerShell**

```
.venv\Scripts\activate.ps1
```

No **Linux**

```
source .venv/bin/activate
```

Instale as bibliotecas necessárias com o comando 

```
python -m pip install --no-cache-dir -r requirements.txt
```


Criar uma instância no **ElephantSQL**

Lembrar da estrutura da URL do **PostgreSQL** quando for conectar com o **DBeaver** ou **pgAdmin**

```
postgres://username:password@hostname/databasename
```

Porém ao utilizar o SQLAlchemy lembrar que é "postgresql://", sendo assim no Python ela ficará:

```
postgresql://username:password@hostname/databasename
```

Crie uma variável de ambiente FLASK_APP com o valor "app.py"

No **PowerShell**: 

    $env:FLASK_APP="app.py"

No **Linux**: 

```
export FLASK_APP=app.py
```

Para criar o banco de dados, configure o arquivo **\__init__.py** da pasta api com a url do seu banco de dados

depois execute as linhas abaixo no terminal do Python. Estas linhas criarão a tabela na base de dados e incluirão dois registros de postagens:

```python
>>> from app import db
>>> db.create_all()
>>> from datetime import datetime
>>> from api.models import Post
>>> current_date = datetime.today().date()
>>> new_post = Post(title="A new morning", description="A new morning details", created_at=current_date)
>>> db.session.add(new_post)
>>> db.session.commit()
>>> new_post = Post(title="Good Night", description="Have a good night and better dreams", created_at=current_date)
>>> db.session.add(new_post)
>>> db.session.commit()
>>> exit()
```



Saia do terminal do Python e da linha de comando execute o Flask para subir a aplicação:

```
flask run
```

Uma tela como na imagem abaixo será retornada:

![image-20210909152320584](https://github.com/luisble/GraphQL-API/blob/main/image-20210909152320584.png)



Se não teve nenhum erro, em um browser digite a seguinte URL:

http://127.0.0.1:5000/

A aplicação retornará um OK, como a imagem abaixo

![image-20210909152737944](https://github.com/luisble/GraphQL-API/blob/main/image-20210909152737944.png)

Pronto, agora é só ir chamando as GraphQL Queries, conforme abaixo. Utilize o [Postman](https://www.postman.com/)  ou  o [Apollo Studio](https://studio.apollographql.com/) para testar as chamadas

Lembrar que de utilizar o método POST do HTTP para as chamadas.

Abaixo estão os exemplos de Querys e de Mutation

**Querys**

getPost - Consulta de um Post específico

listPosts - Listagem de todos os Posts

**Mutation**

createPost - Criação de um Post

updatePost - Atualização de um Post

deletePost - Exclusão de Post



**Query - getPost:**

```
query Query($getPostId: ID!) {
  getPost(id: $getPostId) {
    success
    errors
    post {
      id
      title
      description
      created_at
    }
  }
}
```


GRAPHQL VARIABLES: JSON:

```
{
  "getPostId": "1"
}
```

Response:

```
{
  "data": {
    "getPost": {
      "errors": null,
      "post": {
        "created_at": "08-09-2021",
        "description": "A new morning details",
        "id": "1",
        "title": "A new morning"
      },
      "success": true
    }
  }
}
```

GRAPHQL VARIABLES: JSON:

```
{
  "getPostId": "2"
}
```

Response:

```
{
  "data": {
    "getPost": {
      "errors": null,
      "post": {
        "created_at": "09-09-2021",
        "description": "Have a good night and better dreams",
        "id": "2",
        "title": "Good Night"
      },
      "success": true
    }
  }
}
```

**Query  - listPosts:**

```
query Query {
  listPosts {
    success
    errors
    posts {
      id
      title
      description
      created_at
    }
  }
}
```

Response

```
{
  "data": {
    "listPosts": {
      "errors": null,
      "posts": [
        {
          "created_at": "08-09-2021",
          "description": "A new morning details",
          "id": "1",
          "title": "A new morning"
        },
        {
          "created_at": "09-09-2021",
          "description": "Have a good night and better dreams",
          "id": "2",
          "title": "Good Night"
        }
      ],
      "success": true
    }
  }
}
```

**Mutation - createPost:**

```
mutation Mutation($createPostTitle: String!, $createPostDescription: String!, $createPostCreatedAt: String) {
  createPost(title: $createPostTitle, description: $createPostDescription, created_at: $createPostCreatedAt) {
    success
    errors
    post {
      id
      title
      description
      created_at
    }
  }
}
```

GRAPHQL VARIABLES: JSON:

```
{
  "createPostTitle": "Tomorrow",
  "createPostDescription": "I will go to Tomorrowland!",
  "createPostCreatedAt": null
}
```

Response:

```
{
  "data": {
    "createPost": {
      "errors": null,
      "post": {
        "created_at": "09-09-2021",
        "description": "I will go to Tomorrowland!",
        "id": "3",
        "title": "Tomorrow"
      },
      "success": true
    }
  }
}
```

GRAPHQL VARIABLES: JSON:

```
{
  "createPostTitle": "A long time ago",
  "createPostDescription": "You never walk alone!",
  "createPostCreatedAt": "05-09-2021"
}
```

Response:

```
{
  "data": {
    "createPost": {
      "errors": null,
      "post": {
        "created_at": "05-09-2021",
        "description": "You never walk alone!",
        "id": "4",
        "title": "A long time ago"
      },
      "success": true
    }
  }
}
```

**Mutation - updatePost:**

```
mutation Mutation($updatePostId: ID!, $updatePostTitle: String, $updatePostDescription: String) {
  updatePost(id: $updatePostId, title: $updatePostTitle, description: $updatePostDescription) {
    success
    errors
    post {
      id
      title
      description
      created_at
    }
  }
}
```

GRAPHQL VARIABLES: JSON:

```
{
  "updatePostId": "4",
  "updatePostTitle": "A long time ago",
  "updatePostDescription": "A long time ago in a galaxy far far away"
}
```

Response:

```
{
  "data": {
    "updatePost": {
      "errors": null,
      "post": {
        "created_at": "05-09-2021",
        "description": "A long time ago in a galaxy far far away",
        "id": "4",
        "title": "A long time ago"
      },
      "success": true
    }
  }
}
```

**Mutation - deletePost:**

```
mutation Mutation($deletePostId: ID) {
  deletePost(id: $deletePostId) {
    success
    errors
    post {
      id
      title
      description
      created_at
    }
  }
}
```

GRAPHQL VARIABLES: JSON:

```
{
  "deletePostId": "4"
}
```

Response:

```
{
  "data": {
    "deletePost": {
      "errors": null,
      "post": {
        "created_at": "05-09-2021",
        "description": "A long time ago in a galaxy far far away",
        "id": "4",
        "title": "A long time ago"
      },
      "success": true
    }
  }
}
```

