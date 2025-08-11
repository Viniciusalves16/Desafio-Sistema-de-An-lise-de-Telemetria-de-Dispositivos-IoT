🚀 Desafio: Sistema de Análise de Telemetria de Dispositivos IoT (Simulado)

Seu desafio será construir um Sistema de Análise de Telemetria de Dispositivos IoT (simulado). Imagine um cenário onde milhares de dispositivos enviam dados de telemetria (temperatura, umidade, status de bateria) e você precisa processar, armazenar e alertar sobre anomalias em tempo real. Este sistema será um excelente demonstrativo de suas habilidades em arquitetura de microsserviços, resiliência, observabilidade e segurança.

🏗️ Componentes Principais
Você precisará desenvolver, no mínimo, três microsserviços utilizando Java 17 e Spring Boot:

1. Microsserviço de Ingestão de Dados (TelemetryIngestorService)
Este é o ponto de entrada do sistema para os dados de telemetria.
Funcionalidade: Recebe dados de telemetria dos "dispositivos" via API REST (simulando a entrada de dados em massa).
Segurança (NOVO!): Implemente Spring Security para proteger o endpoint de ingestão. Os "dispositivos" precisarão autenticar-se (ex: via Basic Authentication ou API Key no cabeçalho) para enviar dados.
Publicação Kafka: Após receber e validar os dados (e a autenticação), publica-os em um tópico do Kafka (ex: raw-telemetry-events).
Armazenamento Temporário/Cache (Redis): Pode usar Redis para limitar a taxa de ingestão por dispositivo (rate limiting) ou para armazenar temporariamente dados antes de publicar no Kafka, reduzindo a carga inicial e protegendo o Kafka de picos.

APIs:
POST /telemetry: Recebe um JSON com dados de telemetria (ID do dispositivo, timestamp, temperatura, umidade, etc.). Este endpoint será protegido pelo Spring Security.

2. Microsserviço de Processamento de Dados (TelemetryProcessorService)
Este serviço é o cérebro da análise de telemetria.
Consumo Kafka: Consome mensagens do tópico raw-telemetry-events do Kafka.

Lógica de Negócio (Análise de Anomalias): Simule uma lógica para detectar anomalias (ex: temperatura acima de 30°C ou abaixo de 0°C, umidade fora de um determinado range, ou combinações complexas de dados). Essa lógica deve ser executada de forma assíncrona (@Async) para não bloquear o processamento de novos eventos e garantir alta vazão.

Serviço Externo Simulado (Validação de Dispositivo): Este serviço precisará interagir com um "serviço de validação de dispositivo" simulado (que pode ser um endpoint em outro serviço ou um mock) que pode falhar ocasionalmente.

Publicação de Eventos Processados/Anomalias:
Publica dados processados em um novo tópico do Kafka (ex: processed-telemetry-events).
Se detectar uma anomalia, publica-a em outro tópico (ex: anomaly-events).

3. Microsserviço de Alerta e Notificação (AlertNotificationService)
Este serviço é responsável por alertar sobre problemas.
Consumo Kafka: Consome mensagens do tópico anomaly-events do Kafka.
Lógica de Notificação: Simula o envio de um alerta (ex: System.out.println("Alerta: Dispositivo X com anomalia Y!") ou logando o alerta em um formato estruturado).
Redis para Cooldown/Limitação: Use Redis para implementar um "cooldown" de alertas, evitando que o mesmo dispositivo dispare múltiplos alertas em um curto período (ex: "não alertar sobre o mesmo dispositivo mais de uma vez a cada 5 minutos"). Isso evita sobrecarga de notificações.

🛠️ Requisitos Essenciais e Tecnologias
API RESTful: Todas as interações externas dos seus microsserviços devem ser via API REST.
Programação Assíncrona: Utilize @Async do Spring para operações que não precisam ser síncronas, especialmente no processamento de telemetria para não gargalar o Kafka consumer.
Resiliência com Circuit Breaker, Bulkhead, Retry, Timeout (Resilience4j):
No TelemetryProcessorService, aplique Circuit Breaker (Resilience4j ou Spring Cloud Circuit Breaker) para proteger a chamada ao "serviço de validação de dispositivo" simulado.
Implemente Retry para tentar novamente chamadas falhas para esse serviço externo.
Configure Timeout para as chamadas a esse serviço externo.
Pense em como aplicar Bulkhead para isolar as chamadas a serviços externos e proteger o TelemetryProcessorService de sobrecarga.
Apache Kafka: Utilize para comunicação assíncrona e desacoplamento entre os microsserviços. Pense em grupos de consumidores e na durabilidade das mensagens.
Redis: Use Redis para caching, rate limiting no TelemetryIngestorService e para o controle de cooldown de alertas no AlertNotificationService.
Princípios SOLID e Clean Code: Escreva código limpo, legível e testável, aplicando os princípios SOLID no design das suas classes e módulos desde o início do projeto.

