
# Projeto Backend com Express e SQLite

## Descrição
Este projeto configura um backend usando Express e SQLite para gerenciar usuários. 

## Estrutura de Diretórios
A estrutura de diretórios do projeto é a seguinte:

```
backend/
│── data/
│   └── database.sqlite
├── src/
│   ├── config/
│   │   └── db.js
│   ├── usuario/
│   │   ├── controllers/
│   │   │   └── usuarioController.js
│   │   ├── models/
│   │   │   └── usuarioModel.js
│   │   ├── routes/
│   │   │   └── usuarioRoutes.js
│   ├── app.js
│   └── server.js
├── .gitignore
├── package-lock.json
├── package.json
├── requests.http
└── README.md
```

## Instalação

### 1. Inicialize o Projeto Node.js
No diretório `backend`, inicialize um novo projeto Node.js:
```bash
npm init -y
```

### 2. Instale as Dependências
Instale o Express e a biblioteca do SQLite:
```bash
npm install express sqlite3
```

Instale o `nodemon` como dependência de desenvolvimento:
```bash
npm install --save-dev nodemon
```

### 3. Estrutura de Diretórios
Crie a estrutura de diretórios conforme mostrado acima.

### 4. Configuração do Banco de Dados sqlite3
Crie o arquivo `src/config/db.js` com o seguinte conteúdo:

```javascript
const sqlite3 = require('sqlite3').verbose();
const path = require('path');

// Caminho para o banco de dados
const dbPath = path.resolve(__dirname, '../../data/database.sqlite');

// Cria e abre o banco de dados
const db = new sqlite3.Database(dbPath, (err) => {
    if (err) {
        console.error('Erro ao abrir o banco de dados:', err.message);
    } else {
        console.log('Conectado ao banco de dados SQLite.');
    }
});

// Cria a tabela se não existir
db.serialize(() => {
    db.run(`
        CREATE TABLE IF NOT EXISTS USUARIO (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            nome TEXT NOT NULL,
            email TEXT NOT NULL,
            status TEXT NOT NULL
        )
    `);
});

module.exports = db;
```
## Adaptação para SQL Server e MySQL

Para adaptar o código para usar um banco de dados SQL Server ou MySQL em vez do SQLite, você precisará fazer algumas alterações no código. Vou fornecer exemplos para ambos os bancos de dados.

### SQL Server

#### 1. Instale a biblioteca mssql
```bash
npm install mssql
```

#### 2. Configuração do banco de dados (`config/db.js`)
```javascript
const sql = require('mssql');

// Configuração da conexão com o banco de dados SQL Server
const dbConfig = {
    user: 'seu_usuario',       // Substitua pelo seu usuário do banco de dados
    password: 'sua_senha',     // Substitua pela sua senha do banco de dados
    server: 'seu_servidor',    // Substitua pelo endereço do servidor
    database: 'sua_base_de_dados', // Substitua pelo nome do banco de dados
    options: {
        encrypt: true, // Use iss ose for com windows no azure
        enableArithAbort: true
    }
};

// Conecta ao banco de dados
const poolPromise = new sql.ConnectionPool(dbConfig)
    .connect()
    .then(pool => {
        console.log('Conectado ao banco de dados SQL Server.');
        return pool;
    })
    .catch(err => {
        console.error('Erro ao conectar ao banco de dados SQL Server:', err.message);
    });

module.exports = {
    sql, poolPromise
};
```

#### 3. Criação da tabela (se necessário)

Você pode criar a tabela manualmente ou programaticamente. Aqui está um exemplo de como criar a tabela programaticamente:

```javascript
const { sql, poolPromise } = require('./db');

async function createTable() {
    try {
        const pool = await poolPromise;
        await pool.request().query(\`
            IF NOT EXISTS (SELECT * FROM sysobjects WHERE name='USUARIO' and xtype='U')
            CREATE TABLE USUARIO (
                id INT IDENTITY(1,1) PRIMARY KEY,
                nome NVARCHAR(100) NOT NULL,
                email NVARCHAR(100) NOT NULL,
                status NVARCHAR(50) NOT NULL
            )
        \`);
        console.log('Tabela USUARIO criada ou já existe.');
    } catch (err) {
        console.error('Erro ao criar a tabela USUARIO:', err.message);
    }
}

createTable();
```

### MySQL

#### 1. Instale a biblioteca mysql2
```bash
npm install mysql2
```

