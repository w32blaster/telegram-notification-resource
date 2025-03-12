# telegram-notification-resource
Resource for Concourse CI to send messages to Telegram. You can pass your message from tasks with text file, or send a static message. 

![Telegram Botfather](https://core.telegram.org/file/811140763/1/PihKNbjT8UE/03b57814e13713da37)

It allows you to send (put) a messages to Telegram from your pipeline.

## Prepare your bot

Before using this resource, you should create a bot for yourself. Please, [follow the instruction](https://core.telegram.org/bots#6-botfather) to create a bot and to get its **token**. Pass this value to the _bot_token_ parameter. 

Then, [find out the chat ID](http://stackoverflow.com/a/32572159/622833) you want to send notifications to. Now you are ready to use it.

### Private channel
It is a good idea to create a private channel for your team where bot will print all the messages from Concourse.
In this case you need to follow this steps:

1) create a _public_ channel, give it a name, say, _@MyChannel_
2) add your bot as an administrator
3) then, send some testing message to this channel, use this command: `curl -X POST "https://api.telegram.org/bot<botApi>/sendMessage?chat_id=@MyChannel&text=123"`. You should see a new message from your bot in your channel. Check the JSON output from this command, you will find an `chat_id`. The output could look like this:
`{ "ok" : true, "result" : { "chat" : { "id" : -1001005582487, "title" : "Test Private Channel", "type" : "channel" }, "date" : 1448245538, "message_id" : 7, "text" : "123" } }`
, where `chat_id` is `-1001005582487`
4) optionally, now you can make your channel private, generate link and send it to your collegues

### Forum topics
If you want to send messages to forum topics, you need to get the forum topic ID from the field `message_thread_id` using any of the following methods:

1) Get the `chat_id` and `message_thread_id` in the same way as for private channels
2) or send any message to the topic, then copy the link to the message through the context menu of this message "Copy Message Link". In the link of the form https://t.me/c/2251248935/33/122 the first group of digits is `chat_id`,  add -100 to the beginning and you get "-1002251248935", the second group of digits "33" is `message_thread_id`.

## Use the resource

The simplest example:

```yml
---
# declare custom resource type:
resource_types:
- name: telegram-notification
  type: docker-image
  source:
    repository: w32blaster/concourse-telegram-notifier
    tag: latest

# declare resource
resources:
- name: telegram-notification
  type: telegram-notification
  source:
    bot_token: "<bot token>"

# use it in your job
jobs:
- name: "Job Send Message To Telegram"
  public: true
  plan:
    - put: telegram-notification
      params:
         chat_id: "<your chat ID>"
         message_thread_id: "<your forum topic ID>"
         # you need to specify one of text or text_file properties, with text_file you can generate message text in your previous task
         text: "Build ok. [Build $BUILD_NAME](http://localhost:8080/pipelines/$BUILD_PIPELINE_NAME/jobs/$BUILD_JOB_NAME/builds/$BUILD_NAME)"
         text_file: './task_output/file_with_message'
         # optional parameter, Telegram API parse mode, may be HTML or Markdown, Markdown is default value
         parse_mode: HTML
```

Have fun.