Dockerização:
Crie Dockerfiles para cada um dos seus microsserviços Spring Boot.
Configure um docker-compose.yml para orquestrar e levantar todos os serviços necessários (seus microsserviços, Kafka, Zookeeper, Redis, Prometheus, Grafana) para teste local.

Kubernetes:
Crie os manifestos YAML necessários para implantar seus microsserviços, Kafka (pode ser com operadores como Strimzi para um desafio extra, ou uma versão simplificada), Redis, Prometheus e Grafana no Kubernetes.
Pense em Deployments, Services, ConfigMaps e PersistentVolumes (se necessário para Kafka/Redis).
Monitoramento com Prometheus e Grafana:
Configure seus microsserviços Spring Boot para expor métricas via Micrometer e Actuator.
Configure Prometheus para raspar essas métricas.

Crie dashboards no Grafana para visualizar:
Número de eventos de telemetria ingeridos, processados e de anomalias detectadas.
Latência das APIs e das chamadas ao serviço de validação.
Estado do Circuit Breaker.
Métricas de consumo/produção do Kafka.
Métricas de uso do Redis.
Métricas de requisições autenticadas/não autenticadas (NOVO!).

📝 Passos Sugeridos para o Desafio
Configuração Inicial: Configure os projetos Spring Boot.
Desenvolvimento dos Microsserviços: Implemente a lógica, as APIs e a integração com Kafka e Redis.
Priorize a implementação do Spring Security no TelemetryIngestorService nesta fase.
Implementação de Resiliência: Adicione Circuit Breaker, Retry, Timeout e Bulkhead.
Dockerização: Crie Dockerfiles e o docker-compose.yml para testar localmente.
Monitoramento: Configure Micrometer, Prometheus e Grafana, e crie os dashboards.
Kubernetes: Crie os manifestos YAML e implante em um cluster (Minikube/Kind são ótimos para começar).

🧪 Como Integrar o JMeter no seu Desafio
O JMeter será uma ferramenta externa para simular o tráfego e testar a capacidade de carga do seu sistema. Ele atuará como os "dispositivos IoT" que enviam telemetria.

Passos para incluir no seu desafio:
Instalação e Configuração do JMeter: Baixe e instale o Apache JMeter.

Criação do Plano de Teste (API REST):
Configure um Grupo de Threads para simular um número de usuários (dispositivos IoT).
Adicione um Sampler HTTP Request para chamar seu endpoint POST /telemetry do TelemetryIngestorService.
IMPORTANTE (NOVO!): Configure o Sampler HTTP Request para incluir as credenciais de autenticação (ex: Username/Password para Basic Auth, ou um cabeçalho X-API-Key) exigidas pelo Spring Security.
Configure dados no corpo da requisição (payload JSON) para simular a telemetria.
Adicione Listeners (ex: "Summary Report", "View Results Tree", "Aggregate Report") para coletar os resultados.

Criação do Plano de Teste (Kafka - Opcional, mas Avançado):
Utilize plugins de Kafka para JMeter para simular produtores enviando mensagens diretamente para seus tópicos, ou consumidores lendo delas (útil para validar se o Kafka está suportando a carga).
Execução dos Testes de Carga: Rode o JMeter contra seus microsserviços (depois de implantados via Docker Compose ou Kubernetes).
Análise dos Resultados: Use os relatórios do JMeter e combine-os com as métricas do Prometheus/Grafana para ver como seus microsserviços (CPU, memória, latência de serviço, Circuit Breaker, e status de autenticação) se comportam sob essa carga.
Com a inclusão do Spring Security, o desafio de Sistema de Análise de Telemetria de Dispositivos IoT fica ainda mais completo, permitindo que você:
Valide se sua arquitetura de microsserviços, Kafka e Redis consegue escalar e suportar um alto volume de dados, com uma camada de segurança de API.
Observe como os padrões de resiliência (Circuit Breaker, Bulkhead, Retry, Timeout) reagem sob estresse.
Identifique gargalos de performance usando as métricas do Prometheus/Grafana em conjunto com os resultados do JMeter.
Esta será uma excelente adição ao seu portfólio, demonstrando não apenas suas habilidades de desenvolvimento e resiliência, mas também sua capacidade de integrar aspectos de segurança em sistemas distribuídos.