#### 2. Configuração do banco de dados (`config/db.js`)
```javascript
const mysql = require('mysql2/promise');

// Configuração da conexão com o banco de dados MySQL
const dbConfig = {
    host: 'seu_servidor',      // Substitua pelo endereço do servidor
    user: 'seu_usuario',       // Substitua pelo seu usuário do banco de dados
    password: 'sua_senha',     // Substitua pela sua senha do banco de dados
    database: 'sua_base_de_dados' // Substitua pelo nome do banco de dados
};

// Conecta ao banco de dados
const pool = mysql.createPool(dbConfig);

pool.getConnection()
    .then(conn => {
        console.log('Conectado ao banco de dados MySQL.');
        conn.release();
    })
    .catch(err => {
        console.error('Erro ao conectar ao banco de dados MySQL:', err.message);
    });

module.exports = pool;
```

#### 3. Criação da tabela (se necessário)

Você pode criar a tabela manualmente ou programaticamente. Aqui está um exemplo de como criar a tabela programaticamente:

```javascript
const pool = require('./db');

async function createTable() {
    try {
        const conn = await pool.getConnection();
        await conn.query(\`
            CREATE TABLE IF NOT EXISTS USUARIO (
                id INT AUTO_INCREMENT PRIMARY KEY,
                nome VARCHAR(100) NOT NULL,
                email VARCHAR(100) NOT NULL,
                status VARCHAR(50) NOT NULL
            )
        \`);
        console.log('Tabela USUARIO criada ou já existe.');
        conn.release();
    } catch (err) {
        console.error('Erro ao criar a tabela USUARIO:', err.message);
    }
}

createTable();
```

### Comentários

#### Configuração da Conexão:
SQL Server e MySQL requerem configurações de conexão específicas que incluem o host/servidor, usuário, senha e nome do banco de dados.

#### Criação da Tabela:
A criação da tabela pode ser feita manualmente diretamente no banco de dados ou programaticamente, como mostrado nos exemplos.

#### Importação e Uso do Banco de Dados:
Você precisará importar o pool de conexões no seu modelo e usar os métodos apropriados para executar consultas SQL.

### Modelo de Dados para SQL Server e MySQL

Aqui está um exemplo de como modificar o modelo de dados `usuarioModel.js` para usar com SQL Server e MySQL.

#### SQL Server
```javascript
const { sql, poolPromise } = require('../../config/db');

class Usuario {
    static async create({ nome, email, status }, callback) {
        try {
            const pool = await poolPromise;
            const result = await pool.request()
                .input('nome', sql.NVarChar, nome)
                .input('email', sql.NVarChar, email)
                .input('status', sql.NVarChar, status)
                .query('INSERT INTO USUARIO (nome, email, status) VALUES (@nome, @email, @status); SELECT SCOPE_IDENTITY() AS id');
            callback(null, { id: result.recordset[0].id, nome, email, status });
        } catch (err) {
            console.error('Erro ao criar usuario:', err.message);
            callback(err);
        }
    }

    // Implementar os outros métodos de forma semelhante
}

module.exports = Usuario;
```

#### MySQL
```javascript
const pool = require('../../config/db');

class Usuario {
    static async create({ nome, email, status }, callback) {
        try {
            const conn = await pool.getConnection();
            const [result] = await conn.query('INSERT INTO USUARIO (nome, email, status) VALUES (?, ?, ?)', [nome, email, status]);
            callback(null, { id: result.insertId, nome, email, status });
            conn.release();
        } catch (err) {
            console.error('Erro ao criar usuario:', err.message);
            callback(err);
        }
    }

    // Implementar os outros métodos de forma semelhante
}

module.exports = Usuario;
```

Esses exemplos mostram como adaptar seu código para usar SQL Server ou MySQL em vez de SQLite, incluindo as etapas de configuração da conexão e a criação da tabela, bem como a implementação do modelo de dados para interagir com o banco de dados.

### 5. Modelos de Dados SQLITE
Crie o arquivo `src/usuario/models/usuarioModel.js`:

