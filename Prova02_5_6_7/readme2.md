# PROVA PRÁTICA DE BANCO DE DADOS I - SQL SERVER  (Versão 2)

**Disciplina:** Sistemas de Banco de Dados 2025 - Turma A
**Professor:** Weder M. Sousa
**Data:** 16-05-2025


---

## Instruções:

* Esta prova consiste na criação de um banco de dados, povoamento de suas tabelas e na execução de consultas SQL para responder a 10 perguntas específicas.
* Você deverá utilizar o SQL Server Management Studio (SSMS) compatível para executar os scripts DDL e DML fornecidos e escrever as consultas solicitadas.
* Para cada pergunta, escreva o comando SQL completo necessário para obter o resultado esperado. Apresente apenas o comando SQL final.
* Comente seus comandos SQL apenas se achar estritamente necessário para esclarecer uma lógica complexa, mas a clareza do próprio código é primordial.
* Preste atenção aos detalhes, nomes de colunas e aliases para facilitar a leitura e avaliação.
* O que será avaliado conhecimento nos conteúdos Where, Group by , Having , Joins e Views

---

## Parte 1: Criação e Povoamento do Banco de Dados (DDL e DML)

Execute os scripts SQL abaixo para criar o banco de dados e suas tabelas, e para inserir os dados necessários para a prova.

### Script DDL


```sql
-- Verifica se o banco de dados já existe, se sim, remove-o
IF DB_ID('UniversidadeProvaDB') IS NOT NULL
BEGIN    
    DROP DATABASE UniversidadeProvaDB;
END
GO

-- Cria o banco de dados
CREATE DATABASE UniversidadeProvaDB;
GO

-- Usa o banco de dados criado
USE UniversidadeProvaDB;
GO

-- Cria a tabela Departamentos
CREATE TABLE Departamentos (
    DepartamentoID INT PRIMARY KEY IDENTITY(1,1),
    NomeDepartamento VARCHAR(100) NOT NULL UNIQUE,
    ChefeDepartamento VARCHAR(100)
);
GO

-- Cria a tabela Professores (NOVA TABELA)
CREATE TABLE Professores (
    ProfessorID INT PRIMARY KEY IDENTITY(1,1),
    Nome VARCHAR(100) NOT NULL,
    Sobrenome VARCHAR(100) NOT NULL,
    Email VARCHAR(255) UNIQUE,
    Contrato VARCHAR(50), -- Ex: 'Integral', 'Parcial'
    Salario DECIMAL(10, 2),
    DepartamentoID INT, -- Departamento do professor
    FOREIGN KEY (DepartamentoID) REFERENCES Departamentos(DepartamentoID)
);
GO

-- Cria a tabela Cursos (MODIFICADA para incluir ProfessorID)
CREATE TABLE Cursos (
    CursoID INT PRIMARY KEY IDENTITY(1,1),
    NomeCurso VARCHAR(255) NOT NULL UNIQUE,
    Creditos INT NOT NULL,
    DepartamentoID INT NOT NULL,
    ProfessorID INT, -- Professor responsável pelo curso (NOVA COLUNA)
    FOREIGN KEY (DepartamentoID) REFERENCES Departamentos(DepartamentoID),
    FOREIGN KEY (ProfessorID) REFERENCES Professores(ProfessorID) -- NOVA CHAVE ESTRANGEIRA
);
GO

-- Cria a tabela Alunos (SEM ALTERAÇÕES ESTRUTURAIS)
CREATE TABLE Alunos (
    AlunoID INT PRIMARY KEY IDENTITY(1,1),
    Nome VARCHAR(100) NOT NULL,
    Sobrenome VARCHAR(100) NOT NULL,
    Email VARCHAR(255) UNIQUE,
    DataMatricula DATE NOT NULL,
    MajorDepartamentoID INT, -- Departamento do curso principal do aluno
    FOREIGN KEY (MajorDepartamentoID) REFERENCES Departamentos(DepartamentoID)
);
GO

-- Cria a tabela Matriculas (SEM ALTERAÇÕES ESTRUTURAIS)
CREATE TABLE Matriculas (
    MatriculaID INT PRIMARY KEY IDENTITY(1,1),
    AlunoID INT NOT NULL,
    CursoID INT NOT NULL,
    AnoSemestre VARCHAR(10) NOT NULL, -- Ex: '2023-1', '2023-2', '2024-1'
    NotaFinal DECIMAL(4, 2), -- Pode ser NULL se o curso não terminou
    StatusMatricula VARCHAR(50) DEFAULT 'Ativa', -- Ex: 'Ativa', 'Concluída', 'Trancada'
    UNIQUE (AlunoID, CursoID, AnoSemestre), -- Um aluno não pode se matricular no mesmo curso no mesmo semestre
    FOREIGN KEY (AlunoID) REFERENCES Alunos(AlunoID),
    FOREIGN KEY (CursoID) REFERENCES Cursos(CursoID)
);
GO

```

