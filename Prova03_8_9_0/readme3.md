# PROVA PRÁTICA DE BANCO DE DADOS I - SQL SERVER  (Versão 3)

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
IF DB_ID('VendasProvaDB') IS NOT NULL -- Renomeando o DB para indicar a evolução
BEGIN
    ALTER DATABASE VendasProvaDB SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
    DROP DATABASE VendasProvaDB
END
GO

-- Cria o banco de dados evoluído
CREATE DATABASE VendasProvaDB;
GO

-- Usa o banco de dados criado
USE VendasProvaDB;
GO

-- Cria a tabela Clientes (SEM ALTERAÇÕES ESTRUTURAIS)
CREATE TABLE Clientes (
    ClienteID INT PRIMARY KEY IDENTITY(1,1) NOT NULL,
    Nome VARCHAR(100) NOT NULL,
    Sobrenome VARCHAR(100) NOT NULL,
    Email VARCHAR(255) UNIQUE
);
GO

-- Cria a tabela EnderecosCliente (NOVA TABELA)
CREATE TABLE EnderecosCliente (
    EnderecoID INT PRIMARY KEY IDENTITY(1,1) NOT NULL,
    ClienteID INT NOT NULL,
    TipoEndereco VARCHAR(50) NOT NULL, -- Ex: 'Entrega', 'Cobrança', 'Residencial'
    Logradouro VARCHAR(255) NOT NULL,
    Numero VARCHAR(50),
    Complemento VARCHAR(100),
    Bairro VARCHAR(100),
    Cidade VARCHAR(100) NOT NULL,
    Estado VARCHAR(2) NOT NULL,
    CEP VARCHAR(10),
    FOREIGN KEY (ClienteID) REFERENCES Clientes(ClienteID)
);
GO

-- Cria a tabela Fornecedores (NOVA TABELA)
CREATE TABLE Fornecedores (
    FornecedorID INT PRIMARY KEY IDENTITY(1,1) NOT NULL,
    NomeFornecedor VARCHAR(255) NOT NULL UNIQUE,
    Contato VARCHAR(100),
    Telefone VARCHAR(20),
    Email VARCHAR(255) UNIQUE,
    Cidade VARCHAR(100),
    Estado VARCHAR(2)
);
GO

-- Cria a tabela Produtos (MODIFICADA para incluir FornecedorID)
CREATE TABLE Produtos (
    ProdutoID INT PRIMARY KEY IDENTITY(1,1)  NOT NULL,
    NomeProduto VARCHAR(255) NOT NULL,
    Categoria VARCHAR(100),
    Preco DECIMAL(10, 2) NOT NULL,
    Estoque INT NOT NULL,
    FornecedorID INT, -- Adicionando referência ao fornecedor
    FOREIGN KEY (FornecedorID) REFERENCES Fornecedores(FornecedorID) -- Chave estrangeira para Fornecedores
);
GO

-- Cria a tabela Pedidos (MODIFICADA para incluir EnderecoEntregaID e EnderecoCobrancaID)
CREATE TABLE Pedidos (
    PedidoID INT PRIMARY KEY IDENTITY(1,1)  NOT NULL,
    ClienteID INT NOT NULL,
    DataPedido DATE NOT NULL,
    ValorTotal DECIMAL(10, 2) NOT NULL,
    StatusPedido VARCHAR(50) DEFAULT 'Pendente', -- Ex: 'Pendente', 'Processando', 'Enviado', 'Entregue', 'Cancelado'
    EnderecoEntregaID INT, -- Endereço para onde o pedido será enviado
    EnderecoCobrancaID INT, -- Endereço para cobrança do pedido
    FOREIGN KEY (ClienteID) REFERENCES Clientes(ClienteID),
    FOREIGN KEY (EnderecoEntregaID) REFERENCES EnderecosCliente(EnderecoID),
    FOREIGN KEY (EnderecoCobrancaID) REFERENCES EnderecosCliente(EnderecoID)
);
GO

