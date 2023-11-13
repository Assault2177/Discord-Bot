import discord
from discord.ext import commands

intents = discord.Intents.all()
bot = commands.Bot(command_prefix='!', intents=intents)

@bot.event
async def on_ready():
    print(f'Logged in as {bot.user.name}')

@bot.command()
async def create_scrim(ctx, team_name, size, date, time):
    # You can add more details as needed
    message = f"(Team) - {team_name}\n{size}\nNeed ref"

    # Create an embed with a button
    embed = discord.Embed(title="Scrim Information", description=message)
    view = discord.ui.View()
    view.add_item(discord.ui.Button(style=discord.ButtonStyle.green, label="Assign Ref", custom_id="assign_ref"))

    # Send the message with the embed and button
    await ctx.send(embed=embed, view=view)

@bot.event
async def on_button_click(interaction):
    if interaction.custom_id == "assign_ref":
        # Assuming you have a role named 'Referee'
        referee_role = discord.utils.get(interaction.guild.roles, name='Referee')

        # Assuming you want to notify all referees
        referees = [member for member in interaction.guild.members if referee_role in member.roles]

        # Send a DM to each referee with the scrim information
        for referee in referees:
            await referee.send(f"You've been assigned to a scrim!\n{interaction.message.embeds[0].description}")

# Replace 'YOUR_BOT_TOKEN' with your actual bot token
bot.run("YOUR_BOT_TOKEN")
