# [FreeCourseSite.com] Udemy - Red Hat Certified Engineer (RHCE) - 2018 - P39：9. Email server--3. Configuring main.cf - 我吃印度飞饼 - BV1jJ411S76a

Okay， now we're going to open one of the configuration files that we need to work on。



![](img/cf1668a694192f5fb736b37b11f82d20_1.png)

And let's clear this first。V， I am。Etsy。Host fixs。Main dot C，f。



![](img/cf1668a694192f5fb736b37b11f82d20_3.png)

Okay， and we're going to search for Inet。

![](img/cf1668a694192f5fb736b37b11f82d20_5.png)

Undersre。Interfaces。K。

![](img/cf1668a694192f5fb736b37b11f82d20_7.png)

And we're going to look for all the other。Okay， so this one is called local host。

 This is the one that we're gonna change。 So it's initially， you might find it commented out in your。

System， so just unment it， so it will look like this Inet_scoring interfaces because if it commentted out it's not going to be read。

And then， local host。And we'll just leave it at local hosts。

And the next one we're going to look for is。My destination。 My destination。



![](img/cf1668a694192f5fb736b37b11f82d20_9.png)

Okay and。

![](img/cf1668a694192f5fb736b37b11f82d20_11.png)

Let's see the line that we need to comment here。My destination is this one right here。

 So this is also probably commented out in your system。 So just uncom it。

 and then it should say my host name。And then local host， my domain。Then， local host。Okay。

And then we're going to add quite a few new lines will go all the way to the end of the system。



![](img/cf1668a694192f5fb736b37b11f82d20_13.png)

And then I'm going to hit O to create a new line， and I'm going to type in some。



![](img/cf1668a694192f5fb736b37b11f82d20_15.png)

Some more changes that we're going to make to the system， So my host name。Its going to be。Male dot。

LinuxGig。com。

![](img/cf1668a694192f5fb736b37b11f82d20_17.png)

Then my domain。He is going to be。LinuxG。com。

![](img/cf1668a694192f5fb736b37b11f82d20_19.png)

My。Origin。Is going to be。Dollar sign。Dollar sign my domain。



![](img/cf1668a694192f5fb736b37b11f82d20_21.png)

And home mailbox。Is going to be male。

![](img/cf1668a694192f5fb736b37b11f82d20_23.png)

Mail slash。My networks。And for this， we're going to put the loop back address， which is 127。0。0。

0 slash 8。

![](img/cf1668a694192f5fb736b37b11f82d20_25.png)

Then。Iinette。Interfaces。

![](img/cf1668a694192f5fb736b37b11f82d20_27.png)

Equals all。Then my。Destination。Equals。Dollar sign。Maai。😔，Host name。Local host。Dot。Dot。Dover sign。

 my domain。Local host。

![](img/cf1668a694192f5fb736b37b11f82d20_29.png)

My。😔，Doomain。Okay， then SMTPD。Underscore S AL。Under justscor type。Equals。Doveco。



![](img/cf1668a694192f5fb736b37b11f82d20_31.png)

Then S T S M。SMTPD。Underscore SA ASSL。Under path。Equals。Private。



![](img/cf1668a694192f5fb736b37b11f82d20_33.png)

At。SMT PDD。Underscore S A SL， underscore local。And Discor domain。



![](img/cf1668a694192f5fb736b37b11f82d20_35.png)

And just leave it at equal。S MT TP，D， underscore S A S。SL。Underscore。Security。Undersco options。

Equals。No， anonymous， no。E， O N。Y mo。M OUS。

![](img/cf1668a694192f5fb736b37b11f82d20_37.png)

No anonymousyous。Then broken。Under underscore S A SL。Undersco o。Underscore clients。Equals yes。



![](img/cf1668a694192f5fb736b37b11f82d20_39.png)

Then， SMTPD。SMTPD。Underscore SASL。Underscoreaught。And just score。Enable。Equals。



![](img/cf1668a694192f5fb736b37b11f82d20_41.png)

Yes。Then SMTPD。Undersco recipient。P， I， E N T recipient。Underscore restrictions。Equals permit。

And just。Under square I say。Pasel。Underco。Authentated。Permit。买。Networks。



![](img/cf1668a694192f5fb736b37b11f82d20_43.png)

SMtP。Tls。Underscore。Security。Undersco。Leve。Equals。

![](img/cf1668a694192f5fb736b37b11f82d20_45.png)

Mei。SMTPD underscore TLS。Underscore security。Underscore level。Equals may。



![](img/cf1668a694192f5fb736b37b11f82d20_47.png)

SMTP underscore TlS。Underscore note。Inderco， startteless。Underscore offer。Equals yes。



![](img/cf1668a694192f5fb736b37b11f82d20_49.png)

SMPD。Underscore Tl S， underscore note start。Tl underscore offer equals yes。 Okay。

 I just wanted to make sure because there is a good chance， you know。In Sion like this。

 you can make a typo， so I just wanted to make sure that I haven't done that。Okay， SMTPD。Underscore。

Tls。Underco log level。

![](img/cf1668a694192f5fb736b37b11f82d20_51.png)

Equals1。SMTPD。Underscore TlS， underscore key。Underscore file。Equals Esy。Host pics。SSL。



![](img/cf1668a694192f5fb736b37b11f82d20_53.png)

Server dot key。SMTPD。Undersco TlS。Underscore Ct。Underscore file。Equals at C。Post fix。SSL。

Server dot CRt。

![](img/cf1668a694192f5fb736b37b11f82d20_55.png)

SMTPD。Underscore TlS。Undersco received。Undersco head header。Equals yes。



![](img/cf1668a694192f5fb736b37b11f82d20_57.png)

SMTPD。T LS underscore。Session。And just score cash。Underscore timeout。Equs。



![](img/cf1668a694192f5fb736b37b11f82d20_59.png)

3，600。S。And the last line is TlS underscore sorry。SMTPD underscore TLS。TLS underscore。Random。

Underscoes source。Equals。Deev colon slash。Slash devb。Slash。You random。



![](img/cf1668a694192f5fb736b37b11f82d20_61.png)