-- Cria a tabela ItensPedido (SEM ALTERAÇÕES ESTRUTURAIS)
CREATE TABLE ItensPedido (
    ItemID INT PRIMARY KEY IDENTITY(1,1)  NOT NULL,
    PedidoID INT NOT NULL,
    ProdutoID INT NOT NULL,
    Quantidade INT NOT NULL,
    PrecoUnitario DECIMAL(10, 2) NOT NULL,
    SubtotalItem DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (PedidoID) REFERENCES Pedidos(PedidoID),
    FOREIGN KEY (ProdutoID) REFERENCES Produtos(ProdutoID)
);
GO

-- Cria a tabela Pagamentos (NOVA TABELA)
CREATE TABLE Pagamentos (
    PagamentoID INT PRIMARY KEY IDENTITY(1,1) NOT NULL,
    PedidoID INT NOT NULL,
    DataPagamento DATE NOT NULL,
    MetodoPagamento VARCHAR(50) NOT NULL, -- Ex: 'Cartão de Crédito', 'Boleto', 'PIX'
    ValorPago DECIMAL(10, 2) NOT NULL,
    StatusPagamento VARCHAR(50) DEFAULT 'Aprovado', -- Ex: 'Aprovado', 'Pendente', 'Recusado', 'Estornado'
    FOREIGN KEY (PedidoID) REFERENCES Pedidos(PedidoID)
);
GO
```


## Script DML - Inserts

```sql
-- Usa o banco de dados criado
USE VendasProvaDB;
GO


-- Insere dados na tabela Clientes (15 clientes)
INSERT INTO Clientes (Nome, Sobrenome, Email) VALUES
('Ana', 'Silva', 'ana.silva.ev@email.com'),
('Bruno', 'Martins', 'bruno.m.ev@email.com'),
('Carla', 'Souza', 'carla.s.ev@email.com'),
('Daniel', 'Oliveira', 'daniel.o.ev@email.com'),
('Eduarda', 'Santos', 'eduarda.s.ev@email.com'),
('Fernando', 'Lima', 'fernando.l.ev@email.com'),
('Gabriela', 'Costa', 'gabriela.c.ev@email.com'),
('Hugo', 'Pereira', 'hugo.p.ev@email.com'),
('Isabela', 'Almeida', 'isabela.a.ev@email.com'),
('João', 'Rodrigues', 'joao.r.ev@email.com'),
('Luiza', 'Fernandes', 'luiza.f.ev@email.com'),
('Miguel', 'Gomes', 'miguel.g.ev@email.com'),
('Natália', 'Carvalho', 'natalia.c.ev@email.com'),
('Otávio', 'Rocha', 'otavio.r.ev@email.com'),
('Paula', 'Lima', 'paula.l.ev@email.com');
GO

-- Insere dados na tabela Fornecedores (5 fornecedores)
INSERT INTO Fornecedores (NomeFornecedor, Contato, Telefone, Email, Cidade, Estado) VALUES
('Tech Solutions Ltda.', 'João Fornecedor', '(11) 9876-5432', 'contato@techsol.com', 'São Paulo', 'SP'),
('Eletronica Brasil S/A', 'Maria Contato', '(21) 1234-5678', 'vendas@ebrasil.com', 'Rio de Janeiro', 'RJ'),
('Moveis Modernos', 'Pedro Representante', '(31) 5555-1111', 'comercial@moveis.com', 'Belo Horizonte', 'MG'),
('Audio Prime', 'Ana Atendimento', '(41) 8888-2222', 'suporte@audioprime.com', 'Curitiba', 'PR'),
('Acessórios Top', 'Rafael Vendas', '(51) 7777-3333', 'contato@acessorios.com', 'Porto Alegre', 'RS');
GO

