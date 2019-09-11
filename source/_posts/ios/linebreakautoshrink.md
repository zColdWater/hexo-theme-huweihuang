---
title: "[iOS] LineBreak & AutoShrink 使用"
catalog: true
toc_nav_num: true
date: 2019-09-11 23:00:30
subtitle: "LineBreak & AutoShrink"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/naked.jpg"
tags:
- iOS
catagories:
- iOS
---


# Line Break

## Clip
(default) lable中过长的文字，直接裁掉后方的文字

## Character Wrap
斷字的方式， lable顯示多行時，每行最後是以「字母」為單位來斷行，僅適用英文

## Word Wrap
斷字的方式， lable顯示多行時，每行最後是以「單字」為單位來斷行，僅適用英文

## Truncate Head
裁掉前方的文字，以 … 代替

## Truncate Middle
裁掉中間的文字，以 … 代替

## Truncate Tail
裁掉後方的文字，以 … 代替 (例如要顯示過長的網址，用這個)


效果測試:  
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/linebreak.png" height="150" />

# Auto shrink

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/autoshrink1.png" height="150" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/autoshrink2.png" height="150" />

## Fixed Font Size
(default) 依照以設定好的字型規格來顯示 label文字(當文字超過 Lable寬度時 會以上面Lin Break 的指定)

## Minimum Font Scale
當要顯示的文字太長時，系統會將文字縮小力求全部顯示，至於要縮到多小 在此輸入一個字型比例值，1~0.x

## Minium Font Size
當要顯示的文字太長時，系統會將文字縮小力求全部顯示，至於要縮到多小 在此輸入一個font size最小值，如 9

效果測試:   
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/autoshrink.png" height="150" />



参考:  
[彼得潘的开发教室 ](https://medium.com/%E5%BD%BC%E5%BE%97%E6%BD%98%E7%9A%84-swift-ios-app-%E9%96%8B%E7%99%BC%E6%95%99%E5%AE%A4/%E4%BD%9C%E6%A5%AD-2-line-break-auto-shrink-204c715c1ad8)
