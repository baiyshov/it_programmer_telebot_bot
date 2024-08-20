import telebot
import webbrowser
import sqlite3
from telebot import types
from telebot import apihelper
import time
# Initialize the bot with the provided token
TOKEN = '7035769806:AAGNGGwRL3op3JI5dREBzIZu4eylEmoTT10'
bot = telebot.TeleBot(TOKEN)
name = None


apihelper.DEFAULT_TIMEOUT = 24
# Texts with information
python_texts = [
    "1. Установка Python\nСкачайте и установите Python с официального сайта (https://www.python.org/). Рекомендуется установить последнюю стабильную версию.",
    "2. Редактор кода:\nВыберите редактор кода. Для начала подойдет любой текстовый редактор, но рекомендуется использовать интегрированную среду разработки (IDE), такую как PyCharm, VSCode, или IDLE (входит в состав Python).",
    "3. Основы языка:\nИзучите основные концепции Python, такие как переменные, типы данных, операторы, условные операторы и циклы. Проходите базовые учебники и уроки. На сайте Python есть отличное руководство (https://docs.python.org/3/tutorial/).",
    "4. Структуры данных:\nИзучите основные структуры данных в Python: списки, кортежи, словари и множества. Попрактикуйтесь в их использовании, решая задачи.",
    "5. Функции и модули:\nПонимание функций и создание собственных функций. Изучите работу с модулями и библиотеками. Например, модуль math для математических вычислений.",
    "6. Обработка ошибок:\nПонимание обработки исключений и использование конструкции try-except.",
    "7. Работа с файлами:\nИзучите базовые операции с файлами, такие как чтение и запись.",
    "8. Практика:\nПишите простые программы для закрепления материала. Решайте задачи на алгоритмы и структуры данных на онлайн-платформах, таких как LeetCode, HackerRank или CodeSignal.",
    "9. Продвинутый уровень:\nПосле освоения основ можно переходить к изучению продвинутых тем, таких как ООП, работа с базами данных, веб-разработка и многое другое.",
    "10. Практика и проекты:\nНапишите свой первый небольшой проект, чтобы применить полученные знания на практике. Участвуйте в открытых инициативах или разработке с открытым исходным кодом."
]

cpp_texts = [
    "1. Знакомство с C++\n2. История и применение C++\nУстановка компилятора (например, GCC, Clang, Visual Studio)\n3. Первое приложение 'Hello, World!'",
    "2. Основы синтаксиса\nПеременные и типы данных\nВвод и вывод данных\nОсновные операторы (арифметические, логические, сравнительные)",
    "3. Управляющие структуры\nУсловные операторы (if, else, switch)\nЦиклы (for, while, do-while)\nИспользование break и continue",
    "4. Функции\nОбъявление и определение функций\nПередача параметров и возврат значений\nПерегрузка функций\nРекурсивные функции",
    "5. Массивы и строки\nОдномерные и многомерные массивы\nОсновные операции с массивами\nСтроки (char массивы) и работа с ними",
    "6. Указатели и динамическая память\nОсновы указателей\nОперации с указателями\nДинамическое выделение памяти (new, delete)\nМассивы указателей",
    "7. Структуры и объединения\nОпределение и использование структур\nВложенные структуры\nОбъединения и их применение",
    "8. Введение в ООП (Объектно-Ориентированное Программирование)\nКлассы и объекты\nКонструкторы и деструкторы\nНаследование и полиморфизм\nИнкапсуляция и области видимости",
    "9. Шаблоны и стандартная библиотека шаблонов (STL)\nОсновы шаблонов (функции и классы шаблонов)\nВведение в STL (векторы, списки, множества, карты)\nАлгоритмы STL",
    "10. Файловый ввод/вывод\nЧтение и запись в файлы\nРабота с потоками",
    "11. Обработка ошибок и исключений\nОсновы обработки исключений (try, catch, throw)\nСтандартные исключения C++\nПользовательские исключения",
    "12. Практические проекты\nПростейшие консольные игры (например, 'Крестики-нолики')\nКалькулятор\nРабота с файлами (например, записная книжка)",
    "13. Дополнительные ресурсы\nКниги и онлайн-курсы\nФорумы и сообщества разработчиков",
    "14. Дальнейшее развитие\nУглубленное изучение C++ (многопоточность, шаблоны, работа с сетью и т.д.)\nИзучение других языков программирования и технологий"
]