-- Insere dados na tabela Produtos (15 produtos, relacionados a fornecedores)
INSERT INTO Produtos (NomeProduto, Categoria, Preco, Estoque, FornecedorID) VALUES
('Laptop Gamer X', 'Eletrônicos', 4500.00, 15, 1), -- Tech Solutions
('Smartphone Z', 'Eletrônicos', 2200.00, 50, 1), -- Tech Solutions
('Teclado Mecânico', 'Acessórios', 350.00, 30, 5), -- Acessórios Top
('Mouse Óptico', 'Acessórios', 80.00, 100, 5), -- Acessórios Top
('Monitor UltraWide', 'Eletrônicos', 1800.00, 10, 2), -- Eletronica Brasil
('Cadeira Gamer', 'Móveis', 1200.00, 5, 3), -- Moveis Modernos
('Webcam HD', 'Acessórios', 150.00, 40, 5), -- Acessórios Top
('Fones de Ouvido BT', 'Áudio', 250.00, 60, 4), -- Audio Prime
('Microfone USB', 'Áudio', 400.00, 20, 4), -- Audio Prime
('Mesa Ergonômica', 'Móveis', 750.00, 8, 3), -- Moveis Modernos
('Smart TV 50"', 'Eletrônicos', 3000.00, 25, 2), -- Eletronica Brasil
('Soundbar', 'Áudio', 600.00, 35, 4), -- Audio Prime
('Mochila para Laptop', 'Acessórios', 120.00, 80, 5), -- Acessórios Top
('Gabinete Gamer', 'Eletrônicos', 550.00, 18, 1), -- Tech Solutions
('Impressora Multifuncional', 'Eletrônicos', 800.00, 22, 2); -- Eletronica Brasil
GO

-- Insere dados na tabela EnderecosCliente (múltiplos endereços para alguns clientes)
INSERT INTO EnderecosCliente (ClienteID, TipoEndereco, Logradouro, Numero, Complemento, Bairro, Cidade, Estado, CEP) VALUES
(1, 'Residencial', 'Rua das Flores', '100', 'Apto 101', 'Centro', 'São Paulo', 'SP', '01000-000'), -- Ana
(1, 'Entrega', 'Av. Principal', '500', NULL, 'Bairro Novo', 'São Paulo', 'SP', '01001-000'),    -- Ana (Entrega diferente)
(2, 'Residencial', 'Rua do Sol', '250', NULL, 'Copacabana', 'Rio de Janeiro', 'RJ', '20000-000'), -- Bruno
(3, 'Residencial', 'Av. Liberdade', '30', 'Casa', 'Savassi', 'Belo Horizonte', 'MG', '30000-000'), -- Carla
(3, 'Cobrança', 'Rua da Moeda', '10', 'Sala 5', 'Centro', 'Belo Horizonte', 'MG', '30001-000'),  -- Carla (Cobrança diferente)
(4, 'Residencial', 'Rua das Pedras', '123', NULL, 'Jardins', 'São Paulo', 'SP', '01002-000'),    -- Daniel
(5, 'Residencial', 'Av. Atlântica', '1000', 'Apto 502', 'Centro', 'Porto Alegre', 'RS', '90000-000'), -- Eduarda
(6, 'Residencial', 'Rua da Praia', '50', NULL, 'Ipanema', 'Rio de Janeiro', 'RJ', '20001-000'),  -- Fernando
(7, 'Residencial', 'Rua Sete de Setembro', '77', NULL, 'Centro', 'São Paulo', 'SP', '01003-000'), -- Gabriela
(8, 'Residencial', 'Rua Verde', '88', 'Fundos', 'Água Verde', 'Curitiba', 'PR', '80000-000'),  -- Hugo
(9, 'Residencial', 'Rua Amarela', '99', NULL, 'Centro', 'Belo Horizonte', 'MG', '30002-000'), -- Isabela
(10, 'Residencial', 'Rua Azul', '111', 'Bloco A', 'Morumbi', 'São Paulo', 'SP', '01004-000'), -- João
(11, 'Residencial', 'Rua Rosa', '222', NULL, 'Centro', 'Porto Alegre', 'RS', '90001-000'), -- Luiza
(12, 'Residencial', 'Rua Roxa', '333', NULL, 'Savassi', 'Belo Horizonte', 'MG', '30003-000'), -- Miguel
(13, 'Residencial', 'Rua Laranja', '444', 'Casa 2', 'Jardins', 'São Paulo', 'SP', '01005-000'), -- Natália
(14, 'Residencial', 'Rua Marrom', '555', NULL, 'Copacabana', 'Rio de Janeiro', 'RJ', '20002-000'), -- Otávio
(15, 'Residencial', 'Rua Cinza', '666', NULL, 'Centro', 'Curitiba', 'PR', '80001-000'); -- Paula
GO

