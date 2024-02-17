pip install Flask
from flask import Flask, render_template, request, redirect, url_for, flash
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SECRET_KEY'] = 'your_secret_key'
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///store.db'
db = SQLAlchemy(app)

# Definição do modelo para produtos
class Product(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(50), nullable=False)
    price = db.Column(db.Float, nullable=False)

# Criação do banco de dados
db.create_all()

@app.route('/')
def index():
    products = Product.query.all()
    return render_template('index.html', products=products)

@app.route('/add_to_cart/<int:product_id>')
def add_to_cart(product_id):
    product = Product.query.get(product_id)
    if product:
        flash(f'{product.name} adicionado ao carrinho!', 'success')
    else:
        flash('Produto não encontrado!', 'danger')
    return redirect(url_for('index'))

if __name__ == '__main__':
    app.run(debug=True)
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Loja Online</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css">
</head>
<body>
    <div class="container mt-5">
        <h1>Produtos</h1>
        {% for product in products %}
            <div class="card mt-3">
                <div class="card-body">
                    <h5 class="card-title">{{ product.name }}</h5>
                    <p class="card-text">Preço: R$ {{ product.price }}</p>
                    <a href="{{ url_for('add_to_cart', product_id=product.id) }}" class="btn btn-primary">Adicionar ao Carrinho</a>
                </div>
            </div>
        {% endfor %}
    </div>

    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js"></script>
</body>
</html>
