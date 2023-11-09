# Referência
* O projeto aqui descrito é da plataforma projectpro, para mais informações clique [aqui](https://www.projectpro.io/project-use-case/azure-stream-analytics-cab-service-monitoring)
# Descrição do Projeto
* Nesse projeto, vamos implementar no azure um sistema de monitoramento em tempo real de seviços de taxi (Como uber, 99 e etc). E analisar os dados em tempo real utilizando o power by
# Arquitetura do sistema
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/398c594f-a7d3-4fdc-a647-ad2cc36a7433)
# Stack do Projeto 
* Usamos centros de eventos como ponto de ingestão para detalhes de reserva de táxi/desembarque. Simulamos dados em tempo real usando Azure VM executando um código gerador.
* Usamos análise de fluxo para juntar alguns dados de referência, como detalhes do cliente e detalhes do proprietário do táxi, que são mantidos no armazenamento de Blobs. Os dados combinados são usados ​​para derivar múltiplas tendências, como comissão média por km, rotas com reservas máximas (para identificar tendências).
* Manteremos uma cópia dos dados no banco de dados cosmos No-SQL, que alimentará os painéis do Power BI para compreensão dos usuários corporativos.
* Como estamos usando um serviço de streaming e análise em tempo real, talvez seja necessário monitorá-lo e acioná-lo em caso de sobrecarga de entrada por meio do serviço de e-mail para o administrador do serviço.
# Criar um grupo de recursos
# Criar um Event Hub
* Event Hub é um recurso da Azure que trata dados em streaming. (O kafka da azure)
* Para criar, clica em adicionar um recurso e procure event hub
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/4d04281e-72a9-45aa-b82e-33d4aebce9f7)
* Crie o serviço tudo no basico, deve ficar algo com a imagem abaixo: (apaguei algumas informações)
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/7ce31f96-ef6d-417a-a049-8d3685862834)
* Agora siga para a página do seu hub de eventos. Siga: Página Inicial > Grupos de Recursos > {Grupo Criado} > {Hub de Eventos Criado}
* Agora, vou criar e novos hubs de eventos. Nesse caso, eles vão servir como tópicos onde vamos receber as informações em streaming. Para criar os Hubs, usei como na imagem abaixo.
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/a43aefe2-ecb0-46e4-8afc-0045bc4b0d86)
* Ao todo, criei 2 tópicos com as mesmas configurações, apenas mudando os nomes, abaixo está um exemplo de configuração
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/653c454e-3fc2-484c-8526-5c17b0269922)
* Pronto, criamos os tópicos de conversa

# Criação do CosmoDB
* Digite cosmoDB no painel de buscas de serviço, inicie um novo serviço cosmoDB core(SQL)
* As configurações são a padrão. As minhas ficaram assim:
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/6b1c39ba-05cc-472e-babe-211d08f971e9)
* Adicione uma coleção ao recurso. A coleção é o banco de dados que vamos usar
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/6dde6c51-9c2e-42b8-a42c-a0a3b6371fa9)
* Como as configurações são pessoais, não vou mostrar, mas depois que criar o banco, deve ficar como na imagem abaixo.
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/2c2d08aa-7de6-4606-a0dd-9ccb05d2fa4d)

# Criando Stream Analytics Job
* O Stream Analytics Job é um recurso da Azure que permite que possamos fazer processamentos nos dados em real time
* Podemos concatenar, multiplicar e fazer diversas outras operações
* Para criar um Stream Analytics Job basta ir no menu de recursos da Azure e procurar
* Agora vamos inserir um novo processo
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/05243ffb-c97c-41c2-92cb-72caccb3b9ff)
* A configuração deve ficar mais ou menos assim
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/a4ac7d9c-b6d0-4b26-a439-bce40fde8e2d)
* Depois que criamos o recurso, vamos até ele para configurar
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/91530e51-d188-497c-9aa3-95b02820982b)
* Agora, crie uma nova entrada e configure para os 2 hubs de eventos criados anteriormente
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/4194aa44-e5f1-4ab7-a4b9-684bc790a1cd)
* Nas configurações, use:
* ```
  modo de autenticação: Cadeia de conexão
  Nome de política do Hub de Eventos: UsarExistente
  Nome de política do Hub de Eventos: RootManagerSharedAccessKey
  Formato de serialização de evento: virgula(,)
  ```
* Agora vamos montar a saida, essa saida deve ser feita para o banco de dados cosmoDB que foi criado anteriormente. É ele que vai receber as informações em streaming
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/5fdeade0-a799-4e10-b564-0e512e52849a)
* ID_transac é um campo no streaming que vai funcionar como chave primária das informações no cosmo
* O próximo passo é executar a função de processamento nos dados de streaming. Para isso, vamos acessar a aba consulta do Stream Analytics.
* Podemos usar o SQL padrão para definir de qual Event Hub estamos pegando os dados, então processa-los e por fim inserilos no cosmosDB
* ![image](https://github.com/Antonio-Borges-Rufino/Azure-Stream-Analytics-for-Real-Time-Cab-Service-Monitoring/assets/86124443/f03c2087-b1d7-4a5a-bd49-cc16e7e557a3)
* Temos que conhecer nosso conjunto de dados, então vou indicar o que é tabela e coluna, mas isso varia de conjunto de dados para conjunto de dados.
* Nesse caso, as tabelas são os hubs de eventos, e temos 2, taxi-tarifa, com informações sobre o pagamento, e taxi-viagens, com infomrações sobre a viagem, como distancia, quantidade de pessoas, id_viagem e etc.
* Diferente do projeto original, vou gerar esses dados usando python e numeros randomicos.
* Voltando a consulta do Stream Analytics, vamos recuperando os dados do hub taxi-tarifa
* ```
  SELECT 
    tf.preco,
    tf.avaliacao,
    tv.distancia_km,
    tv.temp_percurso_m,
    tv.qtd_paradas
  INTO
    [container-streaming]
  FROM [taxi-tarifa] tf JOIN [taxi-viagens] tv 
    ON tf.id_viajem = tv.id_viajem 
    and DATEDIFF(minute, tf, tv) BETWEEN 0 AND 15
  ```
* Primeiro, definimos o que vamos recuperar usando a clausula SELECT, essas colunas devem ser enviadas através dos hubs de eventos junto aos dados. INTO indica qual é o destino final dos dados processados enquanto que DATEDIFF é usado para alinhas em espaços de tempo em que as requisções chegam, já que estamos falando de um processamento em streaming.




 