### (Script DML - Inserts Atualizados)

SQL

```sql
USE UniversidadeProvaDB;
GO

-- Insere dados na tabela Departamentos (mantido)
INSERT INTO Departamentos (NomeDepartamento, ChefeDepartamento) VALUES
('Ciência da Computação', 'Dr. Alan Turing'),
('Engenharia Elétrica', 'Dra. Ada Lovelace'),
('Física', 'Dr. Albert Einstein'),
('Matemática', 'Dra. Sofia Kovalevskaya');
GO

-- Insere dados na tabela Professores (NOVOS INSERTS)
INSERT INTO Professores (Nome, Sobrenome, Email, Contrato, Salario, DepartamentoID) VALUES
('Carlos', 'Silva', 'carlos.silva@univ.com', 'Integral', 9000.00, 1), -- CC
('Mariana', 'Santos', 'mariana.santos@univ.com', 'Integral', 8500.00, 1), -- CC
('Fernando', 'Pereira', 'fernando.p@univ.com', 'Parcial', 5000.00, 1), -- CC
('Ana', 'Costa', 'ana.costa@univ.com', 'Integral', 8800.00, 2), -- EE
('Pedro', 'Oliveira', 'pedro.o@univ.com', 'Parcial', 5200.00, 2), -- EE
('Julia', 'Lima', 'julia.l@univ.com', 'Integral', 9200.00, 3), -- Física
('Rafael', 'Souza', 'rafael.s@univ.com', 'Integral', 8700.00, 4), -- Matemática
('Isabela', 'Martins', 'isabela.m@univ.com', 'Parcial', 4800.00, 4); -- Matemática
GO

-- Insere dados na tabela Cursos (MODIFICADO para incluir ProfessorID)
-- É importante que os ProfessorIDs referenciem IDs válidos da tabela Professores
INSERT INTO Cursos (NomeCurso, Creditos, DepartamentoID, ProfessorID) VALUES
('Introdução à Programação', 4, 1, 1), -- Carlos Silva (ProfessorID 1)
('Estruturas de Dados', 4, 1, 2), -- Mariana Santos (ProfessorID 2)
('Circuitos Elétricos', 5, 2, 4), -- Ana Costa (ProfessorID 4)
('Termodinâmica', 4, 3, 6), -- Julia Lima (ProfessorID 6)
('Cálculo I', 6, 4, 7), -- Rafael Souza (ProfessorID 7)
('Banco de Dados', 4, 1, 2), -- Mariana Santos (ProfessorID 2)
('Algoritmos Avançados', 4, 1, 1), -- Carlos Silva (ProfessorID 1)
('Eletrônica Digital', 5, 2, 5), -- Pedro Oliveira (ProfessorID 5)
('Física Quântica', 5, 3, 6), -- Julia Lima (ProfessorID 6)
('Álgebra Linear', 5, 4, 8); -- Isabela Martins (ProfessorID 8)
GO

-- Insere dados na tabela Alunos (mantido)
INSERT INTO Alunos (Nome, Sobrenome, Email, DataMatricula, MajorDepartamentoID) VALUES
('João', 'Silva', 'joao.s@univ.com', '2022-08-10', 1), -- CC
('Maria', 'Fernandes', 'maria.f@univ.com', '2022-08-10', 2), -- EE
('Pedro', 'Gomes', 'pedro.g@univ.com', '2023-02-01', 1), -- CC
('Ana', 'Souza', 'ana.s@univ.com', '2023-02-01', 4), -- Matemática
('Lucas', 'Lima', 'lucas.l@univ.com', '2024-02-01', 1), -- CC
('Carolina', 'Pereira', 'carolina.p@univ.com', '2024-02-01', 2), -- EE
('Rafael', 'Costa', 'rafael.c@univ.com', '2023-08-15', 3), -- Física
('Isabela', 'Martins', 'isabela.m@univ.com', '2022-08-10', 4); -- Matemática
GO

-- Insere dados na tabela Matriculas (mantido - os IDs de curso e aluno ainda são válidos)
INSERT INTO Matriculas (AlunoID, CursoID, AnoSemestre, NotaFinal, StatusMatricula) VALUES
(1, 1, '2022-2', 85.5, 'Concluída'), -- João em Intro Prog (CursoID 1)
(1, 5, '2022-2', 70.0, 'Concluída'), -- João em Cálculo I (CursoID 5)
(2, 3, '2022-2', 78.0, 'Concluída'), -- Maria em Circuitos (CursoID 3)
(2, 5, '2022-2', 65.5, 'Concluída'), -- Maria em Cálculo I (CursoID 5)
(3, 1, '2023-1', 92.0, 'Concluída'), -- Pedro em Intro Prog (CursoID 1)
(3, 2, '2023-2', 88.0, 'Concluída'), -- Pedro em Estruturas de Dados (CursoID 2)
(3, 6, '2024-1', NULL, 'Ativa'),     -- Pedro em Banco de Dados (CursoID 6)
(4, 5, '2023-1', 95.0, 'Concluída'), -- Ana em Cálculo I (CursoID 5)
(4, 10, '2023-2', 80.0, 'Concluída'), -- Ana em Álgebra Linear (CursoID 10)
(5, 1, '2024-1', NULL, 'Ativa'),     -- Lucas em Intro Prog (CursoID 1)
(5, 6, '2024-1', NULL, 'Ativa'),     -- Lucas em Banco de Dados (CursoID 6)
(6, 3, '2024-1', NULL, 'Ativa'),     -- Carolina em Circuitos (CursoID 3)
(7, 4, '2023-2', 75.0, 'Concluída'), -- Rafael em Termodinâmica (CursoID 4)
(7, 9, '2024-1', NULL, 'Ativa'),     -- Rafael em Física Quântica (CursoID 9)
(8, 5, '2022-2', 60.0, 'Concluída'), -- Isabela em Cálculo I (CursoID 5)
(8, 10, '2023-1', 72.0, 'Concluída'); -- Isabela em Álgebra Linear (CursoID 10)
GO

```


