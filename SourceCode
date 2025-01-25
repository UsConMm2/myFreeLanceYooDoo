import logging
import openai
from telegram import Update
from telegram.ext import Application, CommandHandler, MessageHandler, filters, CallbackContext
from telegram.ext import CallbackQueryHandler
import traceback

# Настроим OpenAI API
openai.api_key = "YOUR_OPENAI_API_KEY"  # Замените на ваш API-ключ от OpenAI

# Настройка логирования
logging.basicConfig(
    format="%(asctime)s - %(name)s - %(levelname)s - %(message)s",
    level=logging.INFO
)
logger = logging.getLogger(__name__)

# Обработка команды /start
async def start(update: Update, context: CallbackContext) -> None:
    user = update.effective_user
    await update.message.reply_text(f"Привет, {user.mention_html()}! Я твой чат-бот, и я готов помочь тебе!")

# Обработка ошибок
def error(update: Update, context: CallbackContext) -> None:
    logger.error(f"Ошибка: {context.error}")
    try:
        raise context.error
    except Exception as e:
        logger.error("Трассировка ошибки: %s", traceback.format_exc())

# Генерация ответа от GPT
async def gpt_response(update: Update, context: CallbackContext) -> None:
    user_message = update.message.text  # Текст, отправленный пользователем
    try:
        # Запрос к OpenAI API для генерации ответа
        response = openai.Completion.create(
            engine="text-davinci-003",  # Используйте свой GPT-движок, например text-davinci-003
            prompt=user_message,
            max_tokens=150,
            temperature=0.7
        )
        bot_reply = response.choices[0].text.strip()
        await update.message.reply_text(bot_reply)  # Отправка ответа пользователю
    except openai.error.OpenAIError as e:
        logger.error("Ошибка OpenAI: %s", e)
        await update.message.reply_text("Извините, произошла ошибка при обработке запроса.")

# Основная функция для запуска бота
async def main() -> None:
    # Создаем объект Application с API-ключом Telegram
    api_key = "YOUR_TELEGRAM_API_KEY"  # Замените на ваш API-ключ Telegram
    application = Application.builder().token(api_key).build()

    # Регистрируем обработчики команд и сообщений
    application.add_handler(CommandHandler("start", start))
    application.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, gpt_response))

    # Регистрируем обработчик ошибок
    application.add_error_handler(error)

    # Запуск бота
    await application.run_polling()

if __name__ == "__main__":
    import asyncio
    asyncio.run(main())