```javascript
const db = require('../../config/db'); // Importa a configuração do banco de dados SQLite

// Define a classe Usuario que contém métodos estáticos para operar no banco de dados
class Usuario {
    // Método estático para criar um novo usuário
    static create({ nome, email, status }, callback) {
        // SQL para inserir um novo usuário na tabela USUARIO
        const sql = 'INSERT INTO USUARIO (nome, email, status) VALUES (?, ?, ?)';
        // Parâmetros a serem inseridos no SQL
        const params = [nome, email, status];
        
        // Executa o SQL com os parâmetros fornecidos
        db.run(sql, params, function(err) {
            if (err) {
                // Se houver um erro, exibe uma mensagem de erro e chama o callback com o erro
                console.error('Erro ao criar usuario:', err.message);
                callback(err);
            } else {
                // Se não houver erro, chama o callback com os dados do usuário criado
                callback(null, { id: this.lastID, nome, email, status });
            }
        });
    }

    // Método estático para buscar todos os usuários
    static buscarTodos(callback) {
        // SQL para selecionar todos os usuários na tabela USUARIO
        const sql = 'SELECT * FROM USUARIO';
        
        // Executa o SQL sem parâmetros ([])
        db.all(sql, [], (err, rows) => {
            if (err) {
                // Se houver um erro, exibe uma mensagem de erro e chama o callback com o erro
                console.error('Erro ao listar usuarios:', err.message);
                callback(err);
            } else {
                // Se não houver erro, chama o callback com as linhas (rows) retornadas
                callback(null, rows);
            }
        });
    }

    // Método estático para buscar um usuário por ID
    static buscarPorId(id, callback) {
        // SQL para selecionar um usuário específico na tabela USUARIO por ID
        const sql = 'SELECT * FROM USUARIO WHERE id = ?';
        
        // Executa o SQL com o ID fornecido
        db.get(sql, [id], (err, row) => {
            if (err) {
                // Se houver um erro, exibe uma mensagem de erro e chama o callback com o erro
                console.error('Erro ao buscar usuario:', err.message);
                callback(err);
            } else {
                // Se não houver erro, chama o callback com a linha (row) retornada
                callback(null, row);
            }
        });
    }

    // Método estático para deletar um usuário por ID
    static deletarPorId(id, callback) {
        // SQL para deletar um usuário específico na tabela USUARIO por ID
        const sql = 'DELETE FROM USUARIO WHERE id = ?';
        
        // Executa o SQL com o ID fornecido
        db.run(sql, [id], function(err) {
            if (err) {
                // Se houver um erro, exibe uma mensagem de erro e chama o callback com o erro
                console.error('Erro ao deletar usuario:', err.message);
                callback(err);
            } else {
                // Se não houver erro, chama o callback com o ID do usuário deletado
                callback(null, { deletedID: id });
            }
        });
    }

    // Método estático para atualizar um usuário por ID
    static atualizarPorId(id, { nome, email, status }, callback) {
        // SQL para atualizar um usuário específico na tabela USUARIO por ID
        const sql = 'UPDATE USUARIO SET nome = ?, email = ?, status = ? WHERE id = ?';
        // Parâmetros a serem atualizados no SQL
        const params = [nome, email, status, id];
        
        // Executa o SQL com os parâmetros fornecidos
        db.run(sql, params, function(err) {
            if (err) {
                // Se houver um erro, exibe uma mensagem de erro e chama o callback com o erro
                console.error('Erro ao atualizar usuario:', err.message);
                callback(err);
            } else {
                // Se não houver erro, chama o callback com os dados do usuário atualizado
                callback(null, { id, nome, email, status });
            }
        });
    }
}

// Exporta a classe Usuario para que possa ser usada em outras partes da aplicação
module.exports = Usuario;

```

### 6. Controladores
Crie o arquivo `src/usuario/controllers/usuarioController.js`:

