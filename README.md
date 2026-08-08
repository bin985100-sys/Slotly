# Slotly
Что сделала нейросеть: анимация 3D-офиса и приближения, небольшое количество дизайна и анимации, подключила api ключ от Нейросети

Изначально все писалось локально, код локально.После этого было отправленно нейросети на доработку.

from fastapi import FastAPI, Request, Form
from fastapi.responses import HTMLResponse, RedirectResponse

app = FastAPI()
bookings = []

KorenCode = """
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>MTS Booking</title>
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: Arial, sans-serif; background: #f5f5f5; display: flex; justify-content: center; align-items: center; height: 100vh; }
.container { background: white; padding: 50px; border-radius: 10px; box-shadow: 0 0 30px rgba(0,0,0,0.1); text-align: center; max-width: 600px; }
h1 { color: #e2001a; font-size: 36px; margin-bottom: 10px; }
.subtitle { color: #666; font-size: 18px; margin-bottom: 30px; }
.btn { display: inline-block; padding: 15px 50px; background: #e2001a; color: white; text-decoration: none; border-radius: 5px; font-size: 18px; font-weight: bold; transition: background 0.3s; margin: 5px; }
.btn:hover { background: #c40016; }
.btn-outline { background: transparent; color: #e2001a; border: 2px solid #e2001a; }
.btn-outline:hover { background: #e2001a; color: white; }
</style>
</head>
<body>
<div class="container">
<h1>MTS Booking</h1>
<p class="subtitle">Бронируйте рабочие места в офисах MTS</p>
<a href="/register" class="btn">Войти в систему</a>
<br><br>
<a href="/cabinet" class="btn btn-outline">Мой кабинет</a>
</div>
</body>
</html>
"""

RegistaciaCode = """
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>MTS Booking - Вход</title>
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: Arial, sans-serif; background: #f5f5f5; display: flex; justify-content: center; align-items: center; height: 100vh; }
.login-box { background: white; padding: 40px; border-radius: 10px; box-shadow: 0 0 30px rgba(0,0,0,0.1); width: 400px; }
h2 { color: #333; text-align: center; margin-bottom: 30px; font-size: 28px; }
h2 span { color: #e2001a; }
.form-group { margin-bottom: 20px; }
label { display: block; margin-bottom: 5px; color: #555; font-weight: bold; }
input[type="email"], input[type="password"] { width: 100%; padding: 12px; border: 2px solid #ddd; border-radius: 5px; font-size: 16px; }
input:focus { outline: none; border-color: #e2001a; }
button { width: 100%; padding: 14px; background: #e2001a; color: white; border: none; border-radius: 5px; font-size: 18px; font-weight: bold; cursor: pointer; transition: background 0.3s; }
button:hover { background: #c40016; }
.links { text-align: center; margin-top: 20px; }
.links a { color: #e2001a; text-decoration: none; font-weight: bold; }
.links a:hover { text-decoration: underline; }
</style>
</head>
<body>
<div class="login-box">
<h2>Вход в <span>MTS Booking</span></h2>
<form action="/login" method="post">
<div class="form-group">
<label>Email</label>
<input type="email" name="email" placeholder="example@mail.ru" required>
</div>
<div class="form-group">
<label>Пароль</label>
<input type="password" name="password" placeholder="Введите пароль" required>
</div>
<button type="submit">Войти</button>
</form>
<div class="links">
<a href="/">На главную</a>
</div>
</div>
</body>
</html>
"""

OfficeCode = """
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>MTS Booking - Выбор офиса</title>
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: Arial, sans-serif; background: #f5f5f5; display: flex; justify-content: center; align-items: center; height: 100vh; }
.container { background: white; padding: 40px; border-radius: 10px; box-shadow: 0 0 30px rgba(0,0,0,0.1); width: 500px; }
h2 { color: #333; text-align: center; margin-bottom: 30px; font-size: 28px; }
h2 span { color: #e2001a; }
.form-group { margin-bottom: 20px; }
label { display: block; margin-bottom: 5px; color: #555; font-weight: bold; }
select { width: 100%; padding: 12px; border: 2px solid #ddd; border-radius: 5px; font-size: 16px; background: white; }
select:focus { outline: none; border-color: #e2001a; }
button { width: 100%; padding: 14px; background: #e2001a; color: white; border: none; border-radius: 5px; font-size: 18px; font-weight: bold; cursor: pointer; transition: background 0.3s; }
button:hover { background: #c40016; }
.links { text-align: center; margin-top: 20px; }
.links a { color: #e2001a; text-decoration: none; font-weight: bold; }
.links a:hover { text-decoration: underline; }
</style>
</head>
<body>
<div class="container">
<h2>Выбор <span>офиса</span></h2>
<form action="/select_office" method="post">
<div class="form-group">
<label>Город</label>
<select name="city" required>
<option value="">-- Выберите город --</option>
<option value="Москва">Москва</option>
<option value="Санкт-Петербург">Санкт-Петербург</option>
<option value="Казань">Казань</option>
<option value="Новосибирск">Новосибирск</option>
<option value="Екатеринбург">Екатеринбург</option>
</select>
</div>
<div class="form-group">
<label>Офис</label>
<select name="office" required>
<option value="">-- Выберите офис --</option>
<option value="MTS Центральный">MTS Центральный</option>
<option value="MTS Северный">MTS Северный</option>
<option value="MTS Южный">MTS Южный</option>
<option value="MTS Восточный">MTS Восточный</option>
<option value="MTS Западный">MTS Западный</option>
</select>
</div>
<button type="submit">Далее</button>
</form>
<div class="links">
<a href="/register">Назад</a>
</div>
</div>
</body>
</html>
"""

