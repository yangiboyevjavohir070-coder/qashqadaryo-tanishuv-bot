import logging
from telegram import Update, InlineKeyboardMarkup, InlineKeyboardButton
from telegram.ext import (
    ApplicationBuilder, CommandHandler, MessageHandler,
    CallbackQueryHandler, ContextTypes, filters
)

# TOKEN — shu yerga o'zingizning tokeningizni yozing
TOKEN = "YOUR_TELEGRAM_BOT_TOKEN"

logging.basicConfig(
    format="%(asctime)s - %(name)s - %(levelname)s - %(message)s",
    level=logging.INFO
)

# Foydalanuvchilar bazasi (oddiy dictionary)
users = {}


async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_id = update.effective_user.id

    keyboard = [
        [InlineKeyboardButton("📝 Anketa yaratish", callback_data="create_profile")],
        [InlineKeyboardButton("❤️ Tanishuvni boshlash", callback_data="start_match")]
    ]

    await update.message.reply_text(
        "Salom! Qashqadaryo Tanishuv botiga xush kelibsiz!",
        reply_markup=InlineKeyboardMarkup(keyboard)
    )


async def button_click(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    await query.answer()

    user_id = query.from_user.id

    if query.data == "create_profile":
        users[user_id] = {"step": "name"}
        await query.edit_message_text("Ismingizni kiriting:")

    elif query.data == "start_match":
        await query.edit_message_text("👀 Sizga mos profillar qidirilmoqda...")
        msg = ""

        for uid, data in users.items():
            if uid != user_id and "name" in data:
                msg += f"👤 {data['name']}\n📍 {data.get('region', 'Nomaʼlum')}\n\n"

        if msg == "":
            msg = "Hozircha profillar yo'q 😕"
        
        await query
