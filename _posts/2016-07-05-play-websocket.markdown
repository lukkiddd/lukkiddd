---
layout: post
title:  "สร้าง WebSocket ด้วย Playframework"
date:   2016-07-05 16:28:00 +0700
categories: dev
---
เริ่มจากสร้าง project ขึ้นมาแล้วเขียน Controller ชื่อไฟล์ `Application.scala`

### Import library
{% highlight scala %}
import java.io.File
import javax.inject.Inject

import akka.actor.{Actor, ActorRef, PoisonPill, Props}
import play.api.Play.current
import play.api._
import play.api.libs.concurrent.Promise
import play.api.libs.iteratee.{Concurrent, Enumerator, Iteratee}
import play.api.libs.ws._
import play.api.mvc._

import scala.concurrent.ExecutionContext.Implicits.global
{% endhighlight %}

### เขียน webSocket
เขียน `class` ให้มัน `random` ส่งตัวเลขกลับไป ทุกๆ วินาที
{% highlight scala %}
class Application @Inject()(ws: WSClient) extends Controller {

  def wsRand = WebSocket.using[String] {
    request =>
      Logger.info(s"wsRand, client connected.")

      val outEnumerator: Enumerator[String] = Enumerator.repeatM(Promise.timeout(s"${new scala.util.Random()}", 1000))
      val inIteratee: Iteratee[String, Unit] = Iteratee.ignore[String]

      (inIteratee, outEnumerator)
  }
}
{% endhighlight %}

### conf/routes
{% highlight bash %}

# Websocket for random
GET  /random  controller.Application.wsRand

{% endhighlight %}

### ฝั่ง html
{% highlight html %}
<!DOCTYPE html>
  <meta charset="utf-8" />
  <title>WebSocket Test</title>
  <script language="javascript" type="text/javascript">

  var wsUri = "ws://<IP:PORT>/ROUTE";
  var output;

  function init()
  {
    output = document.getElementById("output");
    testWebSocket();
  }

  function testWebSocket()
  {
    websocket = new WebSocket(wsUri);
    console.log(websocket);
    websocket.onopen = function(evt) { onOpen(evt) };
    websocket.onclose = function(evt) { onClose(evt) };
    websocket.onmessage = function(evt) { onMessage(evt) };
    websocket.onerror = function(evt) { onError(evt) };
  }

  function onOpen(evt)
  {
    writeToScreen("CONNECTED");
    doSend("WebSocket rocks");
  }

  function onClose(evt)
  {
    writeToScreen("DISCONNECTED");
  }

  function onMessage(evt)
  {
    writeToScreen('<span style="color: blue;">RESPONSE: ' + evt.data+'</span>');
  }

  function onError(evt)
  {
    writeToScreen('<span style="color: red;">ERROR:</span> ' + evt.data);
  }

  function doSend(message)
  {
    writeToScreen("SENT: " + message);
    websocket.send(message);
  }

  function writeToScreen(message)
  {
    document.getElementById("data").innerHTML = message;
  }

  window.addEventListener("load", init, false);

  </script>

  <h2>WebSocket Test</h2>
  <div id="data"></div>
  <div id="output"></div>
{% endhighlight %}

เรียบร้อยแล้ว รันได้เลยยย

ใครสนใจหรือชอบ อยากจะแบ่งปันเพิ่มเติม เข้ามาคุยกันได้ที่ [Lukkiddd](http://www.facebook.com/lukkiddesign) ขอบคุณครับ

อ้างอิง:

* [WebSocket.Org](https://www.websocket.org/echo.html)
+ [Ticofab](https://github.com/ticofab/simple-play-websocket-server)
