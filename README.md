# Botnim

> Nim client package for Botway.

## Installation

```bash
nimble install botnim
```

## Usage

> this is an example of botway discord nim template

```nim
import dimscord, asyncdispatch, times, options
import botnim

let discord = newDiscordClient(GetToken())

# Handle event for on_ready.
proc onReady(s: Shard, r: Ready) {.event(discord).} =
    echo "Ready as " & $r.user

# Handle event for message_create.
proc messageCreate(s: Shard, m: Message) {.event(discord).} =
    if m.author.bot: return
    if m.content == "!ping": # If message content is "!ping".
        let
            before = epochTime() * 1000
            msg = await discord.api.sendMessage(m.channel_id, "ping?")
            after = epochTime() * 1000
        # Now edit the message.
        # Use 'discard' because editMessage returns a new message.
        discard await discord.api.editMessage(
            m.channel_id,
            msg.id, 
            "Pong! took " & $int(after - before) & "ms | " & $s.latency() & "ms."
        )
    elif m.content == "!embed": # Otherwise if message content is "!embed".
        # Sends a message with embed.
        discard await discord.api.sendMessage(
            m.channel_id,
            embeds = some @[Embed(
                title: some "Hello there!", 
                description: some "This is description",
                color: some 0x7789ec
            )]
        )

# Connect to Discord and run the bot.
waitFor discord.startSession()
```
