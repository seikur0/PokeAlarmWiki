## Overview
* [Prerequisities](#prerequisities)
* [Introduction](#introduction)
* [Basic Config](#basic-config)
  * [Required Parameters](#required-parameters)
  * [Example: Basic Alarm Configuration using Required Parameters](#example-basic-alarm-configuration-using-required-parameters)
* [Advanced Config](#advanced-config)
  * [Optional Parameters](#optional-parameters)
  * [Example: Alarm Configuration Using Optional Parameters](#example-alarm-configuration-using-optional-parameters)
* [How to Get a Telegram API Key](#how-to-get-a-telegram-api-key)
* [How to Create a Custom Channel](#how-to-create-a-custom-channel)

## Prerequisites
This guide assumes: 

1. You are familiar with [JSON formatting](http://www.w3schools.com/json/default.asp)
2. You have read and understood the [Alarm Configuration](https://github.com/kvangent/PokeAlarm/wiki/Alarm-Configuration) Wiki
3. You are comfortable with the layout of `alarms.json`.

Please familiarize yourself with all of the above before proceeding.

## Introduction

![](images/telegram.png)

**Telegram** is a cloud-based instant messaging service. Telegram clients exist for both mobile (Android, iOS, Windows Phone, Ubuntu Touch) and desktop systems (Windows, OS X, Linux). Users can send messages and exchange photos, videos, stickers and files of any type.

PokeAlarm offers the following for Telegram:

* Notifications to multiple Telegram channels
* Customizable Google Map image of the pokemon, gym, and/or pokestop location
* Personalized notifications via [Dynamic Text Substitution](Dynamic-Text-Subsitution.md)

## Basic Config

### Required Parameters

These `alarm.json` parameters are required to enable the Telegram alarm service:

| Parameters     | Description                            |
| :------------- |:---------------------------------------|
| `type`         | must be `telegram`                     |
| `active`       | `True` for alarm to be active          |
| `bot_token`    | Your Bot Token from Telegram           |
| `chat_id`      | Your chat's id from Telegram           |

### Example: Basic Alarm Configuration using Required Parameters
Below is how a basic Telegram alarm configuration would appear in `alarms.json`.  Note that this is **not** the entire `alarms.json`, but only the section pertaining to the alarm portion of the JSON file.
```json
{
	"active": "False",
	"type":"telegram",
	"bot_token":"YOUR_BOT_TOKEN",
	"chat_id":"YOUR_CHAT_ID"
}
```

## Advanced Config

### Optional Parameters
In addition to the required parameters, several `alarm.json` optional parameters are available to personalize your notifications.  Below is an example of these optional parameters and how they are incorporated into a functional alarm layout.


Parameters at the alarm level will be the default to alert-level parameters.

| Parameters         | Description                                                | Default                      |
|:-------------------|:-----------------------------------------------------------|:-----------------------------|
| `location`         | Sends minimap after main message.                                        | `True`            |
| `disable_map_notification`	| Disables map notifications.  Set to `False` if you are experiencing notification issues on Android                | `True`                       |
| `venue`	| Sends location in main message.              | `False`                       |
| `startup_message`  | Confirmation post when PokeAlarm initialized               | `True`                       |
| `startup_list`     | First post will list all alarmed pokemon enabled in `alarms.json`    | `True`            |

These optional parameters below are applicable to the `pokemon`, `pokestop`, and `gym` sections of the JSON file. These parameters overide the alarm-level settings for this alert.

| Parameters | Description                                      | Default													|
| -----------|:-------------------------------------------------|:----------------------------------------------------------|
| `title`    | Header text for the message						| `A wild <pkmn> has appeared!`								|
| `body`     | Additional text to be added to the message		| `"<gmaps> \n Available until <24h_time> (<time_left>)."`	| 
| `location`         | Sends minimap after main message.                                        | `True`            |
| `disable_map_notification`	| Disables map notifications.  Set to `False` if you are experiencing notification issues on Android                | `True`                       |
| `venue`	| Sends location in main message.              | `False`                       | 

### Example: Alarm Configuration Using Optional Parameters
Below is how an advanced alarm configuration would appear in `alarms.json`. Note that this is **not** the entire `alarms.json`, but only the section pertaining to the alarm portion of the JSON file.
```json
{
	"active": "False",
	"type":"telegram",
	"bot_token":"YOUR_BOT_TOKEN",
	"chat_id":"YOUR_CHAT_ID",
	"startup_message:":"True",
	"startup_list:":"True",
	"pokemon" : {
		"chat_id": "OVERRIDES_DEFAULT_CHANNEL",
		"title": "A wild <pkmn> has appeared!",
		"body": "<gmaps> \n Available until <24h_time> (<time_left>).",
		"location": "True"
	},
	"pokestop":{
		"chat_id": "OVERRIDES_DEFAULT_CHANNEL",
		"title":"Someone has placed a lure on a Pokestop!",
		"body": "<gmaps> \n Lure will expire at <24h_time> (<time_left>).",
		"location": "True"
	},
	"gym":{
		"chat_id": "OVERRIDES_DEFAULT_CHANNEL",
		"title":"A Team <old_team> gym has fallen!",
		"body": "<gmaps> \n It is now controlled by <new_team>.",
		"location": "True"
	}
}
```


## How to get a Telegram API Key

1. Go to [Telegram Web Client](https://telegram.org/dl/webogram). Enter your phone number and follow the instructions to create your account. 

2. Talk to the [BotFather](https://telegram.me/botfather) to create a new bot. Use the `/newbot`command and follow his instructions. It will give you an API Token when you are finished.

3. Start a conversation with your bot. In the top left click on the menu bars, then click create group. Type in the name of the bot you previously created, then click on it when it appears below. Then click next. Type any message to your bot. 

4. Enter your bot_token in to replace the `<BOT_TOKEN_HERE>` in the following url `https://api.telegram.org/bot<BOT_TOKEN_HERE>/getUpdates`. Then go to it, and find the section that says `"chat":{"id":<CHAT_ID>`. This number is your chat_id. 


## How to Create a Custom Channel

1. Create a Public Channel (Currently doesn't work from the web app).

2. Add your bot as an administrator for the channel.

3. The chat_id for your bot will be `@channel_url`.