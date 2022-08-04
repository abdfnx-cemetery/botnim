# Botnim

> Nim client package for Botway.

## Installation

```bash
nimble install botnim
```

## Usage

> this is an example of botway discord nim template

```nim
import dimscord, asyncdispatch, strutils, options
import botway

let discord = newDiscordClient(GetToken())

proc messageCreate(s: Shard, m: Message) {.event(discord).} =
  let args = m.content.split(" ")

  if m.author.bot or not args[0].startsWith("/"): return

  let command = args[0][1..args[0].high]

  case command.toLowerAscii():
    of "test":
      discard await discord.api.sendMessage(m.channel_id, "Success!")

    of "facepalm":
      discard await discord.api.sendMessage(m.channel_id, "smh",
        files = @[DiscordFile(
          name: "assets/facepalm.png"
        )]
      )

    of "help":
      discard await discord.api.sendMessage(
        m.channel_id,
        "`test, echo, facepalm` are the commands."
      )

    of "echo":
      var text = args[1..args.high].join(" ")
      if text == "":
        text = "Empty text."

      discard await discord.api.sendMessage(m.channel_id, text)

    else:
      discard

proc onReady(s: Shard, r: Ready) {.event(discord).} =
  echo "Ready as: " & $r.user

  await s.updateStatus(activity = some ActivityStatus(
    name: "around.",
    kind: atPlaying
  ), status = "idle")

proc messageDelete(s: Shard, m: Message, exists: bool) {.event(discord).} =
  echo "A wild message has been deleted!"

waitFor discord.startSession(
  gateway_intents = {giGuildMessages, giGuilds, giGuildMembers}
)
```
