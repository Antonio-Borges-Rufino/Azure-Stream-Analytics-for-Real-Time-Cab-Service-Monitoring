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

 


