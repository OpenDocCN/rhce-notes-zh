# Red Hat Certified Engineer (RHEL 8 RHCE) - P44：388-4875-2 - Creating and Using Roles - 11937999603_bili - BV12a4y1x7ND

Welcome back， everyone。 This is Matt。 And in this video。

 we're going to continue our discussion on ansible roles。 And in the previous lesson。

 we learned a little bit more about roles。 And in this lesson。

 I'm going to show you how you can create a role and then also use it in your playbook。

 So let's head over to section 10。😊。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_1.png)

And then down to creating and using roles。 So in the diagram。

 I have provided just some examples of what I'm going to be doing in order to create this role。

 So let's head over to the command line and we can get started。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_3.png)

And as you can see， I am in the roles directory， which is our newly defined directory for our ansible roles。

 Currently， the only role that exists is a role called common。 And for this demonstration。

 we're going to create a role called Apache that is going to set up and configure some Apache HtTPD servers for us。

 So the first thing that we need to do is create the directory structure for our role。

 and in order to do this， we need to use the ansible galaxy in knit command。

 So we're going to type in ansible galaxy。A knit and then the name of our role。

 which is going to be Apache。And we get a message that Apache was created successfully。

 so now let's go ahead and do a listing on that。And we see that this skeletal directory structure has been set up for our new role。

 So now that we have our structure， the first thing that we're going to do is create our task。

 and these need to be added in the main dot yamel of the task directory。

 Let's go ahead and C D into Apache task。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_5.png)

And then we can open up the main dot yal。And as you can see。

 the Ansible Galaxy and knit command created this main dot Yaml， and it's just a simple file。

 it's going to start with three dashes， lets us know that this is a Yaml file。

 and then just a short description which says taskask file for Apache。

So let's go ahead and add a new line and rather than you sitting and just watching me type through all these tasks of which there are several。

 I'm just going to copy it for my clipboard。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_7.png)

We'll head back to the top。 Let's just make sure that the formatting seems okay。

 which it does at first glance。 And now we can just walk through the different tasks that we're going to be performing with this role。

 So the first thing we're going to do is create a web content directory。 And for that。

 we're going to use the file module。 And then for Pa， we're actually specifying a variable。

 which I will show you in just a minute。 And this is going to be creating a directory。

 And then we're setting the mode to 0，7，5，5。And then on this directory that we're creating。

 which is called web content， we're going to set the SE context。

 so we're going to use the SEF context module and then for target we're going to use our variable once again and then SE type is going to be HTTPD assist content T and state is going to be present。

And then we're going to be running Restorecon on web content。

 So here we just have the restorestorecon command with dash RV and our Apache content directory variable。

And then we're going to be installing Apache for that we're going to use the y module。

 The name is HtTPD， and then the state is latest。And then we're going push out a couple templates。

 The first is the HttP。 co。 So we're going to use the template module and for source。

 you're going to notice that I'm just listing the name of the template and I'm not specifying a relative path or an absolute path。

 and that's because as long as our templates are in our template directory。

 we can just specify the name we don't have to specify path。

 and then we're going specify the destination which is going to be the HttPD。

 co file and I'm going to go ahead and perform a backup of that。

 and then we're also going to use the notify keyword in order to notify handler that will be creating momentarily Next we're going to deploy our index HTMLtml using the template module。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_9.png)

And notice that for a destination， I'm actually using the Apache content du variable。

 and we're going to call the file index。 HTMLtm。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_11.png)

And then we're going to start and enable the HttPD service。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_13.png)

All right， let's go ahead and save and quit this。And before we move on to templates or handlers。

 let's go ahead and set up our variables。So I'm going to see the into the defaults directory。

And this is the location that we can set our default variables。

And I'm going to open up the main dot Yal。We can add a new line。And here。

 all we're going to do is specify our variables。 So I'll go ahead and copy。And I can paste these in。

And as you can see we are specifying three different variables， we have Apache content Du。

 which is slash web content and then Apache HttP port， which is going to be 8080。

 and then Apache admin， which is going to be cloud user So now that these variables have been defined。

 we can reference them anywhere within our role， So let's go ahead and save and quit this。

Now I'm going to seed into the templates directory。And as you can see。

 we don't have any template files yet。 and again， for sake of time。

 I went ahead and created these templates， and for many of you。

 these are going to be very familiar from our Apache template demonstration。

 Let's go ahead and copy these from home cloud user Ansible templates。Then we have httPd。com。j2。

So go ahead and copy that as well as the index dot HTML。Now let's go ahead and open up the HttPd。com。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_15.png)

As you can see， this is very similar to the template that we used in a prior demonstration。

But as you can see， I've added the namespace variable， so we have Apache HtTP port。

And then Apache admin here for the server admin。And then for the document route。

 we're using Apache content du。 All right， let's go ahead and quit out of this。

 And then just briefly， we can open up the index dot Hm。

 And this is going to remain relatively unchanged since we're using the variables that were gathered by ansible facts。

 All right， so we'll go ahead and quit out of that。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_17.png)

