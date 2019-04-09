---
title: django channels 2.x toy project on websocket
category: [python, django]
tag: [python, django]
---


# Objective

從上一份工作之後休息了半年的時間，這段期間跑了三次的馬拉松，休養了一下身體。
趁這個機會把玩了一下django channels這個聽聞已久的package．
這篇文章是這次經驗的相關紀錄。

## Background

就像wsgi 之於djanog. Channels 是python Asgi spec 在django上的實現。
而藉由asgi, djanog可以支援許多在原本無法支援的protocol。
例如：
- websocket
- IoT
- Chat

## Websocket

websocket 和傳統的http最大的差別就是websocket是雙向的protocol。
原本http只能由browser端(client)對server做request. 如果想實現server端對client的notificaiton
傳統上只能由client每隔一段時間對server做polling.  
而有了websocket就能避免這樣的設計。  
這種雙向的protocol第一時間能想到的應用有：  

- 聊天室
- 線上多人遊戲
- 類似像uber顯示位置的app設計(?


## channels本身的設計

- scope:  

    每個連線建立時，所帶有的資訊。例如session, cookie, request 從哪裡來。


- event:  

    從client來的要求，可能是http或是websocket

- consumer, route:  

    對於各種不同的event,透過route決定要由哪一個consumer處理，consumer的角色有點像是django裡的view
    route有點類似django的url

- channel layer, group:  

    在這樣的架構下的連線都可以視為在server有相對應的application instance(process?)
    而我們可以透過在每個連線初始化時將個別的instance加入某個group
    當我們需要對 group內的成員傳送資料時，就能在channel layer裡找到相對應的group
    
[a good visual on channels architecture on heroku blog](https://heroku-blog-files.s3.amazonaws.com/posts/1473343845-django-wsgi.png)





## Demo project

為了想要更了解channels的使用，  
看了幾分tutorial。大概都是聊天室或是傳送即時訊息的應用。 

[https://github.com/narrowfail/django-channels-chat](https://github.com/narrowfail/django-channels-chat) 
這是一個即時通訊toy project   
他的架構是：  
透過djanog rest api管理成員間的message(model),   
在message model裡sender和reciever的foreignKey,   
而前端每當打開一個對話窗時，會記錄當前的sender和reciever,    
並且在submit訊息時會帶給message的api,   
而新的message在save時會透過channel layer的group send去通知在group的成員。   

![channels toy project architecture]({{ site.url }}{{ site.baseurl }}/assets/images/1.png){:class="img-responsive"}
### 我的改良

因為channels已經進版到 2.x的版本，所有的api都已經大幅改動。  
原作者的程式碼是用1.x做的。並且有group send並沒有送到client的bug   
 
我先把原本的程式碼改成channels 2.X   
並且改成用pipenv管理套件。  
可以參考這個[PR](https://github.com/narrowfail/django-channels-chat/pull/6)  

在改成channels 2.0的過程中，最有趣的地方是在

on message model save, this take me some time to figure out
how to use Channels 2.0 to do a group_send outside a consumer.

I learned a lot from the archiecture. Many thanks to the [author](https://github.com/narrowfail/django-channels-chat/commits?author=narrowfail)


```py
    def notify_ws_clients(self):
        """
        Inform client there is a new message.
        """
        notification = {
            'type': 'recieve_group_message',
            'message': str(self.id)
        }

        channel_layer = get_channel_layer()

        async_to_sync(channel_layer.group_send)("{}".format(self.user.id), notification)
        async_to_sync(channel_layer.group_send)("{}".format(self.recipient.id), notification)

    def save(self, *args, **kwargs):
        """
        Trims white spaces, saves the message and notifies the recipient via WS
        if the message is new.
        """
        new = self.id
        self.body = self.body.strip()  # Trimming whitespaces from the body
        super(MessageModel, self).save(*args, **kwargs)
        if new is None:
            self.notify_ws_clients()


```


#### 繼續玩

如果有一個app可以支援雙方一邊能夠聊天  
又能夠一邊同步看youtube影片該有多好啊！(XDD 


再加一個websocket routing and consumer   
讓前端可以把正在看的影片以及時間送給正在聊天的人 
後來想了一下，我直接透過websocket傳遞資料是比較容易出錯的。
比較好的做法還是像message那一邊透過REST api去處理資料的格式以及驗證,
而websocket只是拿來當作notification就好。  
但也許有可能可以做到用websocket當作REST的媒介。 
查了一下已經有人做這樣的package了。 [https://github.com/linuxlewis/channels-api](https://github.com/linuxlewis/channels-api)
有機會來試試看。

the most interesting parts on the youtube feature are:

- youtube embed js api
- sending sender and reciever id and the yt player information
  to the websocket.
- the group handling on server side
  
## deploy to heroku

順便添加能deploy to  Heroku的設定檔.  
This take me some time for setting the heroku and asgi  
application correctlly.  

notices:

- django_heroku package 
- I turn off the collect static file.(setting the env variable)
 
   
[https://github.com/redliu312/django-channels-chat/tree/heroku](https://github.com/redliu312/django-channels-chat/tree/heroku)

如果想在local run
就用

```bash
python manage.py runserver --settings=chat.settings.local
```
































    