```javascript
const Usuario = require('../models/usuarioModel'); // Importa o modelo Usuario

// Função para listar todos os usuários
exports.listarUsuarios = (req, res) => {
    Usuario.buscarTodos((err, usuarios) => {
        if (err) {
            // Se houver um erro, responde com status 500 (Erro Interno do Servidor) e uma mensagem de erro
            res.status(500).json({ mensagem: 'Erro ao listar usuarios', erro: err.message });
        } else {
            // Se não houver erro, responde com status 200 (OK) e a lista de usuários
            res.status(200).json(usuarios);
        }
    });
};

// Função para listar um usuário específico pelo ID
exports.listarUsuario = (req, res) => {
    const { id } = req.params; // Extrai o ID dos parâmetros da requisição
    Usuario.buscarPorId(id, (err, usuario) => {
        if (err) {
            // Se houver um erro, responde com status 500 (Erro Interno do Servidor) e uma mensagem de erro
            res.status(500).json({ mensagem: 'Erro ao obter usuario', erro: err.message });
        } else if (!usuario) {
            // Se o usuário não for encontrado, responde com status 404 (Não Encontrado) e uma mensagem de erro
            res.status(404).json({ mensagem: 'Usuario não localizado.' });
        } else {
            // Se não houver erro e o usuário for encontrado, responde com status 200 (OK) e os dados do usuário
            res.status(200).json(usuario);
        }
    });
};

// Função para cadastrar um novo usuário
exports.cadastrarUsuario = (req, res) => {
    const { nome, email, status } = req.body; // Extrai os dados do corpo da requisição
    if (!nome || !email || !status) {
        // Se algum campo obrigatório estiver faltando, responde com status 400 (Requisição Inválida) e uma mensagem de erro
        return res.status(400).json({ mensagem: 'Todos os campos são obrigatórios.' });
    }
    Usuario.create({ nome, email, status }, (err, usuario) => {
        if (err) {
            // Se houver um erro, responde com status 500 (Erro Interno do Servidor) e uma mensagem de erro
            res.status(500).json({ mensagem: 'Erro ao cadastrar usuario', erro: err.message });
        } else {
            // Se não houver erro, responde com status 201 (Criado) e os dados do novo usuário
            res.status(201).json(usuario);
        }
    });
};

// Função para atualizar um usuário existente pelo ID
exports.atualizarUsuario = (req, res) => {
    const { id } = req.params; // Extrai o ID dos parâmetros da requisição
    const { nome, email, status } = req.body; // Extrai os dados do corpo da requisição
    if (!nome || !email || !status) {
        // Se algum campo obrigatório estiver faltando, responde com status 400 (Requisição Inválida) e uma mensagem de erro
        return res.status(400).json({ mensagem: 'Todos os campos são obrigatórios.' });
    }
    Usuario.atualizarPorId(id, { nome, email, status }, (err, usuario) => {
        if (err) {
            // Se houver um erro, responde com status 500 (Erro Interno do Servidor) e uma mensagem de erro
            res.status(500).json({ mensagem: 'Erro ao atualizar usuario', erro: err.message });
        } else if (!usuario) {
            // Se o usuário não for encontrado, responde com status 404 (Não Encontrado) e uma mensagem de erro
            res.status(404).json({ mensagem: 'Usuario não localizado.' });
        } else {
            // Se não houver erro e o usuário for atualizado, responde com status 200 (OK) e os dados do usuário atualizado
            res.status(200).json(usuario);
        }
    });
};

// Função para deletar um usuário pelo ID
exports.deletarUsuario = (req, res) => {
    const { id } = req.params; // Extrai o ID dos parâmetros da requisição
    Usuario.deletarPorId(id, (err) => {
        if (err) {
            // Se houver um erro, responde com status 500 (Erro Interno do Servidor) e uma mensagem de erro
            res.status(500).json({ mensagem: 'Erro ao deletar usuario', erro: err.message });
        } else {
            // Se não houver erro, responde com status 200 (OK) e uma mensagem de sucesso
            res.status(200).json({ mensagem: 'Usuario deletado com sucesso.' });
        }
    });
};

```

### 7. Rotas
Crie o arquivo `src/usuario/routes/usuarioRoutes.js`:

```javascript
const express = require('express'); // Importa o framework Express
const router = express.Router(); // Cria uma nova instância do roteador do Express
const usuarioController = require('../controllers/usuarioController'); // Importa o controlador de usuário

// Define a rota GET para listar todos os usuários
// Quando uma requisição GET for feita para '/usuarios', o método 'listarUsuarios' do controlador será chamado
router.get('/usuarios', usuarioController.listarUsuarios);

// Define a rota GET para obter um usuário específico pelo ID
// Quando uma requisição GET for feita para '/usuarios/:id', o método 'listarUsuario' do controlador será chamado
router.get('/usuarios/:id', usuarioController.listarUsuario);

// Define a rota POST para cadastrar um novo usuário
// Quando uma requisição POST for feita para '/usuarios', o método 'cadastrarUsuario' do controlador será chamado
router.post('/usuarios', usuarioController.cadastrarUsuario);

// Define a rota PUT para atualizar um usuário específico pelo ID
// Quando uma requisição PUT for feita para '/usuarios/:id', o método 'atualizarUsuario' do controlador será chamado
router.put('/usuarios/:id', usuarioController.atualizarUsuario);

// Define a rota DELETE para deletar um usuário específico pelo ID
// Quando uma requisição DELETE for feita para '/usuarios/:id', o método 'deletarUsuario' do controlador será chamado
router.delete('/usuarios/:id', usuarioController.deletarUsuario);

// Exporta o roteador para que possa ser utilizado em outras partes da aplicação
module.exports = router;

```