BookingCode = """
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>MTS Booking - Бронирование</title>
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: Arial, sans-serif; background: #f5f5f5; display: flex; justify-content: center; align-items: center; min-height: 100vh; padding: 20px; }
.container { background: white; padding: 40px; border-radius: 10px; box-shadow: 0 0 30px rgba(0,0,0,0.1); width: 500px; }
h2 { color: #333; text-align: center; margin-bottom: 10px; font-size: 28px; }
h2 span { color: #e2001a; }
.info { background: #f9f9f9; padding: 15px; border-radius: 5px; margin: 15px 0 25px 0; text-align: center; color: #555; }
.info strong { color: #e2001a; }
.form-group { margin-bottom: 20px; }
label { display: block; margin-bottom: 5px; color: #555; font-weight: bold; }
select, input[type="date"] { width: 100%; padding: 12px; border: 2px solid #ddd; border-radius: 5px; font-size: 16px; background: white; }
select:focus, input[type="date"]:focus { outline: none; border-color: #e2001a; }
button { width: 100%; padding: 14px; background: #e2001a; color: white; border: none; border-radius: 5px; font-size: 18px; font-weight: bold; cursor: pointer; transition: background 0.3s; }
button:hover { background: #c40016; }
.links { text-align: center; margin-top: 20px; }
.links a { color: #e2001a; text-decoration: none; font-weight: bold; }
.links a:hover { text-decoration: underline; }
</style>
</head>
<body>
<div class="container">
<h2>Бронирование <span>места</span></h2>
<div class="info">
<strong>Офис:</strong> {{ office }} &nbsp;|&nbsp; <strong>Город:</strong> {{ city }}
</div>
<form action="/book" method="post">
<input type="hidden" name="office" value="{{ office }}">
<input type="hidden" name="city" value="{{ city }}">
<div class="form-group">
<label>Место</label>
<select name="seat" required>
<option value="">-- Выберите место --</option>
<option value="Место 1">Место 1 (свободно)</option>
<option value="Место 2">Место 2 (свободно)</option>
<option value="Место 3">Место 3 (занято)</option>
<option value="Место 4">Место 4 (свободно)</option>
<option value="Место 5">Место 5 (свободно)</option>
<option value="Место 6">Место 6 (занято)</option>
<option value="Место 7">Место 7 (свободно)</option>
<option value="Место 8">Место 8 (свободно)</option>
</select>
</div>
<div class="form-group">
<label>Дата</label>
<input type="date" name="date" required>
</div>
<div class="form-group">
<label>Время</label>
<select name="time" required>
<option value="">-- Выберите время --</option>
<option value="09:00">09:00</option>
<option value="10:00">10:00</option>
<option value="11:00">11:00</option>
<option value="12:00">12:00</option>
<option value="13:00">13:00</option>
<option value="14:00">14:00</option>
<option value="15:00">15:00</option>
<option value="16:00">16:00</option>
<option value="17:00">17:00</option>
<option value="18:00">18:00</option>
</select>
</div>
<button type="submit">Забронировать</button>
</form>
<div class="links">
<a href="/offices">Назад</a>
</div>
</div>
</body>
</html>
"""

SuccessCode = """
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>MTS Booking - Успешно</title>
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: Arial, sans-serif; background: #f5f5f5; display: flex; justify-content: center; align-items: center; height: 100vh; }
.container { background: white; padding: 50px; border-radius: 10px; box-shadow: 0 0 30px rgba(0,0,0,0.1); text-align: center; max-width: 500px; }
h1 { color: #28a745; font-size: 36px; margin-bottom: 10px; }
.subtitle { color: #666; font-size: 18px; margin-bottom: 30px; }
.details { text-align: left; background: #f9f9f9; padding: 20px; border-radius: 5px; margin-bottom: 30px; }
.details p { padding: 5px 0; color: #555; }
.details strong { color: #e2001a; }
.btn { display: inline-block; padding: 12px 40px; background: #e2001a; color: white; text-decoration: none; border-radius: 5px; font-size: 16px; font-weight: bold; transition: background 0.3s; margin: 5px; }
.btn:hover { background: #c40016; }
.btn-outline { background: transparent; color: #e2001a; border: 2px solid #e2001a; }
.btn-outline:hover { background: #e2001a; color: white; }
</style>
</head>
<body>
<div class="container">
<h1>Бронирование успешно!</h1>
<p class="subtitle">Ваше место забронировано</p>
<div class="details">
<p><strong>Офис:</strong> {{ office }}</p>
<p><strong>Город:</strong> {{ city }}</p>
<p><strong>Место:</strong> {{ seat }}</p>
<p><strong>Дата:</strong> {{ date }}</p>
<p><strong>Время:</strong> {{ time }}</p>
</div>
<a href="/cabinet" class="btn">Мой кабинет</a>
<a href="/" class="btn btn-outline">На главную</a>
</div>
</body>
</html>
"""

