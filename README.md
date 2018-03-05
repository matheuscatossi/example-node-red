# example-node-red

* Vamos criar uma interface que receba um texto em um campo input, interprete e indentifique em que caso ele irá cair no Conversation, converter a resposta em áudio e listar todos os casos de input e output. Além disso, deixar bonitinho ;)

* [Exemplo funcional](https://example-node-red-catossi.mybluemix.net/template-input) - Neste link você pode ver o resultado

## Iniciando 

### Faça seu cadastro no IBM Cloud

* [IBM Cloud](console.bluemix.net) - São conjuntos de ferramentas e serviços de infraestrutura e cognitivos.

### Crie os serviços
* [Speech to Text](https://console.bluemix.net/catalog/services/text-to-speech?taxonomyNavigation=apps) 
* [Conversation](https://console.bluemix.net/catalog/services/conversation?taxonomyNavigation=apps) 
* [Node-Red](https://console.bluemix.net/catalog/starters/node-red-starter?taxonomyNavigation=apps) 

### Abra o seu serviço Node-Red (Lembre-se que o nome que der será sua URL)
* Clique em: Visite App URL (em português)
* Dê: Next, Next, Finish.
* Em algum momento irá pedir usuário e senha, recomendo utilizar a mesma do IBM Cloud.


### Antes de tudo
* Vá ao IBM Cloud, na sua instância de Node-red e crie conexões com os serviços de Conversation e Text to Speech. (Você vai reparar que já tem a conexão com o Cloudant, ele cria para guardar as infos dos nós.)

### Primeiro fluxo
* Adicione: Input HTTP
  - Em URL adicione: "/input" (sem apas, please)
* Adicione: Function
  - No campo Function adicione: 
    ```
      msg.payload = String(msg.req.query.text);
      return msg;
    ```
* Adicione: Conversation
  - Não se esqueça de adicionar a worskpace.
* Adicione: Function
  - No campo Function adicione:
    ```
    msg.payload = msg.payload.output.text[0];
    return msg;
    ```
* Adicione: Text to Speech
* Adicione: Function
  - No campo Function adicione:
    ```
    msg.headers={ 'Content-Type': 'audio/wav'};
    return msg;
    ```
* Adicione um response HTTP
* Volte ao nó de conversation e adicione: Function
* Adicione: Cloudant Out
  - Informe o database: nodered (ou se preferir crie outro)
  - Selecione a opção insert 
* Resultado esperado:  
![Resultado esperado](https://github.com/matheuscatossi/example-node-red/blob/master/resultnode1.png)

### Segundo fluxo
* Adicione: Input HTTP
* Adicione: Cloudant In 
  - Em "Search By" selecione: All Documents
* Adicione: Function
* Adicione: Template Node
  - Na linha Format selecione: Mustache template
  - Na linha Syntax Highlight: Mustache
  - No campo Template adicione: 
  ```
    <!-- Latest compiled and minified CSS -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">

    <!-- Optional theme -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap-theme.min.css" integrity="sha384-rHyoN1iRsVXV4nD0JutlnGaslCJuC7uwjduW9SVrLvRYooPp2bWYgmgJQIXwl/Sp" crossorigin="anonymous">

    <!-- Latest compiled and minified JavaScript -->
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>

    <style>
        .gap-top10 {
            margin-top: 10px;
        }

    </style>

    <div class="container">
        <div class="panel panel-default gap-top10">
            <div class="panel-heading">
                <h3>Digite o texto</h3>
            </div>
            <div class="panel-body">
                <form action="https://example-node-red-catossi.mybluemix.net/template-output/" method="get">
                    <div class="row">
                        <div class="col-xs-8">
                            <input type="text" class="form-control" name="text" id="" value="" />
                        </div>
                        <div class="col-xs-4">
                            <input type="submit" class="form-control btn btn-primary" value="Interprete!"/>
                        </div>
                    </div>
                 </form>
            </div>
        </div>
        <table class="table table-striped table-bordered">
            <thead>
                <tr>
                    <th>
                        Input
                    </th>
                    <th>
                        Output
                    </th>
                </tr>
            </thead>
            <tbody>
                {{#payload}}
                    {{#input.text}}
                        <tr><td>{{input.text}}</td>
                {{/input.text}}
                {{#output.text}}
                    <td>{{output.text}}</td></tr>
                {{/output.text}}

                {{/payload}}
            </tbody>
        </table>
    </div>
* Adicione: response HTTP
* Resultado esperado: 
![Resultado esperado](https://github.com/matheuscatossi/example-node-red/blob/master/resultnode2.png)

### Terceiro (e último) fluxo
* Adicione um Input HTTP
* Adicione um Function
  - No campo Function adicione:
  ```
  msg.request = msg.payload;
  return msg;
  ```
* Adicione: Cloudant In 
  - Em "Search By" selecione: All Documents
* Adicione um Function
  - No campo Function adicione:
  ```
  msg.cloudant = msg.payload;
  msg.payload = msg.request;
  return msg;
  ```
* Adicione: Template Node
  - Na linha Format selecione: Mustache template
  - Na linha Syntax Highlight: Mustache
  - No campo Template adicione: 
  ```
      <!-- Latest compiled and minified CSS -->
      <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">

      <!-- Optional theme -->
      <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap-theme.min.css" integrity="sha384-rHyoN1iRsVXV4nD0JutlnGaslCJuC7uwjduW9SVrLvRYooPp2bWYgmgJQIXwl/Sp" crossorigin="anonymous">

      <!-- Latest compiled and minified JavaScript -->
      <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>

      <style>
          .gap-top10 {
              margin-top: 10px;
          }
      </style>

      <div class="container">
          <div class="panel panel-default gap-top10">
              <div class="panel-heading">
                  <h3>Digite o texto</h3>
              </div>
              <div class="panel-body">
                  <div class="row">
                      <div class="col-xs-12">
                          <h1>Wow.. vamos ver se existe algo criado para isso</h1>
                      </div>
                  </div>
                  <div class="row">
                      <div class="col-xs-12">
                          <p><q>Você escreveu: {{payload.text}}</q></p>
                      </div>
                  </div>
                  <div class="row">
                      <div class="col-xs-12">
                          <audio controls autoplay>
                            <source src="https://example-node-red-catossi.mybluemix.net/input?text={{payload.text}}" type="audio/wav">
                          </audio>
                      </div>
                  </div>
                  <form action="https://example-node-red-catossi.mybluemix.net/template-output/">
                      <div class="row">
                          <div class="col-xs-8">
                              <input type="text" class="form-control" name="text" id="" value="{{payload.text}}" />
                          </div>
                          <div class="col-xs-4">
                              <input type="submit" class="form-control btn btn-primary" value="Tentar novamente" />
                          </div>
                      </div>
                   </form>
              </div>
          </div>

          <table class="table table-striped table-bordered">
              <thead>
                  <tr>
                      <th>
                          Input
                      </th>
                      <th>
                          Output
                      </th>
                  </tr>
              </thead>
              <tbody>
                  {{#cloudant}}
                      {{#input.text}}
                          <tr><td>{{input.text}}</td>
                  {{/input.text}}
                  {{#output.text}}
                      <td>{{output.text}}</td></tr>
                  {{/output.text}}

                  {{/cloudant}}
              </tbody>
          </table>
      </div>

* Adicione: response HTTP
* Resultado esperado: 
![Resultado esperado](https://github.com/matheuscatossi/example-node-red/blob/master/resultnode3.png)

# Obrigado!