### 8. Configuração do Aplicativo
Crie o arquivo `src/app.js`:

```javascript
const express = require('express');
const app = express();

// Middleware para parsear JSON
app.use(express.json());

// Importa as rotas do usuário
const usuarioRoutes = require('./usuario/routes/usuarioRoutes');

// Usa as rotas do usuário
app.use('/api', usuarioRoutes);

module.exports = app;
```

### 9. Inicialização do Servidor
Crie o arquivo `src/server.js`:

```javascript
const app = require('./app'); // Importa o aplicativo Express configurado
const port = process.env.PORT || 3000; // Define a porta na qual o servidor irá ouvir

// Inicia o servidor e faz com que ele comece a ouvir por requisições na porta especificada
app.listen(port, () => {
    console.log(`Servidor rodando na porta ${port}`); // Exibe uma mensagem no console quando o servidor estiver funcionando
});

```

### 10. Configuração do `package.json`
Atualize o arquivo `package.json` para incluir o script `dev`:

```json
{
  "name": "backend",
  "version": "1.0.0",
  "main": "src/server.js",
  "scripts": {
    "start": "node src/server.js",
    "dev": "nodemon src/server.js",
    "test": "echo "Error: no test specified" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "description": "",
  "dependencies": {
    "express": "^4.19.2",
    "sqlite3": "^5.1.7"
  },
  "devDependencies": {
    "nodemon": "^3.1.0"
  }
}
```
## Configuração e Uso do SQLite com Migrations

### Passos para Configuração

### 1. Instalar Dependências

Instale as dependências necessárias:

```bash
npm install sqlite3 knex date-fns date-fns-tz
```

### 2. Configuração do Banco de Dados

Crie um arquivo `db.js` na pasta `config` para configurar a conexão com o banco de dados SQLite:

```javascript
const path = require('path');
const sqlite3 = require('sqlite3').verbose();

const dbPath = path.resolve(__dirname, '../../data/database.sqlite');

const db = new sqlite3.Database(dbPath, (err) => {
    if (err) {
        console.error('Erro ao abrir o banco de dados:', err.message);
    } else {
        console.log('Conectado ao banco de dados SQLite.');
    }
});

module.exports = db;
```

### 3. Configuração do Knex

Crie um arquivo `knexfile.js` na raiz do seu projeto para configurar o Knex:

```javascript
module.exports = {
  development: {
    client: 'sqlite3',
    connection: {
      filename: './data/database.sqlite'
    },
    useNullAsDefault: true
  }
};
```

### 4. Inicializar o Knex

Inicialize o Knex no seu projeto:

```bash
npx knex init
```

### 5. Criar Migração

Crie uma nova migração para a tabela `USUARIO`:

```bash
npx knex migrate:make create_usuarios_table
```

### 6. Definir a Migração

Edite o arquivo de migração criado na pasta `migrations` para definir a tabela `USUARIO`:

```javascript
exports.up = function(knex) {
  return knex.schema.createTable('USUARIO', function(table) {
    table.increments('id').primary();
    table.string('nome').notNullable();
    table.string('email').notNullable();
    table.string('status').notNullable();
    table.string('foto_url');
    table.timestamp('data_criacao').defaultTo(knex.fn.now());
  });
};

exports.down = function(knex) {
  return knex.schema.dropTable('USUARIO');
};
```

### 7. Executar a Migração

Execute a migração para aplicar as mudanças no banco de dados:

```bash
npx knex migrate:latest
```

### 8. Manipulação de Datas de Cadastro

Utilize o `date-fns` e `date-fns-tz` para manipular datas no fuso horário do Brasil:

```bash
npm install date-fns date-fns-tz
```

### 9. Modelo `usuarioModel.js`

Adapte o modelo para utilizar a conexão e métodos do `sqlite3`:

