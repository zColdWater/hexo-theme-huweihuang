---
title: "[iOS] Swift — NSCoding"
catalog: true
toc_nav_num: true
date: 2019-09-18 17:47:30
subtitle: "NSCoding"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- iOS
catagories:
- iOS
---


``` Swift 

import UIKit

class Movie: NSObject, NSCoding {
    var name: String
    var category: String
    
    init(name: String, category: String){
        self.name = name
        self.category = category
    }
    
    required init?(coder aDecoder: NSCoder) {
        name = aDecoder.decodeObject(forKey: "name") as! String
        category = aDecoder.decodeObject(forKey: "category") as! String
    }
    
    func encode(with aCoder: NSCoder) {
        aCoder.encode(name, forKey: "name")
        aCoder.encode(category, forKey: "category")
    }
}

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        let movie = Movie(name: "name", category: "category")
        let movieData = NSKeyedArchiver.archivedData(withRootObject: movie)
        let movie1 = NSKeyedUnarchiver.unarchiveObject(with: movieData) as! Movie
        print(movie1.name)
        print(movie1.category)
    }

}

```

