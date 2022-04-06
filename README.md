import telebot
import config
import random
import anekdots

from telebot import types

bot = telebot.TeleBot(config.TOKEN)
anekdots1 = anekdots.list_of_jokes[0]

@bot.message_handler(commands=['start'])
def welcome(message):
    sti = open('C:/Users/nius1/Desktop/PycharmProjects/pythonProject/Sti/ABC.webp', 'rb')
    bot.send_sticker(message.chat.id, sti)

    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
    item1 = types.KeyboardButton("Рандомное число")
    item2 = types.KeyboardButton("Как дела?")
    item3 = types.KeyboardButton("Ben")
    item4 = types.KeyboardButton("Анекдоты")
    markup.add(item1, item2, item3, item4)

    bot.send_message(message.chat.id, "Добро пожаловать, {0.first_name}!\nЯ - <b>{1.first_name}</b> ботяра".format(message.from_user, bot.get_me()), \
                     parse_mode='html', reply_markup=markup)

@bot.message_handler(content_types=['text'])
def lalala(message):
    BenWhat = open('C:/Users/nius1/Desktop/PycharmProjects/pythonProject/Sti/What.webp', 'rb')
    if message.chat.type == 'private':
        if message.text == 'Рандомное число':
            bot.send_message(message.chat.id, 'Введи от какого числа считать')
            bot.register_next_step_handler(message, get_num1)
        elif message.text == 'Ben':
            bot.send_sticker(message.chat.id, BenWhat)
            bot.send_message(message.chat.id, 'Что? (спроси у него что хочешь)')
            bot.register_next_step_handler(message, get_ben)
        elif message.text == 'Анекдоты':
            bot.send_message(message.chat.id, 'Введи любую цифру, чтобы прочитать анекдот:')
            bot.register_next_step_handler(message, get_anek)
        elif message.text == 'Как дела?':
            markup = types.InlineKeyboardMarkup(row_width=2)
            item1 = types.InlineKeyboardButton("Хорошо", callback_data='good')
            item2 = types.InlineKeyboardButton("Не очень", callback_data='bad')

            markup.add(item1, item2)

            bot.send_message(message.chat.id, 'Отлично, сам как?', reply_markup=markup)
        else:
            bot.send_message(message.chat.id, 'Я не знаю что ответить :(')
def get_anek(message):
    if message.text.lower() in '123456789':
        global anekdots1
        bot.send_message(message.chat.id, anekdots1)
    else:
        bot.send_message(message.chat.id, 'Не понял')
def get_ben(message):
    BenYes = open('C:/Users/nius1/Desktop/PycharmProjects/pythonProject/Sti/Yes.webp', 'rb')
    BenNo = open('C:/Users/nius1/Desktop/PycharmProjects/pythonProject/Sti/No.webp', 'rb')
    BenBee = open('C:/Users/nius1/Desktop/PycharmProjects/pythonProject/Sti/Bee.webp', 'rb')
    global ben
    ben = str(random.randint(1, 2))
    if ben == '1' and message.text[-1] == '?':
        bot.send_sticker(message.chat.id, BenYes)
        bot.send_message(message.chat.id, 'Да')
    elif ben == '2' and message.text[-1] == '?':
        bot.send_sticker(message.chat.id, BenNo)
        bot.send_message(message.chat.id, 'Нет')
    else:
        bot.send_sticker(message.chat.id, BenBee)
        bot.send_message(message.chat.id, 'Беее')
def get_num1(message):
    global num1
    try:
        num1 = int(message.text)
        bot.send_message(message.chat.id, 'Введи до какого числа считать')
        bot.register_next_step_handler(message, get_num2)
    except Exception:
        bot.send_message(message.chat.id, 'Нипон')
def get_num2(message):
    global num2
    try:
        num2 = int(message.text)
        bot.send_message(message.chat.id, str(random.randint(int(num1), int(num2))))
    except Exception:
        bot.send_message(message.chat.id, 'Нипон')
@bot.callback_query_handler(func=lambda call: True)
def callback_inline(call):
    try:
        if call.message:
            if call.data == 'good':
                bot.send_message(call.message.chat.id, 'Вот и отличненько!')
            elif call.data == 'bad':
                bot.send_message(call.message.chat.id, 'Бывает')

            # Удаляет кнопки
            bot.edit_message_text(chat_id=call.message.chat.id, message_id=call.message.message_id, text="Как дела?", \
                                  reply_markup=None)

            bot.answer_callback_query(callback_query_id=call.id, show_alert=False, text="Тут будут уведомления")
    except Exception as e:
        print(repr(e))

# RUN
bot.polling(none_stop=True)
