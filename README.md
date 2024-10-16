Para hospedar uma aplicação web básica em sua máquina utilizando Flask, siga os passos abaixo:

### 1. Instalação do Python e Flask

Certifique-se de que você tenha o Python instalado em sua máquina. Você pode baixar o Python em [python.org](https://www.python.org/downloads/). A versão 3.x é recomendada.

Depois de instalar o Python, você pode usar o seguinte comando para instalar o Flask:

```bash
pip install Flask
```

### 2. Estrutura do Projeto

Crie uma nova pasta para sua aplicação. Por exemplo, `catalogo_poses`. Dentro dela, crie a seguinte estrutura de diretórios e arquivos:

```
/catalogo_poses
    /templates
        admin.html
        index.html
        login.html
    app.py
```

### 3. Código do `app.py`

Crie o arquivo `app.py` com o seguinte código:

```python
from flask import Flask, render_template, request, redirect, url_for, session, flash
from functools import wraps

app = Flask(__name__)
app.secret_key = 'seu_secreto_aqui'  # Altere isso para algo mais seguro

# Mock de usuários
users = {
    "admin": "senha123"  # admin e senha para login
}

# Decorador para exigir acesso de administrador
def admin_required(f):
    @wraps(f)
    def decorated_function(*args, kwargs):
        if 'logged_in' not in session:
            flash('Você precisa estar logado como admin para acessar essa página.')
            return redirect(url_for('login'))
        return f(*args, kwargs)
    return decorated_function

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        if username in users and users[username] == password:
            session['logged_in'] = True
            return redirect(url_for('admin_panel'))
        flash('Credenciais inválidas.')
    return render_template('login.html')

@app.route('/admin')
@admin_required
def admin_panel():
    return render_template('admin.html')

@app.route('/logout')
@admin_required
def logout():
    session.pop('logged_in', None)
    flash('Você saiu com sucesso!')
    return redirect(url_for('index'))

if __name__ == '__main__':
    app.run(debug=True)
```

### 4. Templates HTML

#### `admin.html`

Crie um arquivo chamado `admin.html` no diretório `templates` com o seguinte conteúdo:

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Painel Admin</title>
</head>
<body>
    <h1>Painel de Administração</h1>
    <form method="POST" action="/add_pose">
        <label for="pose">Tipo da Pose:</label>
        <input type="text" id="pose" name="pose" required>
        <label for="video">Link do Vídeo:</label>
        <input type="url" id="video" name="video" required>
        <button type="submit">Adicionar Pose</button>
    </form>
    <a href="/logout">Sair</a>
</body>
</html>
```

#### `index.html`

Crie um arquivo chamado `index.html` no diretório `templates`:

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Catálogo de Poses</title>
</head>
<body>
    <h1>Bem-vindo ao Catálogo de Poses</h1>
    <a href="/login">Acessar Admin</a>
</body>
</html>
```

#### `login.html`

Crie um arquivo chamado `login.html` no diretório `templates`:

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
</head>
<body>
    <h1>Login de Administrador</h1>
    <form method="POST">
        <label for="username">Usuário:</label>
        <input type="text" id="username" name="username" required>
        <label for="password">Senha:</label>
        <input type="password" id="password" name="password" required>
        <button type="submit">Entrar</button>
    </form>
    {% with messages = get_flashed_messages() %}
      {% if messages %}
        <ul>
        {% for message in messages %}
          <li>{{ message }}</li>
        {% endfor %}
        </ul>
      {% endif %}
    {% endwith %}
</body>
</html>
```

### 5. Executando a Aplicação

Agora, você está pronto para executar a aplicação. Abra um terminal, navegue até o diretório `catalogo_poses` e execute:

```bash
python app.py
```

Por padrão, a aplicação será executada em `http://127.0.0.1:5000/`. Você pode abrir esse endereço em um navegador.

### 6. Testando

1. Visite a Página Inicial: Acesse `http://127.0.0.1:5000/`.
2. Login: Clique em "Acessar Admin" e faça login usando as credenciais (`admin` e `senha123`).
3. Painel de Admin: Você será redirecionado para o painel de administração, onde pode adicionar poses (note que a funcionalidade de adicionar poses ainda não foi implementada; esta é uma base que você pode expandir).

### Próximos Passos

- Persistência de Dados: Para armazenar as poses e vídeos, você pode integrar um banco de dados (por exemplo, SQLite).
- Validações: Você pode adicionar validações e melhorias na UI.
- Gerenciamento de Estilos: Use CSS para estilizar sua aplicação.

Isso deve lhe dar um bom ponto de partida para testar a aplicação em sua máquina!
