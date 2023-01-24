---
layout: post
title: Rural area LTE coverage challeges 
tags: [Mikrotik,LTE]
date: 2023-01-03 17:09:57 +0200
comments: true
---
### Intro
My home village was always neglected by local authorities, all infrastructure especially internet was never on list of priorities. When I was a teen, during late '90, where most of the people had ADSL connection we in forgotten area (about 60km from Zagreb) were still on dial-up. That was really frustrating. Fast forward into 2023 and situation did not improve, internet still sucks as much as local authorities. Luckily, mobile internet technology had evolved over these years. 

### Challenge
Due to messed-up geography my house has very poor LTE coverage. With mobile phone you can find a spot to make a call if you lucky.
During years I tested lot of gear antennas, LTE routers and stuff. Most of it was performing bad, or worse then bad. But at some point [Mikrotik LTE-SXT](https://mikrotik.com/product/sxt_lte_kit) router become available so I decided to give it a go.

![SXTLTE](https://i.mt.lv/cdn/rb_images/1884_m.png)
![SXTLTE](https://i.mt.lv/cdn/rb_images/1885_m.png)

It was a success! With poor reception I was able to get around 25Mbps download and 10-15Mbps upload (depending on a weather condition). Router was working reliably for years until recently, it started to freeze for some reason (updating modem firmware or any other update did not help) and when it was working reception had drastically deteriorated. Also, neighbors pines had drastically grown bigger and made reception even worse.

### Solution
 Drastic times require drastic measures. I decided to do a upgrade and replace existing router with [MikroTik RouterBOARD LHG LTE kit](https://mikrotik.com/product/lhg_lte_kit)

![lhg_lte_kit](https://i.mt.lv/cdn/rb_images/1662_m.png)
![lhg_lte_kit](https://i.mt.lv/cdn/rb_images/1665_m.png)

I had the mounting pole and wiring set-up from previous installation so it was easy to replace it.

![install1](..\assets\img\2023-01-04\install1.jpg){:width="50%" ; .mx-auto.d-block :}

Since LHG does not have WiFi I took my spare routerboard with AP to connect from mobile app and find best position for antenna.

![install2](..\assets\img\2023-01-04\install2.jpg){:width="50%" ; .mx-auto.d-block :}
Signal was very poor as always but at least quality was good.

![signal](..\assets\img\2023-01-04\signal.jpg){:width="50%" ; .mx-auto.d-block :}
Oh boy, this was a dramatic step up in performance. With significantly bigger antenna radius performance had tripled my download and increased upload! Router had been up and running for 3 months and it appears it has no problem with neighbors pines.

![speedtest](..\assets\img\2023-01-04\speedtest.png){:width="50%" ; .mx-auto.d-block :}

Now I can work happily from my forgotten village again if I have to.

Thank you for reading and I hope this post can help if you find yourself in similar situation.