```javascript
const db = require('../../config/db');
const { format } = require('date-fns');
const { utcToZonedTime, format: formatTZ } = require('date-fns-tz');

class Usuario {
  static buscarTodos(callback) {
    db.all('SELECT * FROM USUARIO', [], (err, rows) => {
      callback(err, rows);
    });
  }

  static buscarPorId(id, callback) {
    db.get('SELECT * FROM USUARIO WHERE id = ?', [id], (err, row) => {
      callback(err, row);
    });
  }

  static create({ nome, email, status, foto_url }, callback) {
    const now = new Date();
    const timeZone = 'America/Sao_Paulo';
    const zonedDate = utcToZonedTime(now, timeZone);
    const formattedDataCriacao = formatTZ(zonedDate, 'yyyy-MM-dd HH:mm:ssXXX', { timeZone });

    const query = 'INSERT INTO USUARIO (nome, email, status, foto_url, data_criacao) VALUES (?, ?, ?, ?, ?)';
    const params = [nome, email, status, foto_url, formattedDataCriacao];

    db.run(query, params, function(err) {
      if (err) {
        callback(err);
      } else {
        callback(null, { id: this.lastID, nome, email, status, foto_url, data_criacao: formattedDataCriacao });
      }
    });
  }

  static atualizarPorId(id, { nome, email, status, foto_url }, callback) {
    const query = 'UPDATE USUARIO SET nome = ?, email = ?, status = ?, foto_url = ? WHERE id = ?';
    const params = [nome, email, status, foto_url, id];

    db.run(query, params, function(err) {
      if (err) {
        callback(err);
      } else {
        callback(null, { id, nome, email, status, foto_url });
      }
    });
  }

  static deletarPorId(id, callback) {
    db.run('DELETE FROM USUARIO WHERE id = ?', [id], function(err) {
      callback(err);
    });
  }
}

module.exports = Usuario;
```

### 10. Controlador `usuarioController.js`

O controlador não precisa ser alterado, pois ele continuará chamando os métodos do modelo `Usuario` da mesma maneira.

## Adaptação para SQL Server e MySQL

Para adaptar o código para usar um banco de dados SQL Server ou MySQL em vez do SQLite, você precisará fazer algumas alterações no código. Vou fornecer exemplos para ambos os bancos de dados.

### SQL Server

#### 1. Instale a biblioteca mssql
```bash
npm install mssql
```

#### 2. Configuração do banco de dados (`config/db.js`)
```javascript
const sql = require('mssql');

// Configuração da conexão com o banco de dados SQL Server
const dbConfig = {
    user: 'seu_usuario',       // Substitua pelo seu usuário do banco de dados
    password: 'sua_senha',     // Substitua pela sua senha do banco de dados
    server: 'seu_servidor',    // Substitua pelo endereço do servidor
    database: 'sua_base_de_dados', // Substitua pelo nome do banco de dados
    options: {
        encrypt: true, // Use this if you're on Windows Azure
        enableArithAbort: true
    }
};

// Conecta ao banco de dados
const poolPromise = new sql.ConnectionPool(dbConfig)
    .connect()
    .then(pool => {
        console.log('Conectado ao banco de dados SQL Server.');
        return pool;
    })
    .catch(err => {
        console.error('Erro ao conectar ao banco de dados SQL Server:', err.message);
    });

module.exports = {
    sql, poolPromise
};
```

#### 3. Criação da tabela (se necessário)

Você pode criar a tabela manualmente ou programaticamente. Aqui está um exemplo de como criar a tabela programaticamente:

```javascript
const { sql, poolPromise } = require('./db');

async function createTable() {
    try {
        const pool = await poolPromise;
        await pool.request().query(\`
            IF NOT EXISTS (SELECT * FROM sysobjects WHERE name='USUARIO' and xtype='U')
            CREATE TABLE USUARIO (
                id INT IDENTITY(1,1) PRIMARY KEY,
                nome NVARCHAR(100) NOT NULL,
                email NVARCHAR(100) NOT NULL,
                status NVARCHAR(50) NOT NULL
            )
        \`);
        console.log('Tabela USUARIO criada ou já existe.');
    } catch (err) {
        console.error('Erro ao criar a tabela USUARIO:', err.message);
    }
}

createTable();
```

### MySQL

#### 1. Instale a biblioteca mysql2
```bash
npm install mysql2
```

#### 2. Configuração do banco de dados (`config/db.js`)
```javascript
const mysql = require('mysql2/promise');

// Configuração da conexão com o banco de dados MySQL
const dbConfig = {
    host: 'seu_servidor',      // Substitua pelo endereço do servidor
    user: 'seu_usuario',       // Substitua pelo seu usuário do banco de dados
    password: 'sua_senha',     // Substitua pela sua senha do banco de dados
    database: 'sua_base_de_dados' // Substitua pelo nome do banco de dados
};

// Conecta ao banco de dados
const pool = mysql.createPool(dbConfig);

pool.getConnection()
    .then(conn => {
        console.log('Conectado ao banco de dados MySQL.');
        conn.release();
    })
    .catch(err => {
        console.error('Erro ao conectar ao banco de dados MySQL:', err.message);
    });

module.exports = pool;
```

