# PROVA PRÁTICA DE BANCO DE DADOS I - SQL SERVER (Versão 1)

**Disciplina:** Sistemas de Banco de Dados 2025 - Turma A
**Professor:** Weder M. Sousa
**Data:** 16-05-2025

## Instruções:

* Esta prova consiste na criação de um banco de dados, povoamento de suas tabelas e na execução de consultas SQL para responder a 10 perguntas específicas.
* Você deverá utilizar o SQL Server Management Studio (SSMS) ou outra ferramenta compatível para executar os scripts DDL e DML fornecidos e escrever as consultas solicitadas.
* Para cada pergunta, escreva o comando SQL completo necessário para obter o resultado esperado. Apresente apenas o comando SQL final.
* Comente seus comandos SQL apenas se achar estritamente necessário para esclarecer uma lógica complexa, mas a clareza do próprio código é primordial.
* Preste atenção aos detalhes, nomes de colunas e aliases para facilitar a leitura e avaliação.
* O que será avaliado conhecimento nos conteúdos Where, Group by , Having , Joins e Views


### Parte 1: Criação e Povoamento do Banco de Dados (DDL e DML)

### Execute os scripts SQL abaixo para criar o banco de dados e suas tabelas, e para inserir os dados necessários para a prova.

# (Script DDL)

```sql
-- Verifica se o banco de dados já existe, se sim, remove-o
IF DB_ID('ProjetosProvaDB') IS NOT NULL -- Renomeando o DB para indicar a evolução
BEGIN    
    DROP DATABASE ProjetosProvaDB;
END
GO

-- Cria o banco de dados evoluído
CREATE DATABASE ProjetosProvaDB;
GO

-- Usa o banco de dados criado
USE ProjetosProvaDB;
GO

-- Cria a tabela Departamentos (SEM ALTERAÇÕES ESTRUTURAIS)
CREATE TABLE Departamentos (
    DepartamentoID INT PRIMARY KEY IDENTITY(1,1),
    NomeDepartamento VARCHAR(100) NOT NULL UNIQUE
);
GO

-- Cria a tabela Funcionarios (SEM ALTERAÇÕES ESTRUTURAIS)
CREATE TABLE Funcionarios (
    FuncionarioID INT PRIMARY KEY IDENTITY(1,1),
    Nome VARCHAR(100) NOT NULL,
    Sobrenome VARCHAR(100) NOT NULL,
    Email VARCHAR(255) UNIQUE,
    Cargo VARCHAR(100),
    Salario DECIMAL(10, 2),
    DepartamentoID INT,
    FOREIGN KEY (DepartamentoID) REFERENCES Departamentos(DepartamentoID)
);
GO

-- Cria a tabela Projetos (SEM ALTERAÇÕES ESTRUTURAIS)
CREATE TABLE Projetos (
    ProjetoID INT PRIMARY KEY IDENTITY(1,1),
    NomeProjeto VARCHAR(255) NOT NULL UNIQUE,
    DataInicio DATE,
    DataFimPrevista DATE,
    StatusProjeto VARCHAR(50) DEFAULT 'Ativo' -- Ex: 'Ativo', 'Concluído', 'Cancelado', 'Em Pausa'
);
GO

-- Cria a tabela AtribuicoesProjeto (quem trabalha na equipe do projeto - SEM ALTERAÇÕES ESTRUTURAIS)
CREATE TABLE AtribuicoesProjeto (
    AtribuicaoID INT PRIMARY KEY IDENTITY(1,1),
    FuncionarioID INT NOT NULL,
    ProjetoID INT NOT NULL,
    DataAtribuicao DATE NOT NULL,
    FuncaoNoProjeto VARCHAR(100), -- Ex: 'Desenvolvedor', 'Gerente', 'Analista', 'Testador'
    HorasDedicadas DECIMAL(10, 2), -- Horas estimadas ou registradas no projeto
    FOREIGN KEY (FuncionarioID) REFERENCES Funcionarios(FuncionarioID),
    FOREIGN KEY (ProjetoID) REFERENCES Projetos(ProjetoID)
);
GO

-- Cria a tabela Tarefas (NOVA TABELA)
CREATE TABLE Tarefas (
    TarefaID INT PRIMARY KEY IDENTITY(1,1),
    ProjetoID INT NOT NULL,
    NomeTarefa VARCHAR(255) NOT NULL,
    Descricao TEXT,
    StatusTarefa VARCHAR(50) DEFAULT 'Pendente', -- Ex: 'Pendente', 'Em Andamento', 'Concluída', 'Bloqueada'
    Prioridade VARCHAR(50) DEFAULT 'Média', -- Ex: 'Baixa', 'Média', 'Alta', 'Crítica'
    DataCriacao DATE DEFAULT GETDATE(),
    DataVencimento DATE,
    FuncionarioResponsavelID INT, -- Funcionário primariamente responsável pela tarefa
    FOREIGN KEY (ProjetoID) REFERENCES Projetos(ProjetoID),
    FOREIGN KEY (FuncionarioResponsavelID) REFERENCES Funcionarios(FuncionarioID)
);
GO

-- Cria a tabela Marcos (NOVA TABELA)
CREATE TABLE Marcos (
    MarcoID INT PRIMARY KEY IDENTITY(1,1),
    ProjetoID INT NOT NULL,
    NomeMarco VARCHAR(255) NOT NULL,
    DataMarco DATE NOT NULL,
    Descricao TEXT,
    FOREIGN KEY (ProjetoID) REFERENCES Projetos(ProjetoID)
);
GO

-- Cria a tabela DependenciasTarefa (NOVA TABELA - Relacionamento N:N entre Tarefas)
CREATE TABLE DependenciasTarefa (
    DependenciaID INT PRIMARY KEY IDENTITY(1,1),
    TarefaDependenteID INT NOT NULL, -- A tarefa que depende
    TarefaPredecessoraID INT NOT NULL, -- A tarefa da qual depende
    TipoDependencia VARCHAR(50) DEFAULT 'Fim-Início', -- Ex: 'Fim-Início', 'Início-Início'    
    FOREIGN KEY (TarefaDependenteID) REFERENCES Tarefas(TarefaID),
    FOREIGN KEY (TarefaPredecessoraID) REFERENCES Tarefas(TarefaID)
);
GO

```

