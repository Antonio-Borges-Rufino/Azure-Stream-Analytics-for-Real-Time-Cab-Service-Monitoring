# Diagrama
![image](https://github.com/Antonio-Borges-Rufino/Build-Streaming-Data-Pipeline-using-Azure-Stream-Analytics/assets/86124443/480f3612-0e16-4033-b723-e570f58a929d)
# Passos
* Crie um hub de eventos do Azure.
* Crie um trabalho de análise de fluxo do Azure.
* Crie um banco de dados SQL do Azure.
* Crie estrutura de tabela no banco de dados SQL.
* Adicione o centro de eventos como fonte de entrada para o trabalho de análise de fluxo
* Adicione o banco de dados SQL do Azure como fonte de saída para o trabalho de análise de fluxo
* Inicie o trabalho de análise de fluxo.
* Execute o script python, que enviará dados de streaming do hub de eventos para o banco de dados SQL do Azure.
* Carregue dados de tabelas de banco de dados SQL do Azure no Power BI.
* Crie um painel no Power BI.
# Crie um hub de eventos Azure
* Para criar um hub de eventos, antes, precisamos criar um grupo de recursos
* Depois de criar o grupo de recursos, vá no marketplace e crie um hub de eventos
* ![image](https://github.com/Antonio-Borges-Rufino/Build-Streaming-Data-Pipeline-using-Azure-Stream-Analytics/assets/86124443/1903fa28-4d57-4ee0-9888-b00e01ae7286)
* As informações do meu hub de eventos está a seguir. Eu cobri algumas que são informações das configurações pessoais da azure
* ![image](https://github.com/Antonio-Borges-Rufino/Build-Streaming-Data-Pipeline-using-Azure-Stream-Analytics/assets/86124443/5ffe20f2-d2b6-40ff-9819-4fecc448d9ca)
* O que fiz foi criar um namespace para o hub de evento. Você pode acessar seu namespace através do grupo de serviço
* Após acessar meu namespace do hub de eventos, vou criar de fato o hub de evento que vou usar
* ![image](https://github.com/Antonio-Borges-Rufino/Build-Streaming-Data-Pipeline-using-Azure-Stream-Analytics/assets/86124443/5368825b-876e-4e38-aa1e-afd033550c01)
* O meu hub de eventos ficou com essa caracteristica
* ![image](https://github.com/Antonio-Borges-Rufino/Build-Streaming-Data-Pipeline-using-Azure-Stream-Analytics/assets/86124443/fd0580bb-3bcc-4e63-887f-07eef1d61534)
# Script Python de Acesso Ao Event Hub
* Como uma parte adicional, vamos criar um script python que acesse o event hub e envie eventos para ele
* Primeiro importamos todas as bibliotecas necessárias, vamos conversar com o event hub através de arquivos JSON
* ```
  import time
  import json
  import random
  from azure.eventhub import EventHubProducerClient, EventData
  ```
* Agora vou definir a string de conexão com o meu event hub
* ```
  connection_str = 'string_conect'
  eventhub_name = 'name_hub'
  ```
* Para saber sua string de conexão, vá até o event hub que quer enviar mensagem e acesse porlitica de acesso compartilhado, vá até a chave que quiser usar e copie, como na imagem abaixo
* ![image](https://github.com/Antonio-Borges-Rufino/Build-Streaming-Data-Pipeline-using-Azure-Stream-Analytics/assets/86124443/4b4940df-8907-4c46-bca4-f3e7cd3fb948)
* O próximo passo é construir uma classe que vai receber os atributos que vamos enviar como mensagem
* ```
  class Transport:   
    def __init__(self, id_corrida, n_pessoas, custo, distancia, nota):
        self.id_corrida = id_corrida
        self.n_pessoas = n_pessoas
        self.custo = custo
        self.distancia = distancia
        self.nota = nota
    def __str__(self):
        return f"{self.id_corrida}, {self.n_pessoas}, {self.custo}, {self.distancia}, {self.nota}"
  ```
* Essa classe vai receber os atributos e vai virar um json que vamos enviar para o event hub
* Agora vamos criar a função que vai enviar os eventos a partir da biblioteca do event hub
* ```
    def send_to_eventhub(client, data):
      event_data_batch = client.create_batch()
      event_data_batch.add(EventData(data))
      client.send_batch(event_data_batch)
  ```
* A função recebe como parâmetro o client, que é a conexão que utiliza a string de conexão e a data, que é a classe transport retornada em json
* Depois ele envia os dados através do send_batch
* Agora vamos criar a função que vai instanciar as classes, transformar o objeto em json e vai enviar a requisão
* ```
    def main(count):
      client = EventHubProducerClient.from_connection_string(connection_str, eventhub_name=eventhub_name)
      n_pessoas = random.randint(0,4)
      custo = random.uniform(2.0, 80.0)
      distancia = random.uniform(1.0, 100.0)
      nota = random.randint(1,5)
      weather = Transport(count,n_pessoas,custo,distancia,nota)
      send_to_eventhub(client, json.dumps(weather.__dict__))
      print(json.dumps(weather.__dict__))
  ```
* A função recebe um parâmetro count onde eu vou passar o id_corrida para ela, isso no mundo real não é assim, mas como é só uma implementação para simular um streaming de dados. vou deixar.
* Depois instanciamos EventHubProducerClient que recebe as strings de conexão, é ele quem faz a conexão com o hub event
* As variaveis antes da instancia da classe de transport apenas criam valores aleatorios que vão ser passados como parâmetro para a classe transport
* Instancio a classe transport com os valores do que seria a informação passada por um usuario e depois envio a informação através da função send_to_eventhub
* Os dados são enviados em uma transformação da classe em um jason
* Depois eu printo os dados
* Por fim, vou criar um looping que vai ficar enviando os eventos em determinado tempo
* ```
    for count in range(1,150):
      main(count)
      time.sleep(30)
  ```
* O looping mostre que de 30 em 30 segundos enviamos uma mensagem para o hub de eventos
* A imagem abaixo mostra uma parte do gráfico
* ![image](https://github.com/Antonio-Borges-Rufino/Build-Streaming-Data-Pipeline-using-Azure-Stream-Analytics/assets/86124443/ab540023-4522-4cac-8bc3-8f2f2f6db349)
* O código está funcionando
# Crie um banco de dados SQL do Azure
* Para criar um banco de dados no Azure vá no marketplace e procure por "Banco de Dados SQL"
* Agora, crie um novo banco de dados
* Para criar um novo banco de dados, antes é preciso criar um novo servidor de banco de dados. Para isso clique na opção como na figura abaixo
* ![image](https://github.com/Antonio-Borges-Rufino/Build-Streaming-Data-Pipeline-using-Azure-Stream-Analytics/assets/86124443/93380b0d-64ea-43bc-85a1-7e76a67b6892)
* Para criar o servidor, use a autenticação SQL
* Configure o servidor da forma que preferir
* Para o banco de dados, as configurações foram as seguintes
* ```
  Básico
  Assinatura: --------
  Grupo de recursos: --------
  Região: East US
  Nome do banco de dados: -------
  Servidor: --------------
  Método de autenticação: Autenticação do SQL
  Logon do administrador do servidor: ---------
  Computação + armazenamento: Uso Geral - Sem servidor: Série Standard (Gen5), 1 vCore, 32 GB de armazenamento, zona redundante desabilitada
  Redundância do armazenamento de backup: Armazenamento de backup com redundância local
  Rede 
  Permitir que serviços e recursos do Azure acessem este servidor: Sim
  Adicionar o endereço IP do cliente atual -------: Não
  Ponto de extremidade privado: Nenhum
  Versão mínima do TLS: 1.2
  Política de Conexão: Default
  Segurança
  Identidade: Não habilitado
  Transparent Data Encryption (Nível do servidor): Chave gerenciada por serviço selecionada
  Chave gerenciada pelo cliente no nível do banco de dados: Não configurado
  Identidade gerenciada atribuída pelo usuário no nível do banco de dados: Não configurado
  Segurança de dados avançada: Agora não
  Always encrypted with secure enclaves: Não configurado
  Sql Razão (Banco de dados): Desabilitado
  Digerir armazenamento: Desabilitado
  Configurações adicionais 
  Usar dados existentes: Blank
  Ordenação: SQL_Latin1_General_CP1_CI_AS
  Janela de manutenção: Padrão do sistema (17h às 8h)
  ```
* Depois de criar o servidor, precisamos habilitar os ips de acesso, para fazer isso, vá até grupos de recurso, entre no servidor de banco de dados, segurança e rede e adicione uma regra de acesso. A imagem abaixo mostra o recurso
* ![image](https://github.com/Antonio-Borges-Rufino/Build-Streaming-Data-Pipeline-using-Azure-Stream-Analytics/assets/86124443/2c294baf-4769-483b-b100-14d56a73b8bf)
* Agora vamos para o nosso banco de dados, para isso, vá até o grupo de recurso e acesse o recurso "Banco de Dados SQL"
* Depois de acessar o recurso, vá até editor de consultas e acesse usando a autenticação SQL
* ![image](https://github.com/Antonio-Borges-Rufino/Build-Streaming-Data-Pipeline-using-Azure-Stream-Analytics/assets/86124443/45d34493-ea8c-4cd1-a14b-d49c183643dd)

  
# Crie um trabalho de análise de fluxo do Azure.
* Primeiro, vá no marketplace e procure por trabalhos do stream analytics e adicione um novo
* A minha config é essa
* ![image](https://github.com/Antonio-Borges-Rufino/Build-Streaming-Data-Pipeline-using-Azure-Stream-Analytics/assets/86124443/0ff95775-ffe2-4642-91e2-abf9c508d278)
* Após criar, vá ate o grupo de recursos e acesso o recurso trabalhos do stream analytics
* Nele, vamos criar conexções para conseguir ler e trabalhar com os dados em stream
* Para isso, crie uma nova entrada e coloque a entrada como sendo um hub event, como na imagem abaixo
* ![image](https://github.com/Antonio-Borges-Rufino/Build-Streaming-Data-Pipeline-using-Azure-Stream-Analytics/assets/86124443/67625078-d8ac-468c-90b0-3fcaa834b633)
* Configure com as configurações já feitas anteriormente utilizando os botões "usar existente". Em modo de autenticação, use o modo atribuido pelo sistema





