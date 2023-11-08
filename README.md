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

