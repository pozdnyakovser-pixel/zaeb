import os
import logging
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, MessageHandler, filters, ContextTypes
import openai

# Настраиваем логирование
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)

# Переменные окружений для API ключей
OPENAI_API_KEY = os.getenv("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")
TELEGRAM_BOT_TOKEN = os.getenv("8321365949:AAH1NknBpNVp6Ur0A0aOerzaQRHlZoIZTF4")

# Устанавливаем ключ OpenAI
openai.api_key = OPENAI_API_KEY

# Промт для нашей модели
PROMPT_TEMPLATE = """
Ты помощник налогового консультанта Сергея и даёшь первичную консультацию по налоговым вопросам.
Будь внимателен и четок в ответах. Отвечай лаконично и уверенно.
"""

# Функционал бота
async def handle_message(update: Update, context: ContextTypes.DEFAULT_TYPE) -> None:
    # Получаем текст сообщения
    user_message = update.message.text
    
    # Формируем полноценный запрос к модели с учётом нашего промо-та
    full_prompt = PROMPT_TEMPLATE + '\\n\\nВопрос: ' + user_message + '\\nОтвет:'
    
    # Обращаемся к OpenAI API для получения ответа
    completion = openai.ChatCompletion.create(
      model="gpt-3.5-turbo",
      messages=[
          {"role": "system", "content": PROMPT_TEMPLATE},
          {"role": "user", "content": user_message}
      ]
    )
    
    # Извлекаем ответ
    assistant_reply = completion.choices[0].message.content
    
    # Отправляем ответ пользователю
    await update.message.reply_text(assistant_reply)

# Команда "/start"
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE) -> None:
    await update.message.reply_text("Привет! Я помощник налогового консультанта Сергея. Задавайте вопросы!")

# Главный метод запуска бота
def main() -> None:
    # Создаем экземпляр приложения
    application = ApplicationBuilder().token(TELEGRAM_BOT_TOKEN).build()
    
    # Регистрирование обработчика старта
    application.add_handler(CommandHandler("start", start))
    
    # Сообщения пользователя
    application.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_message))
    
    # Запускаем прослушивание сообщений
    application.run_polling()

if __name__ == "__main__":
    main()
