# Discord.py Components v2 (Cv2) – Guide

This guide explains the new Cv2 UI system in discord.py, which lets you build modern, interactive Discord bot interfaces with less code and more control.

---

## 🆕 The 7 New Cv2 Features

### 1. `TextDisplay`

- Static text inside a view.

```py
discord.ui.TextDisplay(content="📜 Welcome adventurer!")
```

🔹 **New in Cv2** → Previously text had to be sent outside of the view.

---

### 2. `Separator`

- Adds visual spacing or dividers.

```py
discord.ui.Separator(visible=True, spacing=discord.SeparatorSpacing.medium)
```

🔹 **New in Cv2** → No Cv1 equivalent.

---

### 3. `Container`

- Groups related UI elements.

```py
discord.ui.Container(
    discord.ui.TextDisplay(content="Choose wisely..."),
    discord.ui.ActionRow(
        discord.ui.Button(label="Hero 🦸", style=discord.ButtonStyle.success, custom_id="hero"),
        discord.ui.Button(label="Villain 🦹", style=discord.ButtonStyle.danger, custom_id="villain"),
    ),
)
```

🔹 **New in Cv2** → Keeps layouts modular and clean.

---

### 4. `Buttons`

- Same functionality, cleaner placement in rows.

```py
discord.ui.Button(style=discord.ButtonStyle.primary, label="Continue ➡️", custom_id="continue")
```

✅ **Update** → No `view.add_item()` required.

---

### 5. `Select Menus`

- Inline dropdowns.

```py
discord.ui.Select(
    custom_id="pets",
    options=[
        discord.SelectOption(label="Dragon 🐉", value="dragon"),
        discord.SelectOption(label="Phoenix 🔥", value="phoenix"),
    ],
)
```

✅ **Update** → Same options, but structured inside containers.

---

### 6. `MediaGallery`

- Displays multiple images/videos in a carousel.

```py
discord.ui.MediaGallery(
    discord.MediaGalleryItem(media="attachment://forest.png"),
    discord.MediaGalleryItem(media="attachment://castle.png"),
)
```

🆕 **New in Cv2** → Great for previews, showcases, or galleries.

---

### 7. `File`

- Attach files directly with a view.

```py
discord.ui.File(media="attachment://guide.pdf")
```

🆕 **New in Cv2** → Send assets, configs, documents alongside components.

---

## Common Cv2 Patterns

### Buttons + Selects

```python
class MyView(discord.ui.LayoutView):
    ui = discord.ui.Container(
        discord.ui.TextDisplay(content="🌐 Cv2 Example"),
        discord.ui.ActionRow(
            discord.ui.Button(label="Approve 👍", style=discord.ButtonStyle.success, custom_id="approve"),
            discord.ui.Button(label="Reject 👎", style=discord.ButtonStyle.danger, custom_id="reject"),
        ),
        discord.ui.ActionRow(
            discord.ui.Select(
                custom_id="select_demo",
                options=[
                    discord.SelectOption(label="Path A", value="a"),
                    discord.SelectOption(label="Path B", value="b"),
                ],
            )
        ),
    )
```

---

## Troubleshooting

- **Child limit error:** Only 40 UI elements per view/container. Combine text/buttons into fewer items.
- **Buttons not working:** Assign an async callback to each button if you use them.
- **UI not updating:** Edit the message with `await interaction.response.edit_message(view=self)` after changing the view.

---

## Minimal Working Example (Single File)

```python
import discord
from discord.ext import commands
from discord.ui import LayoutView, Container, TextDisplay, Separator, Button, ActionRow

intents = discord.Intents.default()
intents.message_content = True
bot = commands.Bot(command_prefix="!", intents=intents)

class MyView(LayoutView):
    def __init__(self, latency):
        super().__init__(timeout=30)
        self.add_item(Container(
            TextDisplay(content=f"Pong! Latency: `{latency}ms`"),
            Separator(visible=True),
            ActionRow(Button(label="OK", style=discord.ButtonStyle.success, disabled=True))
        ))

@bot.command()
async def ping(ctx):
    latency = round(ctx.bot.latency * 1000)
    view = MyView(latency)
    await ctx.send(view=view)

bot.run("YOUR_TOKEN")
```

---

**Best Practices:**

- Use `LayoutView` and `Container` for all UI layouts.
- Always use async callbacks for buttons/selects if you want interactivity.
- Use `await ctx.send(view=view)` or `await ctx.reply(view=view)` to send Cv2 UIs.
- You can use this pattern in any command, event, or cog.

---

## Advanced Patterns

### Multi-Row Layout

```python
class MultiRowView(LayoutView):
    def __init__(self):
        super().__init__(timeout=60)
        self.add_item(Container(
            TextDisplay(content="Choose below:"),
            Separator(visible=True),
            ActionRow(
                Button(label="One", style=discord.ButtonStyle.primary),
                Button(label="Two", style=discord.ButtonStyle.secondary),
            ),
            ActionRow(
                Button(label="Three", style=discord.ButtonStyle.success),
            ),
        ))
```

### Button Callback Example

```python
class CallbackView(LayoutView):
    def __init__(self):
        super().__init__(timeout=30)
        btn = Button(label="Click Me", style=discord.ButtonStyle.primary)
        btn.callback = self.on_click
        self.add_item(Container(btn))
    async def on_click(self, interaction):
        await interaction.response.send_message("Button clicked!", ephemeral=True)
```

---

## FAQ

**Q: Why do I get 'maximum number of children exceeded (40)'?**
A: You can only have 40 UI elements per view/container. Combine text/buttons into fewer items.

**Q: Why do my buttons not work?**
A: Make sure you assign an async callback to each button: `btn.callback = self.on_click`.

**Q: How do I update the UI after a button is pressed?**
A: Change the view state, then call `await interaction.response.edit_message(view=self)`.

**Q: Can I use Cv2 in both commands and cogs?**
A: Yes! The pattern is the same everywhere.

---
