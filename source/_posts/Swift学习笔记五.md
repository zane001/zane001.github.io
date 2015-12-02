title: "Swift学习笔记五"
date: 2015-11-07
tags: "swift"
categories: "编程"
---
### Project 11 Pachinko
1. SpriteKit 的point 0,0 表示某一个node的水平、竖直的中心点，Y坐标起始于底部，Y越大，在屏幕的位置越靠上。
didMoveToView() 类似于 viewDidLoad()
```
let background = SKSpriteNode(imageNamed: "background.jpg")
background.zPosition = -1        // behind everything else
background.blendMode = .Replace	// just draw it, ignoring any alpha values
addChild(background)
physicsBody = SKPhysicsBody(edgeLoopFromRect: frame) //给整个屏幕添加PhysicsBody
```
2. 在屏幕点击处绘制一个box，包括颜色、大小、physicsBody
```
override func touchesBegan(touches: Set<UITouch>, withEvent event: UIEvent?) {
      if let touch = touches.first {
          let location = touch.locationInNode(self)
          let box = SKSpriteNode(color: UIColor.redColor(), size: CGSize(width: 64, height: 64))
          box.position = location
          box.physicsBody = SKPhysicsBody(rectangleOfSize: CGSize(width: 64, height: 64))
          addChild(box)
      }
  }
```
3. 被碰撞后不会被移动
```
bouncer.physicsBody!.dynamic = false
```
4. 设置旋转动作，包括弧度、时间间隔。
```
let spin = SKAction.rotateByAngle(CGFloat(M_PI_2), duration: 10)	// 每隔10秒旋转90°
let spinForever = SKAction.repeatActionForever(spin)		// 永不停止
slotGlow.runAction(spinForever)
```
5. physicsBody的 contactTestBitMask 赋值给 collisionBitMask
```
physicsWorld.contactDelegate = self
ball.physicsBody!.collisionBitMask = ball.physicsBody!.contactTestBitMask
```
6. 将一个node移除 `SKNode.removeFromParent()`
7. 判断接触的bodyA和bodyB
```
func didBeginContact(contact: SKPhysicsContact) {	//override
    if contact.bodyA.node!.name == "ball" {
        collisionBetweenBall(contact.bodyA.node!, object: contact.bodyB.node!)
    } else if contact.bodyB.node!.name == "ball" {
        collisionBetweenBall(contact.bodyB.node!, object: contact.bodyA.node!)
    }
}
```
8. 使用 didSet 属性观察器
```
var scoreLabel: SKLabelNode!  		
var score: Int = 0 {
     didSet {
        scoreLabel.text = "Score: \(score)"
     }
}
let objects = nodesAtPoint(location) as [SKNode] // 获取到location的nodes
```
9. 使用 GKRandomDistribution 方法设置随机数的上限、下限
```
let size = CGSize(width: GKRandomDistribution(lowestValue: 16, highestValue: 128).nextInt(), height: 16)   //生成16-128的Int类型随机数
box.zRotation = RandomCGFloat(0, max: 3)  //随机的旋转，0-3的范围
```
10. 使用 SKEmitterNode 方法，火焰粒子效果
```
if let fireParticles = SKEmitterNode(fileNamed: "FireParticles") {
     fireParticles.position = ball.position
     addChild(fireParticles)
}
```
### Project 12 NSUserDefaults
1. as? 和 ?? 的用法
```
let array = defaults.objectForKey("SavedArray") as? [String] ?? [String]()
```
2. 使用 NSUserDefault 需要遵守 NSCoding 协议
```
required init(coder aDecoder: NSCoder) {	//  required表示必须要实现这个方法
    name = aDecoder.decodeObjectForKey("name") as! String   // 解密
    image = aDecoder.decodeObjectForKey("image") as! String
}
// 加密
func encodeWithCoder(aCoder: NSCoder) {
    aCoder.encodeObject(name, forKey: "name")
    aCoder.encodeObject(name, forKey: "image")
}
```
3. 将 array 类型转换为 NSData 类型。
```
func save() {
    //   converts array into an NSData object
    let savedData = NSKeyedArchiver.archivedDataWithRootObject(people)
    let defaults = NSUserDefaults.standardUserDefaults()
    defaults.setObject(savedData, forKey: "people")
}
```
4. 取出optional NSData，convert it back to an object graph
```
let defaults = NSUserDefaults.standardUserDefaults()
if let savedPeople = defaults.objectForKey("people") as? NSData {
     people = NSKeyedUnarchiver.unarchiveObjectWithData(savedPeople) as! [Person]
}
```
5. NSUserDefaults是不安全的，如果涉及私密信息，可以用keychain替代.
### Project 13 Instafilter
1. 使用 CIContext CIFilter
```
context = CIContext(options: nil)
currentFilter = CIFilter(name: “CISepiaTone")
```
2. 使用 CIImage
```
let beginImage = CIImage(image: currentImage)
currentFilter.setValue(beginImage, forKey: kCIInputImageKey)
```
3. 将 CGImage 转换为 UIImage
```
func applyProcessing() {
    currentFilter.setValue(intensity.value, forKey: kCIInputIntensityKey)
    let cgimg = context.createCGImage(currentFilter.outputImage!, fromRect: currentFilter.outputImage!.extent)
    let processedImage = UIImage(CGImage: cgimg)
    imageView.image = processedImage
}
```
4. `presentViewController(xxxxx, animated: true, completion: nil)` 把其中的xxxxx写成self导致运行时错误。
5. UIImagePickerControllerEditedImage 少写Picker导致import图片后，无法继续操作。
6. 先判断input keys 是否存在，然后判断是否包括 kCIInputIntensityKey
```
let inputKeys = currentFilter.inputKeys
if inputKeys.contains(kCIInputIntensityKey) {
     currentFilter.setValue(intensity.value, forKey: kCIInputIntensityKey)
}
```
7. 将 imageView 写入图片库
```
UIImageWriteToSavedPhotosAlbum(imageView.image!, self, "image:didFinishSavingWithError:contextInfo:", nil)
```
8. 写入图片库时的错误处理，使用 NSError，localizedDescription 显示具体的错误信息。
```
func image(image: UIImage!, didFinishSavingWithError error: NSError?, contextInfo: UnsafePointer<Void>) {

     let ac = UIAlertController(title: "Save error!", message: error?.localizedDescription, preferredStyle: .Alert)
     ac.addAction(UIAlertAction(title: "OK", style: .Default, handler: nil))
     presentViewController(ac, animated: true, completion: nil)
}
```