-- Insere dados na tabela Pedidos (20 pedidos, relacionados a clientes e endereços)
-- Certifique-se que os EnderecoEntregaID e EnderecoCobrancaID pertencem ao ClienteID
INSERT INTO Pedidos (ClienteID, DataPedido, ValorTotal, StatusPedido, EnderecoEntregaID, EnderecoCobrancaID) VALUES
(1, '2023-01-10', 1020.50, 'Entregue', 2, 1),    -- Ana, Entrega: Av. Principal, Cobrança: Rua das Flores
(2, '2023-01-15', 2040.20, 'Entregue', 3, 3),    -- Bruno, Entrega/Cobrança: Rua do Sol
(3, '2023-02-20', 900.00, 'Entregue', 4, 5),     -- Carla, Entrega: Av. Liberdade, Cobrança: Rua da Moeda
(1, '2023-03-01', 750.00, 'Entregue', 2, 1),     -- Ana, Entrega: Av. Principal, Cobrança: Rua das Flores
(4, '2023-03-10', 8700.00, 'Entregue', 6, 6),    -- Daniel, Entrega/Cobrança: Rua das Pedras
(5, '2023-04-05', 2340.00, 'Entregue', 7, 7),    -- Eduarda, Entrega/Cobrança: Av. Atlântica
(6, '2023-04-10', 7500.00, 'Entregue', 8, 8),    -- Fernando, Entrega/Cobrança: Rua da Praia
(7, '2023-05-01', 3749.00, 'Entregue', 9, 9),    -- Gabriela, Entrega/Cobrança: Rua Sete de Setembro
(8, '2023-05-15', 4890.00, 'Entregue', 10, 10),    -- Hugo, Entrega/Cobrança: Rua Verde
(9, '2023-06-20', 1203.00, 'Entregue', 11, 11),    -- Isabela, Entrega/Cobrança: Rua Amarela
(10, '2024-01-05', 7890.00, 'Processando', 12, 12), -- João, Entrega/Cobrança: Rua Azul
(1, '2024-01-10', 7120.00, 'Processando', 2, 1), -- Ana, Entrega: Av. Principal, Cobrança: Rua das Flores
(2, '2024-02-01', 1810.00, 'Pendente', 3, 3),    -- Bruno, Entrega/Cobrança: Rua do Sol
(3, '2024-02-10', 4569.00, 'Pendente', 4, 5),    -- Carla, Entrega: Av. Liberdade, Cobrança: Rua da Moeda
(4, '2024-03-01', 1452.00, 'Enviado', 6, 6),     -- Daniel, Entrega/Cobrança: Rua das Pedras
(11, '2024-03-05', 12480.00, 'Entregue', 13, 13), -- Luiza
(12, '2024-03-10', 23789.00, 'Processando', 14, 14), -- Miguel
(13, '2024-03-15', 11234.00, 'Pendente', 15, 15), -- Natália
(14, '2024-03-20', 9410.00, 'Enviado', 16, 16), -- Otávio
(15, '2024-03-25', 3297.00, 'Entregue', 17, 17); -- Paula
GO