And before we move on， I did want to show you that I have these examples within the diagram。

 so we have great tasks。 It's going to be all the tasks that we're using。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_19.png)

And then we have create variables。And then it create templates。

 and here's the example of the index do Htm and also the HttPd do com。 And then on the next page。

 I have create handlers and finally create playbook。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_21.png)

So let's head back over the command line and we can create the handlers and then we can actually run our role in a playbook。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_23.png)

So clear this out。If I can spell it correctly。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_25.png)

And then we're going to see D into handlers。We'll see our pre generatedrated main。 Yal。

Let's go ahead and grab our handler from our clipboard。And as you can see。

 the name of our handler is restart web servers， we're going to be using the service module。

 the name is HttPD and the state is restarted， and we're going to be listening for that notify keyword。

 which is restart Apache。So let's go ahead and save and quit this。

 Now let's see the back a couple directories。Maybe one more and then into our playbooks directory。

And now we're going to create a playbook in order to run this role。

 So let's just call this roll dot Yl。Start with our three dashes and then host。

 we're going to be executing this against the web server host。

Since we're running some privilege commands， we need to set that become to be yes。

Now there's a couple different ways that we can reference our role within our playbook。

 and that's why I've provided a couple different examples in the diagram。

 So one way is to specify the roles keyword。And then we can come under here and we could just type in the name of our roll。

 which is Apache。 and if we saved and quit and then ran this， it would pull in our roll。

 and that would also pull in all our tasks and our handlers and variables and templates。

 and then it would execute our roll。But we can also specify role and then a colon and then the name of our roll。

Should be Apache， and you could also use the include roll keyword and then specify the name of the roll。

 as you see at the bottom of the diagram。 But let's just go ahead and save it as it is。

And before we run that playbook， let's head over to MS。 Pearson 3C。

 which is one of the two servers in our Web servers group。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_27.png)

First， I just wanted to do a listing， showed that the web content directory has not been created。

And then also， we can do a pseudo yum list installed。And then Grep for HttPD。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_29.png)

And we see that the service is not installed， so let's head back to our control node。

 and now we can kick off our playbook。Soll be Antsible playbook。And then roll dot Yaml。

And this may take a little while， so I'll go ahead and speed this up so you don't have to sit and wait。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_31.png)

And we see that just by specifying our role in the playbook。

 it pulled in all the tasks that we define and executed them。

 So let's scroll up and we can just walk through that real quick so we gather our facts。

 and then we create our web content directory。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_33.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_34.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_35.png)

And then we set the Se context on that directory。Next， we run restorestorecon。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_37.png)

And then install Apache。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_39.png)

And then we push out our two templates， httbd。com and index。htm。

 and then finally we start and enable the HttBD service。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_41.png)

And lastly you're going to see running handler， and that's because we had a change to the Apache configuration file and we said notify the handler whenever that happens。

 so it went ahead and restarted our web servers。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_43.png)

So let's head over to MS Pearson 3 just to validate our changes。

So go ahead and clear that first we'll see if our web content directory was created。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_45.png)

And it was。 Let's make sure that the context is correct。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_47.png)

And that is set to HttbD cis content。We can run a system CTL status， H TTBD。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_49.png)

And we see that our service was installed， enabled， and is up and running。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_51.png)

Now let's make sure our index。 HTML was deployed。And it was。

 and let's go ahead and open up the EtsyttbDtb。com file。

And we see that our template was pushed out and all our variables were updated with the appropriate values。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_53.png)

Let's go ahead and quit out of here， and we're going to head back to our control node。

Let's just try a simple curl。On MSParson 3C on port 880。And we see that we get our index do HTML。

Well， we validated that our role is executing successfully， but before we finish。

 I wanted to show you one last thing。So let's open up our playbook one more time。

And I'm going to show you how you can add a variable here in the playbook and how that is going to override the default variable that you put in your defaults directory。

So let's add another line and at the same level as rule， we're going to add in vrs。

And then we're going to specify our variable name。 And for this example。

 let's just go ahead and update the Apache port so it's going to be Apache underscore HttP。

Underscore port， and we're going to set that to be 80 rather than 8080。

So let's go ahead and save and quit， and then we can kick off our playbook again。

And I'll go ahead and speed this up。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_55.png)

And now that that's finished， one thing that you'll notice is that the handler was notified again。

 and that's because we updated the httPd。com file。 So now let's clear this out and go ahead and try to run our curl again。

 So Emmas Pearson 3C first let's try it on 8080。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_57.png)

As you can see， we get a connection refused。Let'll see what happens when we try to hit it on 80。

And as expected， we'd get our index。 HTML。Just to further validate that。

 let's go ahead and open up our HttP。com file。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_59.png)

And we see that our listen port is now set at 80。Well。

 that's going to finish up this video on creating and using roles。

 so let's go ahead and market Comp and we can move on to the next section。



![](img/9168d8cd67d2ca9fa21312e9b23df99d_61.png)