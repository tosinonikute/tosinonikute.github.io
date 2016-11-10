---
layout: post
title: "firebase push notification Android"
date: 2016-09-07
categories:
  - Juice
description: 
tags: ['firebase', 'messaging', 'gms','fcm','android','notification','push notification','push messages']
image: https://unsplash.it/2000/1200?image=1003
image-sm: https://unsplash.it/500/300?image=1003
---

### <b>What is firebase ? </b>

Firebase is a cloud backend, that helps you build apps, grow your user base, and earn more money. Firebase features provides you authentication, real-time data updates, queries to filters for your data, offline capabilities and analytics.

This topic will focus on sending push notifications with firebase using Postman and Curl, In firebase you can send notifications to a specific device, to list of devices and subscribe users to a topic.

<b>This tutorial covers</b>

1. Sending messages from firebase console.
2. Sending data or notification messages.
3. Sending to One or more devices.
4. Using Postman Http Client to send messages.
5. Sending messages using Curl with PHP.

### 1.  Lets get started.

1. Get the latest Android Studio: <a href="https://developer.android.com/studio/index.html">https://developer.android.com/studio/index.html</a>
2. Create an account on Firebase: <a href="https://firebase.google.com">https://firebase.google.com</a>

<b>Add a new project or import an existing project</b>
<img src="https://tosinonikute.github.io/images/firebase/first.png" class="post-img" style="width:450px">


<b>Enter a project name for your project and select your country</b>
<img src="https://tosinonikute.github.io/images/firebase/second.png" class="post-img" style="width:450px">

<b>It brings up a new page, choose "Add Firebase to your Android app". </b>
<img src="https://tosinonikute.github.io/images/firebase/third.png" class="post-img" style="width:450px">

<b>Then you need to set a package name for your app, make sure its the same package name that's in your app.</b>
<img src="https://tosinonikute.github.io/images/firebase/fourth.png" class="post-img" style="width:450px">

<b>Add google-services.json file to your Android Studio, in Project Mode. </b>
<img src="https://tosinonikute.github.io/images/firebase/fifth.png" class="post-img" style="width:450px">

Then go to your Project-level build.gradle (<project>/build.gradle):  to configure your project and Sync.
<img src="https://tosinonikute.github.io/images/firebase/sixth.png" class="post-img" style="width:450px">

<b>Remember to add this line at the botom of your build.gradle</b>

{% highlight gradle %}
apply plugin: 'com.google.gms.google-services'
{% endhighlight %}

Include the firebase dependecies

{% highlight gradle %}
dependencies {
    compile 'com.google.firebase:firebase-core:9.2.0'
    compile 'com.google.firebase:firebase-messaging:9.2.0'
}
{% endhighlight %}

<b> Update Google services - For existing Google services Only </b><br>
You need to update any existing services that are using com.google.android.gms:play-services, Failure to update will result to  google services version conflict.


<b> Add your applicationId to the defaultConfig section of your build.gradle (Module - app) </b>

{% highlight gradle %}
   defaultConfig {
        applicationId "com.example.myfirebaseapp" //add your application Id
        minSdkVersion ...
        targetSdkVersion ...
    }
{% endhighlight %}

<b> Add firebase services to your app </b>

i. Add a Class extends FirebaseMessagingService

{% highlight java %}
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private static final String TAG = "FCM Service";
    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
    Log.d(TAG, "From: " + remoteMessage.getFrom());
    Log.d(TAG, "From: " + remoteMessage.getNotification().getBody());
    }
}    
{% endhighlight %}

--- Then add it into the AndroidManifest.xml file, In the <b>application tag</b>.
   
   {% highlight xml %}
         <service
            android:name=".MyFirebaseMessagingService"
            android:enabled="true"
            android:exported="true">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
   {% endhighlight %}