-- Insere dados na tabela ItensPedido (relacionando com os pedidos e produtos)
-- Calcula o SubtotalItem
INSERT INTO ItensPedido (PedidoID, ProdutoID, Quantidade, PrecoUnitario, SubtotalItem) VALUES
(1, 1, 1, 4500.00, 4500.00),  -- Pedido 1: Laptop Gamer X
(2, 2, 1, 2200.00, 2200.00),  -- Pedido 2: Smartphone Z
(3, 3, 2, 350.00, 700.00),   -- Pedido 3: Teclado Mecânico x2
(4, 4, 3, 80.00, 240.00),    -- Pedido 4: Mouse Óptico x3
(5, 5, 1, 1800.00, 1800.00), -- Pedido 5: Monitor UltraWide
(6, 6, 1, 1200.00, 1200.00), -- Pedido 6: Cadeira Gamer
(7, 7, 5, 150.00, 750.00),  -- Pedido 7: Webcam HD x5
(8, 8, 2, 250.00, 500.00),  -- Pedido 8: Fones de Ouvido BT x2
(9, 9, 1, 400.00, 400.00),  -- Pedido 9: Microfone USB
(10, 10, 1, 750.00, 750.00), -- Pedido 10: Mesa Ergonômica
(11, 1, 1, 4500.00, 4500.00), -- Pedido 11: Laptop Gamer X
(12, 2, 1, 2200.00, 2200.00), -- Pedido 12: Smartphone Z
(12, 4, 1, 80.00, 80.00),    -- Pedido 12: Mouse Óptico
(13, 6, 1, 1200.00, 1200.00), -- Pedido 13: Cadeira Gamer
(14, 3, 1, 350.00, 350.00),  -- Pedido 14: Teclado Mecânico
(11, 11, 1, 3000.00, 3000.00), -- Pedido 11: Smart TV 50"
(12, 12, 1, 600.00, 600.00), -- Pedido 12: Soundbar
(13, 13, 2, 120.00, 240.00), -- Pedido 13: Mochila para Laptop x2
(14, 14, 1, 550.00, 550.00), -- Pedido 14: Gabinete Gamer
(15, 15, 1, 800.00, 800.00); -- Pedido 15: Impressora Multifuncional
GO

-- Insere dados na tabela Pagamentos (registrando pagamentos para alguns pedidos)
INSERT INTO Pagamentos (PedidoID, DataPagamento, MetodoPagamento, ValorPago, StatusPagamento) VALUES
(1, '2023-01-10', 'Cartão de Crédito', 4500.00, 'Aprovado'),
(2, '2023-01-15', 'Cartão de Crédito', 2200.00, 'Estornado'),
(3, '2023-02-20', 'Boleto', 700.00, 'Aprovado'),
(4, '2023-03-01', 'PIX', 240.00, 'Aprovado'),
(5, '2023-03-10', 'Cartão de Crédito', 1800.00, 'Aprovado'),
(6, '2023-04-05', 'Cartão de Crédito', 1200.00, 'Aprovado'),
(7, '2023-04-10', 'PIX', 750.00, 'Aprovado'),
(8, '2023-05-15', 'Boleto', 500.00, 'Aprovado'),
(10, '2023-06-20', 'Cartão de Crédito', 750.00, 'Recusado'),
(11, '2024-01-05', 'Cartão de Crédito', 7500.00, 'Aprovado'), -- Pedido 11: Laptop + Smart TV
(12, '2024-01-10', 'PIX', 2280.00, 'Aprovado'), -- Pedido 12: Smartphone + Mouse + Soundbar
(14, '2024-03-01', 'Cartão de Crédito', 900.00, 'Aprovado'), -- Pedido 14: Teclado + Gabinete
(15, '2024-03-25', 'Boleto', 800.00, 'Aprovado'), -- Pedido 15: Impressora
(16, '2024-03-05', 'Cartão de Crédito', 3000.00, 'Aprovado'), -- Pedido 16: Luiza, Smart TV
(17, '2024-03-10', 'PIX', 1200.00, 'Aprovado'), -- Pedido 17: Miguel, Cadeira Gamer
(19, '2024-03-20', 'Cartão de Crédito', 550.00, 'Aprovado'); -- Pedido 19: Otávio, Gabinete Gamer
GO

```


```sql
-- Parte 2: Consultas SQL (15 Perguntas)

-- 1 -  Liste o nome de todos os fornecedores: 
-- Selecione a coluna NomeFornecedor da tabela Fornecedores.

-- 2 - Produtos na categoria 'Áudio' com estoque superior a 30: 
-- Selecione o NomeProduto e Estoque para todos os produtos que pertencem à categoria 'Áudio' e cujo estoque atual é maior que 30. 
-- Use WHERE.


