# DIO - Modelagem de Dados em Grafos de um Serviço de Streaming

Um serviço de streaming pede um grafo que capture pessoas, conteúdos, uso e contexto. A chave é modelar entidades centrais (usuário, perfil, conteúdo) e relacionamentos ricos (assistiu, gostou, seguiu, similaridade, disponibilidade), preservando histórico e permitindo recomendações e consultas analíticas.

## Nós principais e atributos

### Usuários, perfis e acesso
- User: userId, signupDate, country, ageRange
- Profile: profileId, name, maturityLevel, preferences
- Plan: planId, name, price, maxDevices, quality
- Device: deviceId, type, os, appVersion

### Conteúdo e catálogo
- Title (Conteúdo): titleId, type (movie|series|episode), name, releaseYear, duration, maturityRating
- Series/Season/Episode: usar Title com type diferente ou nós distintos conforme granularidade
- Person (Talento): personId, name, role (actor|director|writer)
- Genre: name
- Tag: name
- Language: code, name
- Region: code, name

### Engajamento e qualidade de serviço
- Event (opcional para trilhar histórico fino): eventId, type (play|pause|seek|finish), timestamp, position, bitrate
- Rating: ratingId, value (1–5), createdAt
- Feedback: feedbackId, type (like|dislike|thumbs), createdAt

## Relacionamentos e semântica

### Estrutura e metadados
- (Title)-[:IN_GENRE]->(Genre): classificação principal
- (Title)-[:HAS_TAG]->(Tag): temas/atributos finos
- (Title)-[:STARS]->(Person): elenco
- (Title)-[:DIRECTED_BY]->(Person): direção
- (Series)-[:HAS_SEASON]->(Season)-[:HAS_EPISODE]->(Episode): hierarquia
- (Title)-[:AVAILABLE_IN]->(Region): disponibilidade por território
- (Title)-[:HAS_LANGUAGE {type: 'audio'|'subtitle'}]->(Language): idiomas

### Conta, perfis e dispositivos
- (User)-[:HAS_PROFILE]->(Profile): perfis por conta
- (User)-[:BELONGS_TO_PLAN]->(Plan): plano vigente
- (Profile)-[:STREAMED_ON]->(Device): vínculo para hábitos de consumo

### Engajamento e recomendação
- (Profile)-[:WATCHED {progress, startedAt, finishedAt}]->(Title): consumo e progresso
- (Profile)-[:RATED]->(Rating)-[:FOR]->(Title): avaliações separadas
- (Profile)-[:FEEDBACK]->(Feedback)-[:FOR]->(Title): likes/dislikes
- (Title)-[:SIMILAR_TO {score}]->(Title): aresta ponderada para recomendação
- (Profile)-[:FOLLOWS]->(Person|Profile): seguir talentos ou perfis (social)