#### 3. Criação da tabela (se necessário)

Você pode criar a tabela manualmente ou programaticamente. Aqui está um exemplo de como criar a tabela programaticamente:

```javascript
const pool = require('./db');

async function createTable() {
    try {
        const conn = await pool.getConnection();
        await conn.query(\`
            CREATE TABLE IF NOT EXISTS USUARIO (
                id INT AUTO_INCREMENT PRIMARY KEY,
                nome VARCHAR(100) NOT NULL,
                email VARCHAR(100) NOT NULL,
                status VARCHAR(50) NOT NULL
            )
        \`);
        console.log('Tabela USUARIO criada ou já existe.');
        conn.release();
    } catch (err) {
        console.error('Erro ao criar a tabela USUARIO:', err.message);
    }
}

createTable();
```

### Comentários

#### Configuração da Conexão:
SQL Server e MySQL requerem configurações de conexão específicas que incluem o host/servidor, usuário, senha e nome do banco de dados.

#### Criação da Tabela:
A criação da tabela pode ser feita manualmente diretamente no banco de dados ou programaticamente, como mostrado nos exemplos.

#### Importação e Uso do Banco de Dados:
Você precisará importar o pool de conexões no seu modelo e usar os métodos apropriados para executar consultas SQL.

### Modelo de Dados para SQL Server e MySQL

Aqui está um exemplo de como modificar o modelo de dados `usuarioModel.js` para usar com SQL Server e MySQL.

#### SQL Server
```javascript
const { sql, poolPromise } = require('../../config/db');

class Usuario {
    static async create({ nome, email, status }, callback) {
        try {
            const pool = await poolPromise;
            const result = await pool.request()
                .input('nome', sql.NVarChar, nome)
                .input('email', sql.NVarChar, email)
                .input('status', sql.NVarChar, status)
                .query('INSERT INTO USUARIO (nome, email, status) VALUES (@nome, @email, @status); SELECT SCOPE_IDENTITY() AS id');
            callback(null, { id: result.recordset[0].id, nome, email, status });
        } catch (err) {
            console.error('Erro ao criar usuario:', err.message);
            callback(err);
        }
    }

    // Implementar os outros métodos de forma semelhante
}

module.exports = Usuario;
```

#### MySQL
```javascript
const pool = require('../../config/db');

class Usuario {
    static async create({ nome, email, status }, callback) {
        try {
            const conn = await pool.getConnection();
            const [result] = await conn.query('INSERT INTO USUARIO (nome, email, status) VALUES (?, ?, ?)', [nome, email, status]);
            callback(null, { id: result.insertId, nome, email, status });
            conn.release();
        } catch (err) {
            console.error('Erro ao criar usuario:', err.message);
            callback(err);
        }
    }

    // Implementar os outros métodos de forma semelhante
}

module.exports = Usuario;
```

Esses exemplos mostram como adaptar seu código para usar SQL Server ou MySQL em vez de SQLite, incluindo as etapas de configuração da conexão e a criação da tabela, bem como a implementação do modelo de dados para interagir com o banco de dados.

## Configuração e Uso do Swagger para Documentação de API

### Passos para Configuração

### 1. Instalar Dependências

Instale as dependências necessárias:

```bash
npm install swagger-jsdoc swagger-ui-express
```

### 2. Definir a Especificação OpenAPI

Crie um arquivo `swagger.js` na raiz do seu projeto para definir a especificação OpenAPI:

```javascript
const swaggerJSDoc = require('swagger-jsdoc');

const options = {
  definition: {
    openapi: '3.0.0',
    info: {
      title: 'API Exemplo',
      version: '1.0.0',
      description: 'Uma API exemplo usando Swagger'
    },
  },
  apis: ['./src/usuario/routes/*.js'], // Caminho para os arquivos de definição da API, * para pegar todos os caminhos
};

const swaggerSpec = swaggerJSDoc(options);

module.exports = swaggerSpec;
```

### 3. Configurar o Swagger UI no Seu Servidor

Adicione a configuração do Swagger UI no seu servidor. Edite o arquivo `server.js`:

