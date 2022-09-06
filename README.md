# anekdot_bot
# Бот создан в качестве тренировки для работы с Python


import random
import telebot
import requests
from bs4 import BeautifulSoup as b

URL = 'https://www.anekdot.ru/last/good'
API_KEY = '5675104214:AAH5jKOKYjCFKcRtV1v21bC00jRyKuhq4Bo'

def parser(url):
    r = requests.get(url)
    soup = b(r.text, 'html.parser')
    anekdots = soup.find_all('div', class_='text')
    return [c.text for c in anekdots]

list_of_jokes = parser(URL)
random.shuffle(list_of_jokes)

bot = telebot.TeleBot(API_KEY)

@bot.message_handler(commands = ['Начать'])
def hello(message):
    bot.send_message(message.chat.id, "Здравствуйте! Чтобы посмеяться введите любую цифру: ")

@bot.message_handler(content_types = ['text'])
def jokes(message):
    if message.text.lower() in '123456789':
        bot.send_message(message.chat.id, list_of_jokes[0])
        del list_of_jokes[0]
    else:
        bot.send_message(message.chat.id, "Введите любую цифру: ")

bot.polling()
