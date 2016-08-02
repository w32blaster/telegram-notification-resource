# telegram-notification-resource
Resource for Concourse CI to send messages to Telegram.

![Telegram Botfather](https://core.telegram.org/file/811140763/1/PihKNbjT8UE/03b57814e13713da37)

It allows you to send (put) a messages to Telegram from your pipeline.

## Prepare your bot

Before using this resource, you should create a bot for yourself. Please, [follow the instruction](https://core.telegram.org/bots#6-botfather) to create a bot and to get its **token**. Pass this value to the _bot_token_ parameter. 

Then, [find out the chat ID](http://stackoverflow.com/a/32572159/622833) you want to send notifications to. Now you are ready to use it.

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
      chat_id: <your chat ID without quotes>
      text: "Build ok. [Build $BUILD_NAME](http://localhost:8080/pipelines/$BUILD_PIPELINE_NAME/jobs/$BUILD_JOB_NAME/builds/$BUILD_NAME)"
```

Have fun.