--- Add a service that extends FirebaseInstanceIdService

    {% highlight java %}
    public class FirebaseIDService extends FirebaseInstanceIdService {
        private static final String TAG = "FirebaseIDService";

        @Override
        public void onTokenRefresh() {
        String refreshedToken = FirebaseInstanceId.getInstance().getToken();
            Log.d(TAG, "Refreshed token: " + refreshedToken);

            // TODO: Implement this method to send any registration to your app's servers.
            sendRegistrationToServer(refreshedToken);
        }
    
        private void sendRegistrationToServer(String token) {
            // Add custom implementation, as needed.
        }
    }
    {% endhighlight %}
    
--- Add it into the AndroidManifest.xml file, In the <b>application tag</b>.
       
       {% highlight xml %}
       <service android:name=".FirebaseIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT" />
            </intent-filter>
        </service>
        {% endhighlight %}


<b>Now its time for testing.</b>

To test to see if the setup works, go to your Firebase console, Go to Notifications.
<img src="https://tosinonikute.github.io/images/firebase/seventh.png" class="post-img" >


<br><b>Sending Notification Messages </b>

Type your message in the box and click SEND MESSAGE.
<img src="https://tosinonikute.github.io/images/firebase/eigth.png" class="post-img">

Now you should get a push notification on your Android mobile. Please note that If your app is running on the background, you will get it on the mobile's notification center; otherwise you can see it in your Android Monitor log (we have to put a code to log incoming messages) like this.

    {% highlight java %}
    Log.d(TAG, "From: " + remoteMessage.getFrom());
    Log.d(TAG, "From: " + remoteMessage.getNotification().getBody());
    {% endhighlight %}

If everything is correctly setup, you should get a push notification on your device. 
<br><b>Note:</b> You must also keep in mind that to receive Messages sent from Firebase Console, App must be in background, not started neither hidden.

And that's all, you're Done !

It can take up to some couple of minutes for notifications to deliver, so be patient :) 



<br><br>
<a href="#" class="my-link">Furthermore...Lets go deeper into firebase notifications</a>

### 2.  Sending Data messages: 

Data messages enables sending messeges as Key-value pairs and doesn't automatically displays the message to end-user devices on behalf of the client app. You have to write your own notification method to handle incoming data message and show to the user. You can also receive data push notification even when app is not on background.

To send data messages, go to your firebase console, Select advance option, enter your title and your custom data message as key and value pairs. 

<img src="https://tosinonikute.github.io/images/firebase/nineth.png" class="post-img">

Data messages come as Map, To get the value of your data messages, see below:

{% highlight java %}
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private static final String TAG = "FCM Service";

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
    
       Log.d(TAG, "From: " + remoteMessage.getData().size()); // for the data size
       final Map<String, String> data = remoteMessage.getData();
       String title = data.get("title");
       String body = data.get("body");
       if(!title.equals("") && !body.equals("")){ 
           sendNotificationData(title, body); //send notification to user
       }
    }
    
    private void sendNotificationData(String messageTitle,String messageBody) {
        Intent intent = new Intent(this, YourActivity.class);
        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        PendingIntent pendingIntent = PendingIntent.getActivity(this,0 /* request code */, intent,PendingIntent.FLAG_UPDATE_CURRENT);

        long[] pattern = {500,500,500,500,500};

        Uri defaultSoundUri= RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);

        NotificationCompat.Builder notificationBuilder = (NotificationCompat.Builder) new NotificationCompat.Builder(this)
                .setSmallIcon(R.drawable.your_drawable)
                .setContentTitle(messageTitle)
                .setContentText(messageBody)
                .setAutoCancel(true)
                .setVibrate(pattern)
                .setLights(Color.BLUE,1,1)
                .setSound(defaultSoundUri)
                .setContentIntent(pendingIntent);

        NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
        notificationManager.notify(0 /* ID of notification */, notificationBuilder.build());
    }
    
    
}
{% endhighlight %}


You can also send notification using an HTTP client, you need structure the JSON formatted data, specify the Content-Type, Authorization and device Id.

<b> url: https://fcm.googleapis.com/fcm/send </b>