### (Script DML - Inserts) SQL

```sql
-- Usa o banco de dados criado
USE ProjetosProvaDB;
GO

-- Insere dados na tabela Departamentos
INSERT INTO Departamentos (NomeDepartamento) VALUES
('Tecnologia'),
('Recursos Humanos'),
('Financeiro'),
('Marketing'),
('Design'),
('Operações'),
('Jurídico');
GO

-- Insere dados na tabela Funcionarios (20 funcionários em diferentes departamentos e cargos)
INSERT INTO Funcionarios (Nome, Sobrenome, Email, Cargo, Salario, DepartamentoID) VALUES
('Carlos', 'Mendes', 'carlos.m@empresa.com', 'Gerente de TI', 8000.00, 1), -- Tecnologia
('Mariana', 'Ferreira', 'mariana.f@empresa.com', 'Desenvolvedora Senior', 6500.00, 1), -- Tecnologia
('Pedro', 'Costa', 'pedro.c@empresa.com', 'Analista de RH', 4500.00, 2), -- Recursos Humanos
('Julia', 'Alves', 'julia.a@empresa.com', 'Contador Senior', 5500.00, 3), -- Financeiro
('Rafael', 'Gomes', 'rafael.g@empresa.com', 'Analista de Marketing', 5000.00, 4), -- Marketing
('Fernanda', 'Carvalho', 'fernanda.c@empresa.com', 'Designer UI/UX', 6000.00, 5), -- Design
('Gustavo', 'Rocha', 'gustavo.r@empresa.com', 'Desenvolvedor Junior', 3800.00, 1), -- Tecnologia
('Larissa', 'Lima', 'larissa.l@empresa.com', 'Recrutador', 4200.00, 2), -- Recursos Humanos
('Marcelo', 'Dias', 'marcelo.d@empresa.com', 'Analista Financeiro', 5200.00, 3), -- Financeiro
('Carolina', 'Nunes', 'carolina.n@empresa.com', 'Especialista de SEO', 4800.00, 4), -- Marketing
('Roberto', 'Sousa', 'roberto.s@empresa.com', 'Gerente de Projetos', 7500.00, 1), -- Tecnologia
('Amanda', 'Oliveira', 'amanda.o@empresa.com', 'Analista de Operações', 4700.00, 6), -- Operações
('Bruno', 'Santos', 'bruno.s@empresa.com', 'Advogado Corporativo', 7000.00, 7), -- Jurídico
('Cristina', 'Almeida', 'cristina.a@empresa.com', 'Desenvolvedora Pleno', 5500.00, 1), -- Tecnologia
('Diego', 'Rodrigues', 'diego.r@empresa.com', 'Testador QA', 4000.00, 1), -- Tecnologia
('Elaine', 'Costa', 'elaine.c@empresa.com', 'Assistente Administrativo', 3500.00, 6), -- Operações
('Fabio', 'Lima', 'fabio.l@empresa.com', 'Gerente de RH', 7000.00, 2), -- Recursos Humanos
('Giovana', 'Gomes', 'giovana.g@empresa.com', 'Designer Gráfico', 5800.00, 5), -- Design
('Henrique', 'Martins', 'henrique.m@empresa.com', 'Especialista Financeiro', 6800.00, 3), -- Financeiro
('Ingrid', 'Souza', 'ingrid.s@empresa.com', 'Analista Jurídico', 5300.00, 7); -- Jurídico
GO

-- Insere dados na tabela Projetos (10 projetos com diferentes status e datas)
INSERT INTO Projetos (NomeProjeto, DataInicio, DataFimPrevista, StatusProjeto) VALUES
('Sistema Interno v1.0', '2023-03-01', '2023-09-30', 'Concluído'),
('Novo Website Institucional', '2023-06-15', '2023-12-20', 'Concluído'),
('App Mobile Clientes', '2024-01-10', '2024-07-31', 'Ativo'),
('Campanha Marketing 2024', '2024-02-01', '2024-11-30', 'Ativo'),
('Otimização Processos Financeiros', '2024-03-10', '2024-09-15', 'Ativo'),
('Implantação Novo RH System', '2024-04-01', '2025-03-31', 'Ativo'),
('Redesign UI/UX', '2024-05-20', '2024-08-31', 'Em Pausa'),
('Auditoria Interna 2024', '2024-06-01', '2024-07-15', 'Ativo'),
('Expansão Mercado Internacional', '2024-07-01', '2025-12-31', 'Ativo'),
('Atualização Infraestrutura TI', '2024-08-15', '2025-02-28', 'Pendente');
GO

-- Insere dados na tabela AtribuicoesProjeto (quem trabalha na equipe do projeto - mais atribuições)
INSERT INTO AtribuicoesProjeto (FuncionarioID, ProjetoID, DataAtribuicao, FuncaoNoProjeto, HorasDedicadas) VALUES
(2, 1, '2023-03-01', 'Desenvolvedor', 300.00), -- Mariana no Sistema Interno
(7, 1, '2023-03-15', 'Desenvolvedor', 250.00), -- Gustavo no Sistema Interno
(1, 1, '2023-03-05', 'Gerente', 100.00),     -- Carlos no Sistema Interno
(14, 1, '2023-04-01', 'Desenvolvedor', 280.00), -- Cristina no Sistema Interno
(15, 1, '2023-04-10', 'Testador QA', 150.00), -- Diego no Sistema Interno
(6, 2, '2023-06-15', 'Designer UI/UX', 150.00),-- Fernanda no Website
(5, 2, '2023-06-20', 'Analista de Marketing', 80.00), -- Rafael no Website
(11, 2, '2023-06-10', 'Gerente de Projetos', 50.00), -- Roberto no Website
(18, 2, '2023-07-01', 'Designer Gráfico', 120.00), -- Giovana no Website
(2, 3, '2024-01-10', 'Desenvolvedor', 120.00), -- Mariana no App Mobile
(7, 3, '2024-01-15', 'Desenvolvedor', 100.00), -- Gustavo no App Mobile
(14, 3, '2024-01-20', 'Desenvolvedor', 110.00), -- Cristina no App Mobile
(15, 3, '2024-02-01', 'Testador QA', 80.00), -- Diego no App Mobile
(11, 3, '2024-01-08', 'Gerente de Projetos', 30.00), -- Roberto no App Mobile
(5, 4, '2024-02-01', 'Analista de Marketing', 60.00), -- Rafael na Campanha
(10, 4, '2024-02-05', 'Especialista de SEO', 90.00), -- Carolina na Campanha
(8, 6, '2024-04-01', 'Recrutador', 40.00),   -- Larissa no RH System
(3, 6, '2024-04-10', 'Analista de RH', 50.00),-- Pedro no RH System
(17, 6, '2024-04-15', 'Gerente de RH', 30.00), -- Fabio no RH System
(4, 5, '2024-03-10', 'Contador Senior', 70.00),   -- Julia nos Processos Financeiros
(9, 5, '2024-03-12', 'Analista Financeiro', 80.00),-- Marcelo nos Processos Financeiros
(19, 5, '2024-03-15', 'Especialista Financeiro', 60.00), -- Henrique nos Processos Financeiros
(6, 7, '2024-05-20', 'Designer UI/UX', 20.00),-- Fernanda no Redesign (Em Pausa)
(18, 7, '2024-05-25', 'Designer Gráfico', 15.00), -- Giovana no Redesign (Em Pausa)
(11, 7, '2024-05-18', 'Gerente de Projetos', 10.00), -- Roberto no Redesign (Em Pausa)
(4, 8, '2024-06-01', 'Contador Senior', 40.00), -- Julia na Auditoria
(9, 8, '2024-06-05', 'Analista Financeiro', 35.00), -- Marcelo na Auditoria
(13, 8, '2024-06-10', 'Advogado Corporativo', 20.00), -- Bruno na Auditoria
(19, 8, '2024-06-12', 'Especialista Financeiro', 30.00); -- Henrique na Auditoria
GO

-- Insere dados na tabela Tarefas (várias tarefas para diferentes projetos, com responsáveis e datas)
-- Projeto 3: App Mobile Clientes
INSERT INTO Tarefas (ProjetoID, NomeTarefa, Descricao, StatusTarefa, Prioridade, DataVencimento, FuncionarioResponsavelID) VALUES
(3, 'Desenvolver Login', 'Implementar funcionalidade de login do usuário', 'Em Andamento', 'Alta', '2024-02-15', 2), -- Mariana
(3, 'Criar Tela Inicial', 'Desenvolver a tela principal do aplicativo', 'Pendente', 'Alta', '2024-02-28', 7), -- Gustavo
(3, 'Configurar Banco de Dados', 'Configurar a base de dados para o app', 'Concluída', 'Crítica', '2024-01-25', 14), -- Cristina
(3, 'Testar Funcionalidades', 'Realizar testes nas funcionalidades implementadas', 'Pendente', 'Média', '2024-03-10', 15), -- Diego
(3, 'Integrar APIs', 'Integrar o app com as APIs de backend', 'Em Andamento', 'Alta', '2024-03-20', 2), -- Mariana
(3, 'Desenvolver Tela de Perfil', 'Criar a tela de perfil do usuário', 'Pendente', 'Média', '2024-03-31', 7), -- Gustavo
(3, 'Realizar Testes de Performance', 'Testar o desempenho do aplicativo', 'Pendente', 'Baixa', '2024-04-15', 15); -- Diego

-- Projeto 4: Campanha Marketing 2024
INSERT INTO Tarefas (ProjetoID, NomeTarefa, Descricao, StatusTarefa, Prioridade, DataVencimento, FuncionarioResponsavelID) VALUES
(4, 'Criar Conteúdo para Redes Sociais', 'Elaborar posts e artes para mídias sociais', 'Em Andamento', 'Alta', '2024-03-10', 5), -- Rafael
(4, 'Planejar Anúncios Pagos', 'Definir estratégia e orçamento para anúncios online', 'Concluída', 'Alta', '2024-02-20', 10), -- Carolina
(4, 'Monitorar Métricas', 'Acompanhar o desempenho da campanha', 'Ativa', 'Média', '2024-11-30', 5), -- Rafael
(4, 'Otimizar SEO', 'Realizar otimizações para motores de busca', 'Em Andamento', 'Média', '2024-05-31', 10); -- Carolina

-- Projeto 5: Otimização Processos Financeiros
INSERT INTO Tarefas (ProjetoID, NomeTarefa, Descricao, StatusTarefa, Prioridade, DataVencimento, FuncionarioResponsavelID) VALUES
(5, 'Analisar Processos Atuais', 'Mapear e documentar os processos financeiros existentes', 'Concluída', 'Crítica', '2024-03-31', 4), -- Julia
(5, 'Identificar Gargalos', 'Encontrar pontos de ineficiência nos processos', 'Em Andamento', 'Alta', '2024-04-15', 9), -- Marcelo
(5, 'Propor Soluções', 'Desenvolver propostas para otimização', 'Pendente', 'Alta', '2024-05-01', 19), -- Henrique
(5, 'Implementar Mudanças', 'Executar as mudanças nos processos', 'Pendente', 'Alta', '2024-06-30', 4); -- Julia

INSERT INTO Tarefas (ProjetoID, NomeTarefa, Descricao, StatusTarefa, Prioridade, DataVencimento, FuncionarioResponsavelID) VALUES
(7, 'Pesquisar Tendências', 'Pesquisar as últimas tendências de design', 'Em Pausa', 'Baixa', '2024-06-15', 6), -- Fernanda
(7, 'Criar Wireframes', 'Desenvolver wireframes para as novas telas', 'Pendente', 'Média', '2024-06-30', 18); -- Giovana

-- Projeto 8: Auditoria Interna 2024
INSERT INTO Tarefas (ProjetoID, NomeTarefa, Descricao, StatusTarefa, Prioridade, DataVencimento, FuncionarioResponsavelID) VALUES
(8, 'Coletar Documentos', 'Reunir a documentação necessária para a auditoria', 'Em Andamento', 'Alta', '2024-06-20', 4), -- Julia
(8, 'Analisar Registros Financeiros', 'Examinar os registros contábeis', 'Pendente', 'Alta', '2024-06-30', 9), -- Marcelo
(8, 'Verificar Conformidade Legal', 'Checar a conformidade com as leis', 'Pendente', 'Alta', '2024-07-05', 13); -- Bruno
GO

-- Insere dados na tabela Marcos (marcos para alguns projetos)
INSERT INTO Marcos (ProjetoID, NomeMarco, DataMarco, Descricao) VALUES
(3, 'Lançamento Beta App', '2024-06-30', 'Versão beta do aplicativo disponível para testes internos'),
(3, 'Lançamento Oficial App', '2024-07-31', 'Lançamento público do aplicativo nas lojas'),
(4, 'Início da Campanha', '2024-03-01', 'Início oficial da campanha de marketing'),
(4, 'Relatório de Meio Termo', '2024-07-31', 'Relatório de desempenho da campanha até o momento'),
(5, 'Propostas de Otimização Aprovadas', '2024-05-15', 'Aprovação das propostas de melhoria de processo'),
(8, 'Relatório de Auditoria Final', '2024-07-15', 'Entrega do relatório final da auditoria');
GO

-- Insere dados na tabela DependenciasTarefa (definindo algumas dependências)
-- Dependências no Projeto 3 (App Mobile Clientes)
-- "Criar Tela Inicial" depende de "Desenvolver Login"
INSERT INTO DependenciasTarefa (TarefaDependenteID, TarefaPredecessoraID, TipoDependencia) VALUES
(1, 2, 'Fim-Início'); -- TarefaID 1 depende de TarefaID 2

-- "Integrar APIs" depende de "Configurar Banco de Dados"
INSERT INTO DependenciasTarefa (TarefaDependenteID, TarefaPredecessoraID, TipoDependencia) VALUES
(3, 4, 'Fim-Início'); -- TarefaID 3 depende de TarefaID 4


```