CabinetCode = """
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>MTS Booking - Мой кабинет</title>
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: Arial, sans-serif; background: #f5f5f5; display: flex; justify-content: center; align-items: center; min-height: 100vh; padding: 20px; }
.container { background: white; padding: 40px; border-radius: 10px; box-shadow: 0 0 30px rgba(0,0,0,0.1); max-width: 700px; width: 100%; }
h2 { color: #333; margin-bottom: 10px; font-size: 28px; }
h2 span { color: #e2001a; }
.subtitle { color: #666; margin-bottom: 30px; font-size: 16px; }
.booking-card { background: #f9f9f9; padding: 20px; border-radius: 5px; margin-bottom: 15px; border-left: 4px solid #e2001a; }
.booking-card p { padding: 5px 0; color: #555; }
.booking-card strong { color: #333; }
.empty { text-align: center; padding: 40px 0; color: #999; font-size: 18px; }
.btn { display: inline-block; padding: 12px 30px; background: #e2001a; color: white; text-decoration: none; border-radius: 5px; font-size: 16px; font-weight: bold; transition: background 0.3s; margin-top: 20px; }
.btn:hover { background: #c40016; }
.header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
</style>
</head>
<body>
<div class="container">
<div class="header">
<h2>Мой <span>кабинет</span></h2>
<a href="/" style="color: #e2001a; text-decoration: none; font-weight: bold;">На главную</a>
</div>
<p class="subtitle">Ваши активные бронирования</p>
{{ bookings }}
<a href="/offices" class="btn">Новое бронирование</a>
</div>
</body>
</html>
"""

@app.get("/", response_class=HTMLResponse)
async def index(request: Request):
    return KorenCode

@app.get("/register", response_class=HTMLResponse)
async def reed_root(request: Request):
    return RegistaciaCode

@app.post("/login")
async def login(email: str = Form(...), password: str = Form(...)):
    print(f"Email: {email}, Пароль: {password}")
    return RedirectResponse(url="/offices", status_code=303)

@app.get("/offices", response_class=HTMLResponse)
async def offices(request: Request):
    return OfficeCode

@app.post("/select_office")
async def select_office(city: str = Form(...), office: str = Form(...)):
    print(f"Город: {city}, Офис: {office}")
    return RedirectResponse(url=f"/booking/{office}/{city}", status_code=303)

@app.get("/booking/{office}/{city}", response_class=HTMLResponse)
async def booking(request: Request, office: str, city: str):
    return BookingCode.replace("{{ office }}", office).replace("{{ city }}", city)

@app.post("/book", response_class=HTMLResponse)
async def book(office: str = Form(...), city: str = Form(...), seat: str = Form(...), date: str = Form(...), time: str = Form(...)):
    booking_data = {
        "office": office,
        "city": city,
        "seat": seat,
        "date": date,
        "time": time
    }
    bookings.append(booking_data)
    print(f"Бронирование: {office}, {city}, {seat}, {date}, {time}")
    result = SuccessCode.replace("{{ office }}", office)
    result = result.replace("{{ city }}", city)
    result = result.replace("{{ seat }}", seat)
    result = result.replace("{{ date }}", date)
    result = result.replace("{{ time }}", time)
    return result

@app.get("/cabinet", response_class=HTMLResponse)
async def cabinet(request: Request):
    if not bookings:
        booking_html = '<div class="empty">У вас пока нет бронирований</div>'
    else:
        booking_html = ""
        for b in bookings:
            booking_html += f'''
            <div class="booking-card">
                <p><strong>Офис:</strong> {b["office"]}</p>
                <p><strong>Город:</strong> {b["city"]}</p>
                <p><strong>Место:</strong> {b["seat"]}</p>
                <p><strong>Дата:</strong> {b["date"]}</p>
                <p><strong>Время:</strong> {b["time"]}</p>
            </div>
            '''
    return CabinetCode.replace("{{ bookings }}", booking_html)

@app.get("/main", response_class=HTMLResponse)
async def main(request: Request):
    MainCODE = """
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>MTS Booking</title>
    </head>
    <body>
    <h2>Добро пожаловать в MTS Booking</h2>
    </body>
    </html>
    """
    return MainCODE
2_5456559935850586404.py
Displaying 2_5456559935850586404.py.

После этого было отправленно нейросети на доработку 
.
