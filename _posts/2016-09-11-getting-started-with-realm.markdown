---
layout: post
title: "getting started with realm"
date: 2016-09-11
categories:
  - Messaging
description: 
tags: ['real.io', 'android database', 'orm','realm.io','android']
image: https://unsplash.it/2000/1200?image=1003
image-sm: https://unsplash.it/500/300?image=1003
---

### Introduction:

For this tutorial we are going to discuss about the basic usage of Realm.

This tutorial is a part of a Github project implementing Realm with a ListView and Removing item from ListView on long click.

See code here: <a target="_blank" href="https://github.com/tosinonikute/Rsample">https://github.com/tosinonikute/Rsample</a>

So let's jump right in.


### So what is Realm?

It's a mobile database for storing your data. Just that simple.

Before Realm came to existence, everyone relied on SQLite which wasn't bad but Realm is better.  Realm leverages on its speed and its modern features.

It also delivers automatic seamless realtime data sync and powerful event handling between server and devices.

Realm database is cross platform, it supports both Android and iOS.


### 1. First we need to add realm to our project

To add realm, we need to add it to our build.gradle (module:app)

{% highlight gradle %}
// Realm Library
compile 'io.realm:realm-android:0.87.3';
{% endhighlight %}

### 2. Create a Realm instance

For us to create a Realm instance for this transaction

We need to create an Activity, and call the Realm.getInstance in the created activity and close after transaction completed.

{% highlight java %}
public class MainActivity extends AppCompatActivity {

    private Realm realm;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        // Get a Realm instance for this thread
        Realm = Realm.getInstance(Context);
        // other codes comes under
    }
}
{% endhighlight %}



### 3. Create the Realm Object

We need to create a model which will extend the RealmObject class.

{% highlight java %}
public class PhoneBook extends RealmObject {

    @PrimaryKey
    private long id;
    private String name;
    private String lastName;
    private String email;
    private String phone;

    public long getId() {
        return id;
    }

    public void setId(long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }
}
{% endhighlight %}


### 4. Create the transaction

We create the transaction by using Realm.beginTransaction(); and creating the Realm object for the model that we have created.

Please note that the transaction code should be in a try and catch block, so as to handle any thrown exceptions.

Here we create the Realm object

{% highlight java %}
realm.beginTransaction();
PhoneBook phoneBook = realm.createObject(PhoneBook.class);
{% endhighlight %}


We create a Unique column so as to get column data by ID, just as its being done using SQLite

{% highlight java %}
// increment index
long nextID = (long) (realm.where(PhoneBook.class).max("id"));
long primaryKeyValue = nextID + 1;
{% endhighlight %}


We save the data and then commit transaction.

{% highlight java %}
phoneBook.setId(primaryKeyValue);
phoneBook.setName(name.getText().toString());
phoneBook.setLastName(lastName.getText().toString());
phoneBook.setEmail(email.getText().toString());
phoneBook.setPhone(phoneNumber.getText().toString());
realm.commitTransaction();
{% endhighlight %}


Don't forget to close the Realm instance.


{% highlight java %}
// realm.close();
{% endhighlight %}

### FULL CODE

{% highlight java %}
public class MainActivity extends AppCompatActivity {

    private Realm realm;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Get a Realm instance for this thread
        realm = Realm.getInstance(getApplicationContext());

                    try {
                        //Writing to Realm with Transaction blocks
                        realm.beginTransaction();

                        PhoneBook phoneBook = realm.createObject(PhoneBook.class);

                        // increment index
                        long nextID = (long) (realm.where(PhoneBook.class).max("id"));
                        long primaryKeyValue = nextID + 1;

                        phoneBook.setId(primaryKeyValue);
                        phoneBook.setName(name.getText().toString());
                        phoneBook.setLastName(lastName.getText().toString());
                        phoneBook.setEmail(email.getText().toString());
                        phoneBook.setPhone(phoneNumber.getText().toString());
                        realm.commitTransaction();

                    } catch (Exception e) {
                        Log.e("Realm Error", "error" + e.getLocalizedMessage());
                        realm.cancelTransaction();
                    }

                    //Close the realm instance for this thread
                    realm.close();
        }
}
{% endhighlight %}



### 5. Retrieve values from Realm

For Quering and reteiving values from Realm is easy as it can get.

You can ask use series of Queries, but here we make use of findAll()

See url link for more Queries: <a target="_blank" href="https://realm.io/docs/java/latest/#queries">https://realm.io/docs/java/latest/#queries</a>

{% highlight java %}
        RealmResults<PhoneBook> results;
        Realm realm = Realm.getInstance(getApplicationContext());

        //Writing to Realm with Transaction blocks
        realm.beginTransaction();

        try {
            results = realm.where(PhoneBook.class).findAll();
        } catch (Exception e) {
            Log.e("Realm Error", "error" + e.getLocalizedMessage());
            realm.cancelTransaction();
        }
        realm.commitTransaction();
        realm.close();
{% endhighlight %}



### 6. Realm browser

You can see how the data looks in a Realm browser version , Just in case you are using a newer version at the time of reading the post.