## Parte 2: Consultas SQL (15 Perguntas)

```sql

-- 1 - Liste o nome de todas as tarefas do projeto 'App Mobile Clientes': 
-- Selecione a coluna NomeTarefa da tabela Tarefas para o projeto cujo NomeProjeto é 'App Mobile Clientes'. 
-- Use WHERE e JOIN com a tabela Projetos.


-- 2 - Marcos previstos para o ano de 2024: 
-- Selecione o NomeMarco e a DataMarco para todos os marcos cuja data esteja no ano de 2024. 
-- Use WHERE.


-- 3 - Tarefas com status 'Em Andamento' e prioridade 'Alta': 
-- Selecione o NomeTarefa, StatusTarefa e Prioridade para todas as tarefas que estão com StatusTarefa como 'Em Andamento' e Prioridade como 'Alta'. 
-- Use WHERE.


-- 4 - Contagem de tarefas por status para o projeto 'Otimização Processos Financeiros': 
-- Conte quantas tarefas existem para cada StatusTarefa dentro do projeto 'Otimização Processos Financeiros'. 
-- Apresente o StatusTarefa e a contagem. Use JOIN com Projetos, WHERE para filtrar o projeto e GROUP BY.


-- 5 - Contagem de tarefas atribuídas a cada funcionário responsável: 
-- Conte quantas tarefas (TarefaID) estão associadas a cada funcionário responsável (FuncionarioResponsavelID). Apresente o FuncionarioID e a contagem de tarefas. 
-- Use GROUP BY.

-- 6 - Projetos que possuem tarefas com prioridade 'Crítica': Liste o NomeProjeto para todos os projetos que contêm pelo 
-- menos uma tarefa com Prioridade igual a 'Crítica'. 
-- Use JOINs com Tarefas e WHERE. Garanta que cada nome de projeto apareça apenas uma vez.


--- 7 - Funcionários responsáveis por tarefas em mais de um projeto: 
-- Liste o Nome e Sobrenome dos funcionários que são responsáveis por tarefas (FuncionarioResponsavelID na tabela Tarefas) em 2 ou mais projetos distintos. 
-- Use JOINs com Tarefas e Projetos, 
-- GROUP BY pelo funcionário e HAVING na contagem distinta de projetos.


-- 8 - Tarefas e o nome do funcionário responsável: Selecione o NomeTarefa e o Nome completo do funcionário responsável (Nome + Sobrenome) 
-- para todas as tarefas que possuem um responsável atribuído. 
-- Utilize um JOIN entre as tabelas Tarefas e Funcionarios.


-- 9 - Tarefas e suas tarefas predecessoras: Liste o NomeTarefa da tarefa dependente e o NomeTarefa da tarefa predecessora para todas as dependências registradas. 
-- Utilize JOINs na tabela DependenciasTarefa com a tabela Tarefas (duas vezes, usando aliases). 
-- Ordene os resultados pelo nome da tarefa dependente.


-- 10 - Projetos e a contagem de tarefas por status: Para cada projeto, conte quantas tarefas existem em cada StatusTarefa. 
-- Apresente o NomeProjeto, StatusTarefa e a contagem. Use JOINs e GROUP BY em múltiplos campos.



-- 11 - Projetos com mais de 5 tarefas 'Concluída': Liste o NomeProjeto para os projetos que possuem mais de 5 tarefas com StatusTarefa igual a 'Concluída'. 
-- Use JOINs com Tarefas, WHERE para filtrar o status, 
-- GROUP BY pelo projeto e HAVING para filtrar pela contagem de tarefas concluídas.


-- 12 - Tarefas do projeto 'App Mobile Clientes' ordenadas por data de vencimento (mais próxima primeiro): 
-- Selecione o NomeTarefa, DataVencimento e StatusTarefa para todas as tarefas do projeto 'App Mobile Clientes'. 
-- Ordene os resultados pela DataVencimento em ordem ascendente (ORDER BY ASC).


--- 13 - Departamentos com salário médio de funcionário acima de R$ 5000.00 e que possuem funcionários atribuídos a projetos: 
-- Liste o NomeDepartamento para os departamentos onde o salário médio de seus funcionários é superior a 5000.00 
-- E que possuem pelo menos um funcionário atribuído a algum projeto. 
-- Use JOINs, WHERE para filtrar salários, GROUP BY pelo departamento, HAVING para filtrar pela média salarial e combine 
-- com uma condição que verifique a existência de atribuições em projetos (pode usar EXISTS ou outro JOIN e HAVING COUNT > 2).



--- 14 - Crie uma View chamada TarefasEmAndamentoView que selecione todas as colunas da tabela Tarefas apenas para as tarefas cujo StatusTarefa seja 'Em Andamento'.
--- Em seguida, consulte esta View (SELECT * FROM TarefasEmAndamentoView;) para listar estas tarefas.



--- 15 - Utilize a View TarefasEmAndamentoView criada na pergunta anterior.
--- Consulte esta View e utilize JOINs com as tabelas Projetos e Funcionarios para listar o NomeTarefa, o NomeProjeto ao qual pertence e o 
-- Nome completo do funcionário responsável.

```
