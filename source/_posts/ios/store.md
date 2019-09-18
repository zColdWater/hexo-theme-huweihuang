---
title: "[iOS] UserDefault"
catalog: true
toc_nav_num: true
date: 2019-09-18 10:49:30
subtitle: "正确使用UserDefault"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/camper.jpg"
tags:
- iOS
catagories:
- iOS
---

# UserDefaults

> UserDefaults是一个轻量级的持久化存储内嵌工具， 简单了解 UserDefaults 用法 

**NSUserDefaults支持的数据类型有：**

* `NSNumber（NSInteger、float、double)`  
* `NSString`  
* `NSData`  
* `NSArray`   
* `NSDictionary`  
* `BOOL`    


### 使用

``` swift
/** --- String 类型 --- */
UserDefaults.standard.set("Node", forKey: "name")
let name = UserDefaults.standard.string(forKey: "name")
print("name:\(String(describing: name))")

/** --- URL 类型 --- */
UserDefaults.standard.set(URL(string: "https://host/path"), forKey: "url")
let url = UserDefaults.standard.url(forKey: "url")
print("url:\(String(describing: url))")

/** --- Bool 类型 --- */
UserDefaults.standard.set(true, forKey: "true")
let `true` = UserDefaults.standard.bool(forKey: "true")
print("true:\(`true`)")

/** --- Double 类型 --- */
UserDefaults.standard.set(Double(1.0), forKey: "double")
let double = UserDefaults.standard.bool(forKey: "double")
print("double:\(double)")

/** --- Float 类型 --- */
UserDefaults.standard.set(Float(1.0), forKey: "float")
let float = UserDefaults.standard.bool(forKey: "float")
print("float:\(float)")

/** --- Int 类型 --- */
UserDefaults.standard.set(Int(1), forKey: "int")
let int = UserDefaults.standard.bool(forKey: "int")
print("int:\(int)")

/** --- Array 类型 --- */
UserDefaults.standard.set(["1","2","3"], forKey: "array")
let array = UserDefaults.standard.array(forKey: "array")
print("array:\(String(describing: array))")

/** --- Dictionary 类型 --- */
UserDefaults.standard.set(["key":"value"], forKey: "dic")
let dic = UserDefaults.standard.dictionary(forKey: "dic")
print("dic:\(String(describing: dic))")

/** --- Data 类型 --- */
let str = "Data"
let data = str.data(using: .utf8)
UserDefaults.standard.set(data, forKey: "data")
guard let d = UserDefaults.standard.data(forKey: "data") else { return }
print("data:\(String(describing: String.init(data: d, encoding: .utf8)))")
```