```sql
-- Parte 3: Consultas SQL


-- 1 - Liste o nome e sobrenome de todos os professores: 
-- Selecione as colunas Nome e Sobrenome da tabela Professores.


-- 2 - Cursos do departamento de 'Engenharia Elétrica' com 5 créditos: 
-- Selecione o NomeCurso para todos os cursos que pertencem ao departamento de 'Engenharia Elétrica' e que possuem exatamente 5 créditos. 
-- Use WHERE e JOIN com a tabela Departamentos.

-- 3 -  Alunos matriculados após 01/01/2023: 
-- Selecione o Nome e Sobrenome de todos os alunos cuja data de matrícula (DataMatricula) é posterior a '2023-01-01'. 
-- Use WHERE.


-- 4 -  Contagem total de alunos por departamento Major: 
-- Conte quantos alunos têm seu curso principal (MajorDepartamentoID) em cada departamento. 
-- Apresente o NomeDepartamento e a contagem de alunos. 
-- Use JOIN e GROUP BY.


-- 5 -  Salário médio dos professores por tipo de contrato: 
-- Calcule o salário médio (Salario) dos professores para cada tipo de contrato (Contrato). 
-- Apresente o tipo de contrato e o salário médio. 
-- Use GROUP BY.


-- 6 -  Cursos com mais de 2 alunos matriculados no semestre '2022-2': 
-- Liste o NomeCurso para todos os cursos que tiveram mais de 2 alunos matriculados no semestre '2022-2'. 
-- Use JOINs com Matriculas, 
-- WHERE para filtrar o semestre, GROUP BY pelo curso e HAVING para filtrar os grupos pela contagem de matrículas.


-- 7 -  Contagem de matrículas concluídas por status e semestre: Conte quantas matrículas foram concluídas (StatusMatricula = 'Concluída') em cada AnoSemestre. 
-- Apresente o AnoSemestre e a contagem. Use WHERE e GROUP BY.

-- 8 -  Alunos e o nome do curso em que estão matriculados (apenas matrículas Ativas): 
-- Selecione o Nome completo do aluno (Nome + Sobrenome) e o NomeCurso para todas as matrículas cujo StatusMatricula seja 'Ativa'. 
-- Use JOINs entre Alunos, Matriculas e Cursos, e WHERE.


-- 9 -  Matrículas concluídas com nota final, ordenadas por nota (maior para menor): Selecione o MatriculaID, AlunoID, CursoID, AnoSemestre e NotaFinal para todas as matrículas cujo StatusMatricula seja 'Concluída' e NotaFinal não seja nula. Ordene os resultados pela NotaFinal em ordem decrescente (ORDER BY DESC).


-- 10 -  Departamentos com salário médio de professor integral acima de R$ 8600.00: Liste o NomeDepartamento para os departamentos onde o salário médio dos professores com contrato 'Integral' é superior a 8600.00. Use JOINs, WHERE para filtrar por contrato, GROUP BY pelo departamento e HAVING para filtrar pela média salarial.


-- 11 -  Alunos que concluíram pelo menos um curso com nota superior a 90: 
-- Liste o Nome e Sobrenome dos alunos que possuem pelo menos uma matrícula Concluída com uma NotaFinal maior que 90. 
-- Use JOINs com Matriculas, WHERE para filtrar as matrículas relevantes, GROUP BY pelo aluno e HAVING para garantir que ele tenha tal matrícula.

-- 12 -  Cursos e os nomes dos seus respectivos professores, ordenados por departamento e nome do curso: 
-- Liste o NomeCurso, o Nome e Sobrenome do professor responsável, e o NomeDepartamento ao qual o curso pertence. 
--Use JOINs entre Cursos, Professores e Departamentos. 
-- Ordene os resultados por depois por NomeCurso (ascendente) e NomeDepartamento (descendente).


-- 13 -  Professores que ministram mais de um curso: 
-- Liste o Nome e Sobrenome dos professores que são responsáveis por mais de um curso. 
-- Use JOINs com Cursos, GROUP BY pelo professor e HAVING para filtrar pela contagem de cursos.



-- 14 -  View de Professores com Contrato Integral:

-- Crie uma View chamada ProfessoresIntegralView que selecione todos os professores cujo Contrato seja 'Integral', 
-- incluindo todas as colunas da tabela Professores.
-- Em seguida, consulte esta View (SELECT * FROM ProfessoresIntegralView;) para listar estes professores.


-- 15 - Utilize a View ProfessoresIntegralView criada na pergunta anterior.
-- Consulte esta View e utilize um JOIN com a tabela Cursos para listar o NomeCurso e o Nome e Sobrenome do professor que o ministra, apenas para cursos ministrados 
-- por professores com contrato Integral.

```
