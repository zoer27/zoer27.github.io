---
layout: post
title: Whale-y messages from R
subtitle: How to use `telegram` and `ohwhaley` to send whale-themed messages throughout your R code
author: Zoe Rand
---

---
output: 
  html_document:
    keep_md: true
---

Do you have code that takes a long time to run? Do you want to go do other things and then just get a helpful message when your code is finished, or send messages as progress updates?

Do you want this helpful message to come in the form of a whale pun? Then read on.

## Set up Telegram

First you will need to download the Telegram app on your phone or open it in a web browser. Then, set up your bot using the [botfather](https://telegram.me/botfather). Make sure you give your bot a memorable name!

The botfather will return a html token, copy this and save it somewhere because you will need it for the next steps.

You will also need to install and load the [telegram package](https://cran.r-project.org/web/packages/telegram/index.html) for R.


``` r
install.packages("telegram")
library(telegram)
```

Next, create a .Renviron file in your current project directory.

``` bash
touch .Renviron
```

Open this file and add your token from the botfather. The name of your bot should follow the `R_TELEGRAM_BOT_` prefix. This name is also how you will refer to you bot in R code. For our purposes, I've named my bot `ZRRTestBot`.

```         
R_TELEGRAM_BOT_ZRRTestBot=yourtokenhere
```

Finally, you will need to **restart R** in order for the environment variables to load.

## Set up telegram bot in R

Load the telegram library:


``` r
library(telegram)
```

Then, create the bot object with the `TGBot` function. I am just going to call it `bot` here, but you can call it whatever you'd like.


``` r
bot<-TGBot$new(token = bot_token("ZRRTestBot")) #make sure this name matches the name you set above in the .Renviron file
```

Check the bot connection. If this matches the name of the bot you set up with the botfather then your setup is correct!


``` r
bot$getMe()
```

```
## Bot name:	Test bot
## Bot username:	ZRRTestBot
```

Going back to the botfather, open the link to your bot. Send your bot a message (any message, it doesn't matter what it says).

Check your messages in R:


``` r
bot$getUpdates()
```

```
##   update_id message.message_id message.from.id message.from.is_bot
## 1 305531594                  1      7281464972               FALSE
## 2 305531595                  2      7281464972               FALSE
##   message.from.first_name message.from.language_code message.chat.id
## 1                     Zoe                         en      7281464972
## 2                     Zoe                         en      7281464972
##   message.chat.first_name message.chat.type message.date message.text
## 1                     Zoe           private   1750185120       /start
## 2                     Zoe           private   1750185172           Hi
##    message.entities
## 1 0, 6, bot_command
## 2              NULL
```

``` r
#see the message you sent
bot$getUpdates()$message$text
```

```
## [1] "/start"
```

``` r
#see the chat ID which you will need to save
bot$getUpdates()$message$chat$id
```

```
## [1] 7281464972
```

Write the chat-ID into your script, since this will not change.


``` r
bot$set_default_chat_id(7281464972)
```

Now you are ready to send messages in R!

## Send messages to your bot

When you want to send messages from your R script, simply include the following lines of code:


``` r
library(telegram)
bot<-TGBot$new(token = bot_token("ZRRTestBot")) #make sure this name matches the name you set above in the .Renviron file
bot$set_default_chat_id(7281464972)
```

Then, throughout your script you can send messages using the `sendMessage()` function. The easiest messages to send are text messages:


``` r
bot$sendMessage("hello world")
```

Intersperse these messages throughout your long-running code, or after code has finished to get updates as your code runs.

But we don't want just any boring updates...

## Use ohwhaley to send fun whale-themed messages

<img src="../asseimgfile.png" width="20%" style="display: block; margin: auto;" />

[ohwhaley](https://fontikar.github.io/ohwhaley/) is an R package developed by [Fonti Kar](https://fontikar.github.io/) that will print whale-themed messages using the function `say()`

Install `ohwhaley`:


``` r
# install.packages("remotes")
remotes::install_github("fontikar/ohwhaley")
```

Get a fun whale-themed message:


``` r
library(ohwhaley)
say()
```

```
## 
##             ------ 
##            Gee...this is over-whalming! 
##             ------ 
##                \   
##                 \  
##                  \
##      .-'
## '--./ /     _.---.
## '-,  (__..-`       \
##    \          .     |
##     `,.__.   ,__.--/
##      '._/_.'___.-`
```

To send this message via Telegram you will need to save the output using `capture.output()`. You will also need to collapse it into a single string using `paste()`. `collapse = \n` keeps the line spacing correct so you can still get a whale image in your message. Note that the collapsed message will look weird in your R console but will send nicely to your bot.


``` r
#get say() output
output<-capture.output(say(), type = "message")

#collapse into a single string
output_str<-paste(ouput, collapse = "\n")

#send as message to your bot
bot$sendMessage(output_str)
```

If you want to send a specific message (perhaps about where you are in your code) but you want this to still come with an image of a cute whale, simply add your own text to `say()`:


``` r
say("Simulation 1 complete")
```

```
## 
##             ------ 
##            Simulation 1 complete 
##             ------ 
##                \   
##                 \  
##                  \
##      .-'
## '--./ /     _.---.
## '-,  (__..-`       \
##    \          .     |
##     `,.__.   ,__.--/
##      '._/_.'___.-`
```

Then, follow the same steps as above to send this to your bot:


``` r
#custom say() output
output<-capture.output(say("Simulation 1 complete"), type = "message")

#collapse into a single string
output_str<-paste(output, collapse = "\n")

#send as message to your bot
bot$sendMessage(output_str)
```

I hope this tip brings you joy as you run those interminable models! Not into whales? Try this with other animals using the [cowsay package](https://github.com/sckott/cowsay).
