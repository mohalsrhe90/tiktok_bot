from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import ApplicationBuilder, MessageHandler, filters, CallbackQueryHandler, ContextTypes
import yt_dlp
import os

BOT_TOKEN = "8315858804:AAFuui29VSZIg4KJNVv3nmaCO-XeFiOSfeU"
CHANNEL_ID = "@tyaf90"

# التحقق من الاشتراك في القناة
async def check_membership(user_id, context):
    try:
        member = await context.bot.get_chat_member(CHANNEL_ID, user_id)
        return member.status in ['member', 'administrator', 'creator']
    except:
        return False

# عند استلام رسالة نصية
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_id = update.effective_user.id
    if not await check_membership(user_id, context):
        keyboard = [
            [InlineKeyboardButton("📢 الاشتراك في القناة", url="https://t.me/tyaf90")],
            [InlineKeyboardButton("✅ تحقق من الاشتراك", callback_data="check_sub")]
        ]
        reply_markup = InlineKeyboardMarkup(keyboard)
        await update.message.reply_text("🔒 لا يمكنك استخدام البوت حتى تشترك في القناة:", reply_markup=reply_markup)
        return

    await download_video(update, context)

# التحقق عند الضغط على زر "تحقق من الاشتراك"
async def button_callback(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    await query.answer()
    user_id = query.from_user.id

    if await check_membership(user_id, context):
        await query.edit_message_text("✅ تم التحقق! أرسل رابط TikTok أو Facebook الآن.")
    else:
        await query.edit_message_text("❌ لم يتم الاشتراك بعد. تأكد من الاشتراك ثم اضغط تحقق.")

# تحميل الفيديو
async def download_video(update: Update, context: ContextTypes.DEFAULT_TYPE):
    url = update.message.text
    chat_id = update.message.chat_id

    if "tiktok.com" in url or "facebook.com" in url:
        ydl_opts = {
            'outtmpl': 'video.%(ext)s',
            'format': 'mp4',
            'noplaylist': True
        }
        try:
            with yt_dlp.YoutubeDL(ydl_opts) as ydl:
                ydl.download([url])
            await context.bot.send_chat_action(chat_id=chat_id, action="upload_video")
            await context.bot.send_video(chat_id=chat_id, video=open("video.mp4", "rb"))
            os.remove("video.mp4")
        except Exception as e:
            await update.message.reply_text(f"❌ حدث خطأ: {e}")
    else:
        await update.message.reply_text("📎 أرسل رابط من TikTok أو Facebook فقط.")

# بدء تشغيل البوت
if __name__ == "__main__":
    import logging
    logging.basicConfig(level=logging.INFO)

    app = ApplicationBuilder().token(BOT_TOKEN).build()
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, start))
    app.add_handler(CallbackQueryHandler(button_callback))

    print("🤖 Bot is running...")
    app.run_polling()
