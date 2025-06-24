import telebot
import openai
from elevenlabs import generate, save, set_api_key

# 🔑 API KEYS
TELEGRAM_TOKEN = "YOUR_TELEGRAM_BOT_TOKEN"
OPENAI_API_KEY = "YOUR_OPENAI_API_KEY"
ELEVENLABS_API_KEY = "YOUR_ELEVENLABS_API_KEY"

bot = telebot.TeleBot(TELEGRAM_TOKEN)
openai.api_key = OPENAI_API_KEY
set_api_key(ELEVENLABS_API_KEY)

def transcribe_audio(file_path):
    with open(file_path, "rb") as f:
        transcript = openai.Audio.transcribe("whisper-1", f)
    return transcript["text"]

@bot.message_handler(content_types=["voice"])
def handle_voice(message):
    file_info = bot.get_file(message.voice.file_id)
    voice_data = bot.download_file(file_info.file_path)

    with open("voice.ogg", "wb") as f:
        f.write(voice_data)

    text = transcribe_audio("voice.ogg")

    prompt = f"You are Anya, a cute anime girl who speaks sweetly. Be playful.\nUser: {text}\nAnya:"
    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[{"role": "user", "content": prompt}]
    )
    reply = response.choices[0].message.content

    audio = generate(text=reply, voice="Rachel", model="eleven_monolingual_v1")
    save(audio, "reply.mp3")

    with open("reply.mp3", "rb") as a:
        bot.send_voice(message.chat.id, a)

bot.polling()