-- 3 -  Pedidos realizados no primeiro trimestre de 2024: 
-- Selecione o PedidoID, ClienteID e DataPedido de todos os pedidos cuja data esteja entre '2024-01-01' e '2024-03-31' (inclusive). 
-- Use WHERE.


-- 4 -  Contagem de endereços por cliente: 
-- Conte quantos endereços (EnderecoID) estão cadastrados para cada cliente. 
-- Apresente o ClienteID e a contagem de endereços. 
-- Use GROUP BY.


-- 5 -  Valor total pago por método de pagamento: 
-- Calcule a soma do ValorPago para cada método de pagamento (MetodoPagamento). 
-- Apresente o método de pagamento e o valor total pago. Use GROUP BY.


-- 6 -  Pagamentos com status 'Recusado' ou 'Estornado': 
-- Liste o PagamentoID, PedidoID, DataPagamento e StatusPagamento para todos os pagamentos cujo status seja 'Recusado' ou 'Estornado'. 
-- Use WHERE com OR ou IN.


-- 7 -  Fornecedores que fornecem mais de 2 produtos: Liste o NomeFornecedor para todos os fornecedores que estão associados a mais de 2 produtos diferentes. 
-- Use JOIN com Produtos, GROUP BY pelo fornecedor e HAVING para filtrar pela contagem de produtos.

-- 8 -  Categorias de produtos com valor total de estoque superior a R$ 10000.00: 
-- Liste as Categorias de produtos onde a soma total do valor do estoque (Estoque * Preço) para todos os produtos nessa categoria é maior que 10000.00. 
-- Use GROUP BY pela categoria e HAVING na soma (Estoque * Preço).


-- 9 -  Clientes que possuem mais de um tipo de endereço cadastrado: 
-- Liste o Nome e Sobrenome dos clientes que possuem pelo menos dois registros na tabela EnderecosCliente com tipos de endereço (TipoEndereco) distintos. 
-- Use GROUP BY por cliente e HAVING na contagem distinta de tipos de endereço.


-- 10 -  Pedidos entregues em São Paulo (SP): Liste o PedidoID, DataPedido e o Nome completo do cliente para todos os pedidos cujo endereço de entrega (EnderecoEntregaID) esteja em 'São Paulo' no estado de 'SP'. 
-- Use JOINs entre Pedidos, Clientes e EnderecosCliente, e 
-- WHERE para filtrar pela cidade e estado do endereço de entrega.

-- 11 -  Itens de pedido com nome do produto e nome do fornecedor: Selecione o PedidoID, o NomeProduto, o NomeFornecedor e a Quantidade para todos os itens de pedido. -- Utilize JOINs entre ItensPedido, Produtos e Fornecedores. 
-- Ordene os resultados por PedidoID e, secundariamente, por NomeProduto (ascendente).


-- 12 -  Pedidos e o nome completo do cliente que o realizou: 
-- Selecione o PedidoID, DataPedido e o Nome completo do cliente (Nome + Sobrenome) para todos os pedidos. 
-- Utilize um JOIN entre as tabelas Pedidos e Clientes.

-- 13 -  Pedidos com valor total diferente da soma dos pagamentos aprovados: Liste o PedidoID e o ValorTotal para os pedidos onde a soma dos ValorPago dos pagamentos com StatusPagamento 'Aprovado' associados a este pedido é diferente do ValorTotal do pedido. 
-- Use JOINs com Pagamentos, WHERE para filtrar pagamentos aprovados, 
-- GROUP BY pelo pedido e HAVING para comparar a soma dos pagamentos com o ValorTotal do pedido.


-- 14 -  Crie uma View chamada PedidosProcessandoView que selecione todas as colunas da tabela Pedidos apenas para os pedidos cujo StatusPedido seja 'Processando'.
-- Em seguida, consulte esta View (SELECT * FROM PedidosProcessandoView;) para listar estes pedidos.


-- Clientes que têm pedidos 'Processando' (usando View):
-- 15 -  Utilize a View PedidosProcessandoView criada na pergunta anterior.
-- Consulte esta View e utilize um JOIN com a tabela Clientes para listar o Nome e Sobrenome dos clientes que aparecem nesta View.
```