---
title: "[Basic] 数字证书"
catalog: true
toc_nav_num: true
date: 2019-01-13 10:00:30
subtitle: "什么是数字证书，干嘛用的？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- Basic
catagories:
- Basic
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1


数字签名是什么？
=======

> 原文网址：http://www.youdzone.com/signature.html

1. 鲍勃有两把钥匙，一把是公钥，另一把是私钥。
    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/bg2.png" height="100" />  



2. 鲍勃把公钥送给他的朋友们----帕蒂、道格、苏珊----每人一把。

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/bg3.png" height="100" />  


3. 苏珊要给鲍勃写一封保密的信。她写完后用鲍勃的公钥加密，就可以达到保密的效果。

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/bg4.png" height="100" />  

4. 鲍勃收信后，用私钥解密，就看到了信件内容。这里要强调的是，只要鲍勃的私钥不泄露，这封信就是安全的，即使落在别人手里，也无法解密。

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/bg5.png" height="100" />  


5. 鲍勃给苏珊回信，决定采用"数字签名"。他写完后先用Hash函数，生成信件的摘要（digest）。

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/bg6.png" height="100" />  


6. 然后，鲍勃使用私钥，对这个摘要加密，生成"数字签名"（signature）。

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/bg7.png" height="100" />  

7. 鲍勃将这个签名，附在信件下面，一起发给苏珊。

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/bg8.png" height="100" />  

8. 苏珊收信后，取下数字签名，用鲍勃的公钥解密，得到信件的摘要。由此证明，这封信确实是鲍勃发出的。

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/bg9.png" height="100" />  

9. 苏珊再对信件本身使用Hash函数，将得到的结果，与上一步得到的摘要进行对比。如果两者一致，就证明这封信未被修改过。

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/bg10.png" height="100" />  

10. 复杂的情况出现了。道格想欺骗苏珊，他偷偷使用了苏珊的电脑，用自己的公钥换走了鲍勃的公钥。此时，苏珊实际拥有的是道格的公钥，但是还以为这是鲍勃的公钥。因此，道格就可以冒充鲍勃，用自己的私钥做成"数字签名"，写信给苏珊，让苏珊用假的鲍勃公钥进行解密。

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/bg11.png" height="100" />  


11. 后来，苏珊感觉不对劲，发现自己无法确定公钥是否真的属于鲍勃。她想到了一个办法，要求鲍勃去找"证书中心"（certificate authority，简称CA），为公钥做认证。证书中心用自己的私钥，对鲍勃的公钥和一些相关信息一起加密，生成"数字证书"（Digital Certificate）。

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/bg12.png" height="100" />  

12. 鲍勃拿到数字证书以后，就可以放心了。以后再给苏珊写信，只要在签名的同时，再附上数字证书就行了。

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/bg13.png" height="100" />  

13. 苏珊收信后，用CA的公钥解开数字证书，就可以拿到鲍勃真实的公钥了，然后就能证明"数字签名"是否真的是鲍勃签的。

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/bg14.png" height="100" />  


本篇文章参考   http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html


