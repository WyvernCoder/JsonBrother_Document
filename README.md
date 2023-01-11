# JSON Data Brother

LANGUAGE: English, [简体中文](README_zh.md)

[TOC]

## INTRODUCE

This plug-in can easily process JSON data and message them back to your server.

> I have no work experience but I know I can use php **ECHO** to return the data I want. 
>
> For example, you need a function to query user data and this function can be run on UNITY or UNREAL ENGINE. You may search for SQL plug-ins in UNITY MARKET or EPIC MARKET, but it will be very troublesome. (You need twice as much work)
>
> If we implement this function on PHP and then use the HTTP REQUEST function of UNITY or UNREAL ENGINE to query the SQL data, this will become very simple.
>
> <img src="DocumentResource\workflow_in_mind.png" style="zoom:35%;" />



## SUMMARY

This plug-in has two parts: **Json Handler** and **Json Sender**. Their names are the same as their functions.

| Name         | Function                                                     |
| :----------- | ------------------------------------------------------------ |
| Json Handler | *Serialize* your JsonObject or *deserialize* your JsonString. <br />You can modify the value of the field after deserialization or just read their values. <br /> |
| Json Sender  | Message json data to your php and receive data returned by php echo.<br /> |



## JSON HANDLER

I don't want this plugin to be toooo complex, so I only gave it simple **GET** and **SET** functions, which is enough to complete my stupid workflow. 

**Note that you cannot directly replace an array object with other array object**

<img src="DocumentResource\JsonHandler_Family.png" style="zoom:80%;" />



## JSON SENDER

Because of some limitations of UE C++, this function *(JsonSender)* cannot be separated from an object, so I made it into a **component**, which will work well in my cute Actor. 

This node will create a HTTP REQUEST **POST** which MIME type is set to **Application/json; Charset="UTF8"** . When the web is loaded, the **OnRequestCompletee** delegate will be broadcast.

<img src="DocumentResource\JsonSender_Family.png" style="zoom:80%;" />



------



## How does my PHP return data?

#### First, Create a blank php like this.

 <img src="DocumentResource/image-20230111220141324.png" style="zoom:50%;" />



#### Second, Type the php code like this

```php
<?php 
echo "Shader makes me full of power!";
?>
```



#### Third, Open your browser and open php page

As you can see, only your **ECHO CONTENT** on the whole page. 

This is the **result** that the website will return to our program. (CallbackDelegate->Content)

 <img src="DocumentResource/image-20230111220500579.png" alt="image-20230111220500579" style="zoom: 50%;" />



#### So, if we write this php in a more complex way...

```php
<?php
	if($mode == "query")
	{
		$username = YOUR QUERY CODE;
		echo $username;
	}

	if($mode == "delete")
	{
		YOUR DELETE CODE;
		echo "Successful";
	}

	if($mode == "add")
	{
		YOUR ADD CODE;
		echo "Successful";
	}
?>
```

Can you imagine that your work has been transferred to PHP!

**You are no longer limited by SQL plug-ins.**

**No matter what program you are developing, As long as your platform can message HTTP REQUEST, it can connect with your php system!**



------



## How to check my php return data in BP?

#### First, Add a JsonSender ActorComponent to your Actor

 <img src="DocumentResource/image-20230111223616711.png" alt="image-20230111223616711" style="zoom:50%;" />



#### Second, Create node like this

This will bind the function for the delegate OnRequestCompletee.

 <img src="DocumentResource/image-20230111223726726.png" alt="image-20230111223726726" style="zoom:50%;" />



#### Third, Create a CustomEvent for the red "Event"

 <img src="DocumentResource/image-20230111223828458.png" alt="image-20230111223828458" style="zoom:50%;" />



And you will see this node, **Content** variable is your php return data.

 <img src="DocumentResource/image-20230111223913962.png" alt="image-20230111223913962" style="zoom:33%;" />



#### Fourth, Print the content

 <img src="DocumentResource/image-20230111224039510.png" alt="image-20230111224039510" style="zoom:50%;" />

​	

#### Fifth, Send JSON String to your php

My php will return the value of the mode field. You can find the PHP code below.

 <img src="DocumentResource/image-20230111224214411.png" alt="image-20230111224214411" style="zoom:50%;" />

```php
<?php
	header("content-type:text/html;charset=utf-8");
	$jsonString = file_get_contents("php://input");
	$jsonObject = json_decode($jsonString);
	echo $jsonObject->mode;
?>
```



#### Sixth, Place actor to your level

 <img src="DocumentResource/image-20230111224639682.png" alt="image-20230111224639682" style="zoom:50%;" />



#### Final, Play or Simulate

 <img src="DocumentResource/image-20230111224800615.png" alt="image-20230111224800615" style="zoom:50%;" />