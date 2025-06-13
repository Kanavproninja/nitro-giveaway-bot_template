import discord
from discord.ext import commands
import random
import asyncio
import os

intents = discord.Intents.default()
intents.message_content = True

bot = commands.Bot(command_prefix="!", intents=intents)

@bot.event
async def on_ready():
    print(f"✅ Logged in as {bot.user}")

@bot.command()
@commands.has_permissions(manage_guild=True)
async def giveaway(ctx, duration: int, *, prize: str):
    await ctx.send(f"🎉 **GIVEAWAY STARTED** 🎉\nPrize: **{prize}**\nReact with 🎉 to enter!\nEnds in **{duration} seconds**.")
    message = await ctx.send("🎉 Giveaway Entry Message 🎉")
    await message.add_reaction("🎉")
    await asyncio.sleep(duration)
    message = await ctx.channel.fetch_message(message.id)
    users = await message.reactions[0].users().flatten()
    users = [user for user in users if not user.bot]
    if users:
        winner = random.choice(users)
        await ctx.send(f"🥳 Congratulations {winner.mention}, you won **{prize}**!")
    else:
        await ctx.send("❌ No one entered the giveaway.")

bot.run(os.getenv("TOKEN"))