{% highlight json %}
{
  "to": "DeviceId", 
   "data": {
     "title"  : " This is my title message ",
     "body" : " This is the body of my message "
   }
}
{% endhighlight %}

Device Id comes as a token, Firebase takes care of fetching the token and catching it locally.
This method would return the token, if available, or null if the fetching phase is still in progress.

{% highlight java %}
FirebaseInstanceId.getInstance().getToken();
{% endhighlight %}

{% highlight java %}
public class FirebaseIDService extends FirebaseInstanceIdService {
    private static final String TAG = "FirebaseIDService";

    @Override
    public void onTokenRefresh() {
        // Get updated InstanceID token.
        String refreshedToken = FirebaseInstanceId.getInstance().getToken();
        Log.d(TAG, "Refreshed token: " + refreshedToken);

        // TODO: Implement this method to send any registration to your app's servers.
        sendRegistrationToServer(refreshedToken);
    }
    private void sendRegistrationToServer(String token) {
        // Add custom implementation, as needed.
    }
}
{% endhighlight %}

<b>Lets start by using POSTMAN HTTP client, to make a post request. </b>

POSTMAN is a simple tool used to send any HTTP request using the awesome Postman request builder, validate response data, response times, and more. 

Get postman here: <a href="https://www.getpostman.com/">https://www.getpostman.com/</a> 

Alternatively, you could just add it to your chrome extension: <a href="https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?hl=en">https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?hl=en</a>

Set your headers

<img src="https://tosinonikute.github.io/images/firebase/tenth.png" class="post-img2">

Add your json formatted data and Send.

<img src="https://tosinonikute.github.io/images/firebase/eleventh.png" class="post-img2">

You should get your notitification data sent to your phone in few seconds or minutes.


### 3. Sending to One or more devices.

Firebase doesn't allow sending to all users, except you are sending from the Firebase console itself.
To send data to multiple devices at once, you need to create a topic and subscribe users to that topic. It is pretty easy, just a one liner.

In your Activity, just add this line below

{% highlight java %}
FirebaseMessaging.getInstance().subscribeToTopic("news");
{% endhighlight %}

When a client app subscribes to a new topic name (one that does not already exist for your Firebase project), a new topic of that name is created in FCM and any client can subsequently subscribe to it.
 
Then you can send messages to that topic.

<b> url: https://fcm.googleapis.com/fcm/send </b>

{% highlight json %}
{
  "to": "/topics/news", 
   "data": {
     "title"  : " This is my title message ",
     "body" : " This is the body of my message "
   }
}
{% endhighlight %}
 
 
 
 <b> Sending messages using PHP Curl </b>
 
Alternatively, For those comfortable with PHP, you can use Curl with PHP to send messages to users devices that subscribe to a topic.

See code snipet below:

{% highlight php %}
<?php
$url = "https://fcm.googleapis.com/fcm/send";

$values = array();
$values ['title'] = "This is my message title";
$values ['body'] =  "This is my message body";

$data = array();
$data ['to'] = "/topics/news";
$data ['data'] = $values;

$content = json_encode($data);

$curl = curl_init($url);
curl_setopt($curl, CURLOPT_HEADER, false);
curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
curl_setopt($curl, CURLOPT_HTTPHEADER,
        array(
        	"Content-type: application/json",
            "Authorization: key=Your Authorization Key"
        	));
curl_setopt($curl, CURLOPT_POST, true);
curl_setopt($curl, CURLOPT_POSTFIELDS, $content);

$json_response = curl_exec($curl);

$status = curl_getinfo($curl, CURLINFO_HTTP_CODE);

if ( $status != 201 ) {
    die("Error: call to URL $url failed with status $status, response $json_response, curl_error " . curl_error($curl) . ", curl_errno " . curl_errno($curl));
}

curl_close($curl);
$response = json_decode($json_response, true);
?>
{% endhighlight %}



### 4. You're done
Take a stop on the Github project page. Feel free to give a star or feedback, share it with others!  
[See the Github Project](https://github.com/tosinonikute/FirebaseNotification) 
