# Mocy-Giveaways

Advance discord giveaways system with Support Slash/Message support

# Download

```cli
npm i git+https://github.com/JohnDavid30/mocy-giveaways
------ or ---------------------
yarn add git+https://github.com/JohnDavid30/mocy-giveaways
```

# Setting up

### Launch of the module

Required Discord Intents: `Guilds` and `GuildMessageReactions`.  
Optional Discord Privileged Intent for better performance: `GuildMembers`.

```js
const Discord = require('discord.js');
const client = new Discord.Client({
    intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.GuildMembers,
    GatewayIntentBits.MessageContent,
  ],
});

// Requires Manager from mocy-giveaways
// for custom embed
// class CustomManager extends GiveawaySystem {
//   GiveawayStartEmbed(giveaway) {
//     let embed = new EmbedBuilder().setTitle(`Giveway Started`);
//     return embed;
//   }
//   GiveawayEndNoWinnerEmbed(giveaway) {
//     let embed = new EmbedBuilder().setTitle(`Giveway Ended No Winner`);
//     return embed;
//   }
//   GiveawayEndWinnerEmbed(giveaway) {
//     let embed = new EmbedBuilder().setTitle(`Giveway Ended Winners`);
//     return embed;
//   }
// }

// const manager = new CustomManager(client, {
//   embedColor: "Random",
//   pingEveryone: true,
// });

const manager = new GiveawaySystem(client, {
  embedColor: "Random",
  pingEveryone: true,
});
// We now have a giveawaysManager property to access the manager everywhere!
client.giveawaysManager = manager;

client.on('ready', () => {
    console.log('Bot is ready!');
});

client.login(process.env.DISCORD_BOT_TOKEN);
```
After that, giveaways that are not yet completed will start to be updated again and new giveaways can be started.
You can pass an options object to customize the giveaways. Here is a list of them:

-   **client**: the discord client (your discord bot instance).
### Start a giveaway

```js
client.on('interactionCreate', (interaction) => {

    if (interaction.isChatInputCommand() && interaction.commandName === 'start') {
        // /start 2d 1 Awesome prize!
        // Will create a giveaway with a duration of two days, with one winner and the prize will be "Awesome prize!"

        const duration = interaction.options.getString('duration');
        const winnerCount = interaction.options.getInteger('winners');
        const prize = interaction.options.getString('prize');

        client.giveawaysManager
            .start(interaction, {
              channel: channel,
              duration: duration,
              prize: prize,
              winnerCount: winnerCount,
              hostedby: interaction.member.id
            })
            .then((data) => {
                console.log(data); // {...} (messageId, end date and more)
            });
        // And the giveaway has started!
    }
});
```

-   **options.duration**: the giveaway duration.
-   **options.prize**: the giveaway prize.
-   **options.winnerCount**: the number of giveaway winners.
-   **options.channel**: the giveaway channel.
-   **options.hostedby**: the owner hosting of giveaway.

This allows you to start a new giveaway. Once the `start()` function is called, the giveaway starts, and you only have to observe the result, the package does the rest!

<a href="https://zupimages.net/viewer.php?id=23/01/9hna.jpg">
    <img src="https://zupimages.net/up/23/01/9hna.jpg"/>
</a>
<a href="https://zupimages.net/viewer.php?id=23/01/dxlv.jpg">
    <img src="https://zupimages.net/up/23/01/dxlv.jpg"/>
</a>

#### ⚠ ATTENTION!

The command examples below (reroll, edit, delete, end) can be executed on any server your bot is a member of if a person has the `prize` or the `messageId` of a giveaway. To prevent abuse we recommend to check if the `prize` or the `messageId` that was provided by the command user is for a giveaway on the same server, if it is not, then cancel the command execution.
### Reroll a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isChatInputCommand() && interaction.commandName === 'reroll') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager
            .reroll(messageId)
            .then(() => {
                interaction.reply('Success! Giveaway rerolled!');
            })
            .catch((err) => {
                interaction.reply(`An error has occurred, please check and try again.\n\`${err}\``);
            });
    }
});
```
### Edit a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isChatInputCommand() && interaction.commandName === 'edit') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager
            .edit(messageId, {
                winCount: 3,
                prize: 'New Prize!'
            })
            .then(() => {
                interaction.reply('Success! Giveaway updated!');
            })
            .catch((err) => {
                interaction.reply(`An error has occurred, please check and try again.\n\`${err}\``);
            });
    }
});
```

-   **options.winCount**: the new number of winners.
-   **options.prize**: the new prize.
### Delete a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isChatInputCommand() && interaction.commandName === 'delete') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager
            .delete(messageId)
            .then(() => {
                interaction.reply('Success! Giveaway deleted!');
            })
            .catch((err) => {
                interaction.reply(`An error has occurred, please check and try again.\n\`${err}\``);
            });
    }
});
```

-   **doNotDeleteMessage**: whether the giveaway message shouldn't be deleted.

⚠️ **Note**: when you use the delete function, the giveaway data and the message of the giveaway are deleted (by default). You cannot restore a giveaway once you have deleted it!

### End a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isChatInputCommand() && interaction.commandName === 'end') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager
            .end(messageId)
            .then(() => {
                interaction.reply('Success! Giveaway ended!');
            })
            .catch((err) => {
                interaction.reply(`An error has occurred, please check and try again.\n\`${err}\``);
            });
    }
});
```

-   **noWinnerMessage**: Sent in the channel if there is no valid winner for the giveaway.
