# example-node-red
Neste exemplo em nodered utilizo APIs como Text to Speech, Cloudant e Conversation. Além de utilizar bootstrap para customizar o template.


## Iniciando 

### Faça seu cadastro no IBM Cloud

* [IBM Cloud](console.bluemix.net) - São conjuntos de ferramentas e serviços de infraestrutura e cognitivos.

### Crie os serviços e ferramentas
* [Speech to Text](https://console.bluemix.net/catalog/services/text-to-speech?taxonomyNavigation=apps) 
* [Conversation](https://console.bluemix.net/catalog/services/conversation?taxonomyNavigation=apps) 
* [Node-Red](https://console.bluemix.net/catalog/starters/node-red-starter?taxonomyNavigation=apps) 

### Abra o seu serviço Node-Red (Lembre-se que o nome que der será sua URL)

* Clique em: Visite App URL (em português)
* Dê: Next, Next, Finish.
* Em algum momento irá pedir usuário e senha, recomendo utilizar a mesma do IBM Cloud.

### O que faremos???

* Vamos criar uma interface que receba um texto em um campo input, interprete e indentifique em que caso ele irá cair no Conversation, converter a resposta em áudio e listar todos os casos de input e output. Além disso, deixar bonitinho ;)

### Primeiros passos

* Vá ao IBM Cloud, na sua instância de Node-red e crie conexões com os serviços de Conversation e Text to Speech. (Você vai reparar que já tem a conexão com o Cloudant, ele cria para guardar as infos dos nós.)

#### Vamos criar o primeiro fluxo
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