python_info = "\n\n".join(python_texts)
cpp_info = "\n\n".join(cpp_texts)


# Command handling to open a website
@bot.message_handler(commands=['site', 'website', 'сайт'])
def open_site(message):
    webbrowser.open('https://itproger.com')
    bot.send_message(message.chat.id, "Открываю сайт https://itproger.com")

@bot.message_handler(commands=['ютуб', 'youtube', 'vidio'])
def open_youtube(message):
    webbrowser.open('https://www.youtube.com/@IT_programmerbot')
    bot.send_message(message.chat.id, "Открываю YouTube канал www.youtube.com/@IT_programmerbot")

# Command handling for start and welcome message
@bot.message_handler(commands=['start'])
def start(message):
    conn = sqlite3.connect('itproger.sql')
    cur = conn.cursor()
    cur.execute('CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT, pass TEXT)')
    conn.commit()
    cur.close()
    conn.close()

    bot.send_message(message.chat.id, 'Привет! Сейчас тебя зарегистрируем. Введите ваше имя:')
    bot.register_next_step_handler(message, user_name)

def user_name(message):
    global name
    name = message.text.strip()
    bot.send_message(message.chat.id, 'Введите пароль:')
    bot.register_next_step_handler(message, user_pass)

def user_pass(message):
    password = message.text.strip()

    conn = sqlite3.connect('itproger.sql')
    cur = conn.cursor()
    cur.execute("INSERT INTO users (name, pass) VALUES (?, ?)", (name, password))
    conn.commit()
    cur.close()
    conn.close()

    markup = types.InlineKeyboardMarkup()
    markup.add(types.InlineKeyboardButton("Список Пользователей", callback_data='user'))
    bot.send_message(message.chat.id, 'Пользователь зарегистрирован!', reply_markup=markup)

    markup = types.ReplyKeyboardMarkup(resize_keyboard=True, row_width=2)  # Указываем row_width=2 для двух кнопок в ряду
    buttons_row1 = [
        'Перейти на сайт itproger.com',
        'Перейти на сайт обучение code-basics.com',
    ]
    buttons_row2 = [
        'Перейти на сайт игра codecademy.com',
        'Связаться с автором @bayanas_bayanas',
        'Перейти на сайт олимпиада https://olymp.krsu.edu.kg',
    ]
    buttons_row3 = [
        'Помощь / Help',
        'Получить информацию о Python',
    ]
    buttons_row4 = [
        'Получить информацию о C++',
        '',
        'Открыть YouTube канал www.youtube.com/@IT_programmerbot'
    ]
    markup.add(*[types.KeyboardButton(button) for button in buttons_row1])
    markup.add(*[types.KeyboardButton(button) for button in buttons_row2])
    markup.add(*[types.KeyboardButton(button) for button in buttons_row3])
    markup.add(*[types.KeyboardButton(button) for button in buttons_row4])

    bot.send_message(message.chat.id, f'Привет, {message.from_user.first_name} {message.from_user.last_name}!', reply_markup=markup)


@bot.callback_query_handler(func=lambda call: True)
def callback_inline(call):
    if call.message:
        if call.data == 'user':
            conn = sqlite3.connect('itproger.sql')
            cur = conn.cursor()
            cur.execute("SELECT * FROM users")
            users = cur.fetchall()
            conn.commit()
            cur.close()
            conn.close()

            info = '\n'.join([f"Имя: {user[1]}\tПароль: {user[2]}" for user in users])
            bot.send_message(call.message.chat.id, info)

# Handling text messages for providing information
@bot.message_handler(content_types=['text'])
def handle_text(message):
    if message.text == 'Получить информацию о Python':
        bot.send_message(message.chat.id, python_info)
    elif message.text == 'Получить информацию о C++':
        bot.send_message(message.chat.id, cpp_info)

# Run the bot
while True:
    try:
        bot.polling(none_stop=True, interval=1, timeout=60)
    except Exception as e:
        print(f"Ошибка: {e}")
        time.sleep(365) 
if __name__ == "__main__":
    bot.polling(none_stop=True)
    
