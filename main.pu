import discord
from discord.ext import commands
import wavelink

intents = discord.Intents.default()
intents.message_content = True
bot = commands.Bot(command_prefix="!", intents=intents)

# Connect Lavalink node
@bot.event
async def on_ready():
    print(f'Logged in as {bot.user}')
    await wavelink.NodePool.create_node(
        bot=bot,
        host='localhost',
        port=2333,
        password='youshallnotpass',
        region='us_central'
    )

# Join command
@bot.command()
async def join(ctx):
    if ctx.author.voice:
        channel = ctx.author.voice.channel
        player = await channel.connect(cls=wavelink.Player)
        await ctx.send(f"Connected to {channel.name}")
    else:
        await ctx.send("You are not connected to a voice channel.")

# Play command
@bot.command()
async def play(ctx, *, search: str):
    player: wavelink.Player = ctx.voice_client

    if not player:
        await ctx.invoke(bot.get_command('join'))
        player = ctx.voice_client

    query = f"ytsearch:{search}"
    tracks = await wavelink.YouTubeTrack.search(query)
    if not tracks:
        await ctx.send("No tracks found.")
        return

    track = tracks[0]
    await player.play(track)
    await ctx.send(f"Now playing: {track.title}")

# Pause command
@bot.command()
async def pause(ctx):
    player: wavelink.Player = ctx.voice_client
    if player.is_playing():
        await player.pause()
        await ctx.send("Playback paused.")

# Resume command
@bot.command()
async def resume(ctx):
    player: wavelink.Player = ctx.voice_client
    if player.is_paused():
        await player.resume()
        await ctx.send("Playback resumed.")

# Stop command
@bot.command()
async def stop(ctx):
    player: wavelink.Player = ctx.voice_client
    await player.stop()
    await ctx.send("Playback stopped.")

# Leave command
@bot.command()
async def leave(ctx):
    player: wavelink.Player = ctx.voice_client
    await player.disconnect()
    await ctx.send("Disconnected from voice channel.")

bot.run("YOUR_BOT_TOKEN")
