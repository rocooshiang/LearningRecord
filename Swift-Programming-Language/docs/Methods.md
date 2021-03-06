# Methods
<br \>
<br \>

## Instance Methods
##### Modifying Value Types from Within Instance Methods
Structures和Enumerations是Value types，預設是不能使用他內部的方法去變動properties的值，如果想要這麼做的話，要在func前加上關鍵字**mutating**，且該物件不能被宣告為constant，即使要變動的properties是定義為variable的type，：
```swift
struct Point {
  var x = 0.0, y = 0.0

  mutating func moveByX(deltaX: Double, y deltaY: Double) {
    x += deltaX
    y += deltaY
  }

}

var somePoint = Point(x: 1.0, y: 1.0)
somePoint.moveByX(2.0, y: 3.0)
print("The point is now at (\(somePoint.x), \(somePoint.y))")
// Prints "The point is now at (3.0, 4.0)"

let fixedPoint = Point(x: 3.0, y: 3.0)
fixedPoint.moveByX(2.0, y: 3.0)
// this will report an error
```
<br \>
<br \>
##### Assigning to self Within a Mutating Method
在mutating method可以使用隱含的屬性**self**去assign整個實例：
```swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveByX(deltaX: Double, y deltaY: Double) {
        self = Point(x: x + deltaX, y: y + deltaY)
    }
}
```

在Enumerations的mutating methods可以在不同case使用同樣的隱含屬性**self**：
```swift
enum TriStateSwitch {
    case Off, Low, High
    mutating func next() {
        switch self {
        case Off:
            self = Low
        case Low:
            self = High
        case High:
            self = Off
        }
    }
}
var ovenLight = TriStateSwitch.Low
ovenLight.next()
// ovenLight is now equal to .High

ovenLight.next()
// ovenLight is now equal to .Off
```

<br \>
<br \>
## Type Methods
* 使用**static**關鍵字的methods，可以讓繼承該類別的子類別Override
* Class也可以使用關鍵字**class**來取代static，是一樣的效果

***Note: 在Objective-C時宣告為static的方法，該類別只能被Classes繼承，而在Swift可以被Structures、Enumerations和Classes繼承***

使用static methods只要使用types點語法就可以了：
```swift
class SomeClass {
    class func someTypeMethod() {
        // type method implementation goes here
    }
}

SomeClass.someTypeMethod()
```

* type methods的內部隱含屬性self是參考type本身(SomeClase)，而不是type的實例(new SomeClass())
* type methods內可以直接使用type的properties與methods，不需要在前面加上type name

以下例子highestUnlockedLevel在type methods裡被使用時，不需要在前面加上type name：
```swift
struct LevelTracker {
  static var highestUnlockedLevel = 1
  
  static func unlockLevel(level: Int) {
    if level > highestUnlockedLevel { highestUnlockedLevel = level }
  }
  
  static func levelIsUnlocked(level: Int) -> Bool {
    return level <= highestUnlockedLevel
  }
  
  var currentLevel = 1
  
  mutating func advanceToLevel(level: Int) -> Bool {
    
    if LevelTracker.levelIsUnlocked(level) {
      currentLevel = level
      
      return true
    } else {
      
      return false
    }
  }
}
```

以下分別建立了兩個player，而裡面使用到的property是一個type property，所以可以共用：
```swift
class Player {
  var tracker = LevelTracker()
  let playerName: String
  func completedLevel(level: Int) {
    LevelTracker.unlockLevel(level + 1)
    tracker.advanceToLevel(level + 1)
  }
  init(name: String) {
    playerName = name
  }
}

var player = Player(name: "Argyrios")
player.completedLevel(6)

print("highest unlocked level is now \(LevelTracker.highestUnlockedLevel)")
// Prints "highest unlocked level is now 6"

player = Player(name: "Beto")
if player.tracker.advanceToLevel(6) {
  print("player is now on level 6")
} else {
  print("level 6 has not yet been unlocked")
}
// Prints "player is now on level 6"
```