```javascript
const express = require('express');
const swaggerUi = require('swagger-ui-express');
const swaggerSpec = require('../swagger');
const usuarioRoutes = require('./usuario/routes/usuarioRoutes'); 

const app = express();

app.use(express.json()); // Para permitir o parsing de JSON no corpo das requisições
app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerSpec));// rota da api
app.use('/usuarios', usuarioRoutes); 

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Servidor rodando na porta ${PORT}`);
});
```

### 4. Adicionar Comentários de Documentação nos Seus Arquivos de Rotas

Adicione comentários de documentação nos seus arquivos de rotas. Edite o arquivo `routes/usuarioRoutes.js`:

```javascript
const express = require('express');
const router = express.Router();
const usuarioController = require('../controllers/usuarioController');

/**
 * @swagger
 * components:
 *   schemas:
 *     Usuario:
 *       type: object
 *       required:
 *         - nome
 *         - email
 *         - status
 *       properties:
 *         id:
 *           type: integer
 *           description: O ID do usuário
 *         nome:
 *           type: string
 *           description: O nome do usuário
 *         email:
 *           type: string
 *           description: O email do usuário
 *         status:
 *           type: string
 *           description: O status do usuário
 *         foto_url:
 *           type: string
 *           description: URL da foto do usuário
 *         data_criacao:
 *           type: string
 *           format: date-time
 *           description: Data de criação do usuário
 */

/**
 * @swagger
 * /usuarios:
 *   get:
 *     summary: Retorna a lista de todos os usuários
 *     responses:
 *       200:
 *         description: A lista de usuários
 *         content:
 *           application/json:
 *             schema:
 *               type: array
 *               items:
 *                 $ref: '#/components/schemas/Usuario'
 */
router.get('/', usuarioController.listarUsuarios);

/**
 * @swagger
 * /usuarios/{id}:
 *   get:
 *     summary: Retorna um usuário pelo ID
 *     parameters:
 *       - in: path
 *         name: id
 *         required: true
 *         schema:
 *           type: integer
 *         description: O ID do usuário
 *     responses:
 *       200:
 *         description: Usuário encontrado
 *         content:
 *           application/json:
 *             schema:
 *               $ref: '#/components/schemas/Usuario'
 *       404:
 *         description: Usuário não encontrado
 */
router.get('/:id', usuarioController.listarUsuario);

/**
 * @swagger
 * /usuarios:
 *   post:
 *     summary: Cria um novo usuário
 *     requestBody:
 *       required: true
 *       content:
 *         application/json:
 *           schema:
 *             $ref: '#/components/schemas/Usuario'
 *     responses:
 *       201:
 *         description: Usuário criado com sucesso
 *         content:
 *           application/json:
 *             schema:
 *               $ref: '#/components/schemas/Usuario'
 *       400:
 *         description: Erro na requisição
 */
router.post('/', usuarioController.cadastrarUsuario);

/**
 * @swagger
 * /usuarios/{id}:
 *   put:
 *     summary: Atualiza um usuário pelo ID
 *     parameters:
 *       - in: path
 *         name: id
 *         required: true
 *         schema:
 *           type: integer
 *         description: O ID do usuário
 *     requestBody:
 *       required: true
 *       content:
 *         application/json:
 *           schema:
 *             $ref: '#/components/schemas/Usuario'
 *     responses:
 *       200:
 *         description: Usuário atualizado com sucesso
 *         content:
 *           application/json:
 *             schema:
 *               $ref: '#/components/schemas/Usuario'
 *       400:
 *         description: Erro na requisição
 *       404:
 *         description: Usuário não encontrado
 */
router.put('/:id', usuarioController.atualizarUsuario);

/**
 * @swagger
 * /usuarios/{id}:
 *   delete:
 *     summary: Deleta um usuário pelo ID
 *     parameters:
 *       - in: path
 *         name: id
 *         required: true
 *         schema:
 *           type: integer
 *         description: O ID do usuário
 *     responses:
 *       200:
 *         description: Usuário deletado com sucesso
 *       404:
 *         description: Usuário não encontrado
 */
router.delete('/:id', usuarioController.deletarUsuario);

module.exports = router;

```

### 5. Acessar a Documentação

Depois de configurar o Swagger e adicionar os comentários de documentação, você pode acessar a documentação interativa da sua API em `/api-docs` no seu servidor.

### Observações Finais

1. Certifique-se de que os módulos `swagger-jsdoc` e `swagger-ui-express` estão instalados no seu projeto.
2. Verifique se os comentários de documentação estão corretos e seguem a especificação OpenAPI.
3. Garanta que a documentação da API esteja sempre atualizada conforme você adiciona ou modifica endpoints na sua aplicação.

Com essas configurações, você terá uma documentação interativa da sua API disponível para visualização e testes.

