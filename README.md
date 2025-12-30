# app.py - Asosiy Flask ilovasi
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy
from werkzeug.security import generate_password_hash, check_password_hash

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://user:password@localhost/movie_db'
db = SQLAlchemy(app)

# 1. Foydalanuvchi modeli
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    telegram_username = db.Column(db.String(80), unique=True, nullable=False)
    password_hash = db.Column(db.String(200), nullable=False) # Hashlangan parol
    is_admin = db.Column(db.Boolean, default=False)

    def set_password(self, password):
        self.password_hash = generate_password_hash(password)

    def check_password(self, password):
        return check_password_hash(self.password_hash, password)

# 2. Foydalanuvchini ro'yxatdan o'tkazish endpoint'i
@app.route('/register', methods=['POST'])
def register():
    data = request.get_json()
    username = data.get('username')
    password = data.get('password')

    if User.query.filter_by(telegram_username=username).first():
        return jsonify({'error': 'Username already exists'}), 400

    new_user = User(telegram_username=username)
    new_user.set_password(password)

    # Agar admin bo'lsa, belgilash (masalan, login 'Shodiyor1403')
    if username == 'Shodiyor1403':
        new_user.is_admin = True

    db.session.add(new_user)
    db.session.commit()
    return jsonify({'message': 'User created successfully'}), 201

# 3. Admin uchun barcha foydalanuvchilar ro'yxatini olish
@app.route('/admin/users', methods=['GET'])
def get_all_users():
    # Bu yerda haqiqiy loyihada token yoki sessiya orqali adminligini tekshirish kerak
    users = User.query.all()
    result = [{'id': u.id, 'username': u.telegram_username, 'is_admin': u.is_admin} for u in users]
    return jsonify(result)

# 4. Mini App uchun filmalar ro'yxatini qaytarish (keyin Telegram kanaldan o'qib chiqiladi)
@app.route('/movies', methods=['GET'])
def get_movies():
    # Bu yerda kod yozilishi kerak: Telegram kanalingizga ulanish va postlarni o'qish
    movies_list = [{'id': 1, 'title': 'Film Nomi 1', 'link': '...'}, {'id': 2, 'title': 'Film Nomi 2', 'link': '...'}]
    return jsonify(movies_list)

if __name__ == '__main__':
    db.create_all() # Jadval yaratish (faqat birinchi marta)
    app.run(debug=True)
