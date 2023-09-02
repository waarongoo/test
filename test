const warns = require("../../warns.js");
const { MessageEmbed } = require("discord.js");
const { config } = require("mathjs");
const db = require('quick.db')

module.exports = {
    config: {
  name: "warn",
  description: "Warn a user",
        aliases: ["strike"],
  category: "moderation",
  usage: "<User mention> <Reason>",
  permission: "KICK_MEMBERS",
    accessableby: "Moderators",
    },
  run: async (bot, message, args) => {
    if(args[0] == 'help') {
      let helpembed = new MessageEmbed()
      .setTitle('Warn command info') 
      .setDescription(`${module.exports.description}`)
      .addField('Usage', `!warn [@user] [reason]`)
      .addField('Category', `Moderation`)
      .addField('Permission', `${module.exports.permission}`)
      .setColor("RANDOM")
      return message.channel.send(helpembed)
      }
    let user = message.mentions.users.first();
    if (!message.member.hasPermission("KICK_MEMBERS"))
      return message.channel.send("*You don't have enough permissions!*");
    if (!user) return message.channel.send("You did not mention a user!");
    if (user.id === message.author.id)
      return message.reply(`Why warn yourself? Just Leave the server!`);
    if (user.id === message.guild.ownerID)
      return message.reply(
        "Let me tell you a secret: ||No one can warn the owner.||"
      );
    if (!args.slice(1).join(" "))
      return message.channel.send("You did not specify a reason!");

    warns.findOne(
      { Guild: message.guild.id, User: user.id },
      async (err, data) => {
        if (err) console.log(err);
        if (!data) {
          let newWarns = new warns({
            User: user.id,
            Guild: message.guild.id,
            Warns: [
              {
                Moderator: message.author.id,
                Reason: args.slice(1).join(" "),
              },
            ],
          });

          newWarns.save();
          message.channel.send(
            `${user.tag} has been warned with the reason of ${args
              .slice(1)
              .join(" ")}. They now have 1 warn.`
          );
        } else {
          data.Warns.unshift({
            Moderator: message.author.id,
            Reason: args.slice(1).join(" "),
          });
          data.save();
          message.channel.send(
            `${user.tag} has been warned with the reason of "${args
              .slice(1)
              .join(" ")}". They now have ${data.Warns.length} warns.`
          );
          let mwEmbed = new MessageEmbed()
            .setTitle("A member has been warned!")
            .setDescription(
              `Member warned: ${user}\nModerator: ${
                message.author.username
              }\nReason: ${args.slice(1).join(" ")}\nTotal warns of ${user}: ${
                data.Warns.length
              } warns`
            )
            .setColor(`#FF0000`);
          let currentDateTime = new Date();

          let dmEmbed = new MessageEmbed()
            .setTitle(`You've been warned in ${message.guild.name}`)
            .setDescription(
              `Moderator: ${message.author.username}\nReason: ${args
                .slice(1)
                .join(" ")}\nWarned at: ${currentDateTime.toLocaleDateString(
                "en-US"
              )}`
            )
            .setColor(`#FF0000`);

          let dmUser = message.guild.members.cache.find(
            (m) => m.id === user.id
          );
          dmUser.send(dmEmbed).then;
                    let mChannel = db.fetch(`modlog_${message.guild.id}`)
          if(!mChannel) return message.channel.send('This warn was not logged as mod-logs channel is not setup, make sure to set a channel as a mod-log channel using `setmodlogch` command.')
          let sChannel = message.guild.chanels.cache.get(mChannel)
          if(!sChannel) return;
          sChannel.send(mwEmbed)
        }
      }
    );
  },
};
