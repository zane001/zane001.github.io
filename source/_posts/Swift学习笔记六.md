title: "Swift学习笔记六"
date: 2015-11-15
tags: "swift"
categories: "编程" 
---
### Project 14 Whack a Penguin
1. **..<** 误写成 **<..**
2. 使用 SKCropNode 显示、隐藏图片
```
let cropNode = SKCropNode()
cropNode.maskNode = nil 	// 都显示
cropNode.maskNode = SKSpriteNode(imageNamed: "whackMask")
```
3. 使用 SKAction、SKTexture
```
charNode.runAction(SKAction.moveByX(0, y: 80, duration: 0.05))
charNode.texture = SKTexture(imageNamed: “penguinGood")
```
4. 第一个 hideTime 为外部变量，第二个为内部变量，使用时必须带外部参数，show(hideTime: 1)，参数的含义比较明显。
```
func show(hideTime hideTime: Double) {…}
```
5. 使用 dispatch_time 、dispatch_after，当前时间为 DISPATCH_TIME_NOW
```
func runAfterDelay(delay: NSTimeInterval, block: dispatch_block_t) {
	let time = dispatch_time(DISPATCH_TIME_NOW, Int64(delay * Double(NSEC_PER_SEC)))	// 在秒和纳秒之间转换
	dispatch_after(time, dispatch_get_main_queue(), block)
}
```
6. 使用 SKAction 的 waitForDuration/runBlock/sequence 方法，其中 sequence 是一系列的 action
```
func hit() {
    isHit = true
    let delay = SKAction.waitForDuration(0.25)
    let hide = SKAction.moveByX(0, y: -80, duration: 0.5)
    let notVisible = SKAction.runBlock { [unowned self] in
        self.visible = false
    }
    charNode.runAction(SKAction.sequence([delay, hide, notVisible]))
}
```
7. 使用 SKAction 的 playSoundFileNamed 方法播放音乐。
```
let whackSlot = node.parent!.parent as! WhackSlot
SKAction.playSoundFileNamed
whackSlot.charNode.xScale = 0.85 // x坐标方向缩小或者放大
```
### Project 15 Animation
1. Ctrl-drag 沿对角线，可以给自身添加约束。
2. 使用 UIImageView，包括一个 UIImage
```
var imageView: UIImageView!
imageView = UIImageView(image: UIImage(named: “penguin”))
imageView.center = CGPoint(x: 512, y: 384)
view.addSubview(imageView)
```
3. 默认的动画是 ease-in-ease-out animation
```
UIView.animateWithDuration(1, delay: 0, usingSpringWithDamping: 0.5, initialSpringVelocity: 5, options: [], 闭包) 尾随闭包
```
4. 动画的移动、旋转
```
self.imageView.transform = CGAffineTransformMakeScale(2, 2)
CGAffineTransformIdentity 	//恢复原状
self.imageView.transform = CGAffineTransformMakeTranslation(-256, -256)  // 移动，从当前位置减去256
self.imageView.transform = CGAffineTransformMakeRotation(CGFloat(M_2_PI)) 	// 旋转90度，总是找最短路径旋转
self.imageView.alpha = 0.1  // 透明度
```
### Project 16 JavaScript Injection
1. StoryBoard上的实黄线：”this is where you view is,” 虚黄线： “this is where your view will be when your code runs.”
2. 给应用添加扩展，新建 iOS > Application Extension > Action Extension
```
if let inputItem = extensionContext!.inputItems.first as? NSExtensionItem {
    if let itemProvider = inputItem.attachments?.first as? NSItemProvider {
         itemProvider.loadItemForTypeIdentifier(kUTTypePropertyList as String, options: nil, completionHandler: { [unowned self] (dict, error)in
         let itemDirectory = dict as! NSDictionary
         let javaScriptValues = itemDirectory[NSExtensionJavaScriptPreprocessingResultsKey] as! NSDictionary
         print(javaScriptValues)
         })
     }
}
```
3. 在 info.plist 添加 JavaScript 支持。
```
NSExtension > NSExtensionAttributes > NSExtensionActivationRule > NSExtensionActivationSupportsWebPageWithMaxCount
NSExtension > NSExtensionAttributes > NSExtensionJavaScriptPreprocessingFile
```
4. 处理 JavaScript
```
@IBAction func done() {
    let item = NSExtensionItem()
    let webDictionary = [NSExtensionJavaScriptPreprocessingResultsKey: ["customJavaScript": script.text]]
    let customJavaScript = NSItemProvider(item: webDictionary, typeIdentifier: kUTTypePropertyList as String)
    item.attachments = [customJavaScript]
    extensionContext!.completeRequestReturningItems([item], completionHandler: nil)
}
```
5. 添加键盘的监听器
```
let notificationCenter = NSNotificationCenter.defaultCenter()
notificationCenter.addObserver(self, selector: "adjustForKeyboard", name: UIKeyboardWillHideNotification, object: nil)
notificationCenter.addObserver(self, selector: "adjustForKeyboard", name: UIKeyboardWillChangeFrameNotification, object: nil)
```
6. 自动调整键盘位置
```
func adjustForKeyboard(notification: NSNotification) {
      let userInfo = notification.userInfo!
      
      let keyboardScreenEndFrame = (userInfo[UIKeyboardFrameEndUserInfoKey] as! NSValue).CGRectValue()
      let keyboardViewEndFrame = view.convertRect(keyboardScreenEndFrame, fromView: view.window)
      
      if notification.name == UIKeyboardWillHideNotification {
          script.contentInset = UIEdgeInsetsZero
      } else {
          script.contentInset = UIEdgeInsets(top: 0, left: 0, bottom: keyboardViewEndFrame.height, right: 0)
      }
      
      script.scrollIndicatorInsets = script.contentInset  // 不随view的滚动而滚动
      
      let selectedRange = script.selectedRange
      script.scrollRangeToVisible(selectedRange)
}
```
### Project 17 Swifty Ninja
1. 使用 physicsWorld 的 gravity、speed 属性
```
physicsWorld.gravity = CGVector(dx: 0, dy: -6)
physicsWorld.speed = 0.85
SKShapeNode 的
touchesBegan/touchesMoved/touchesEnded/touchesCancelled 方法。
```
2. 使用 guard let ，否则 return，使用 SKAction 的 fadeOutWithDuration 方法。
```
guard let touch = touches.first else {
     return
}
activeSliceBG.runAction(SKAction.fadeOutWithDuration(0.25))  //逐渐消失
activeSliceFG.removeAllActions()
```
3. 使用 UIBezierPath
```
let path = UIBezierPath()
path.moveToPoint(activeSlicePoints[0])
for (var i=1; i<activeSlicePoints.count; ++i) {
    path.addLineToPoint(activeSlicePoints[i])
}
activeSliceBG.path = path.CGPath	//将 UIBezierPath 转换为 CGPath
```
4. 先创建 action，然后 runAction(action)
```
let swooshSound = SKAction.playSoundFileNamed(soundName, waitForCompletion: true)
runAction(swooshSound) { [unowned self] in
     self.swooshSoundActive = false
}
```
5. 创建枚举类，使用 SKPhysicsBody，CGVector 中属性为相对值
```
enum ForceBomb {
    case Never, Always, Default
}
  func createEnemy(forceBomb forceBomb: ForceBomb = .Default) {
  	enemy.physicsBody = SKPhysicsBody(circleOfRadius: 64)
    enemy.physicsBody!.velocity = CGVector(dx: randomXVelocity * 40, dy: randomYVelocity * 40)
    enemy.physicsBody!.angularVelocity = randomAngularVelocity
    enemy.physicsBody!.collisionBitMask = 0
  }
 ```
6. 使用 AVAudioPlayer 播放音乐
```
import AVFoundation
  let path = NSBundle.mainBundle().pathForResource("sliceBombFuse", ofType: nil)!
  let url = NSURL(fileURLWithPath: path)
  let sound = try! AVAudioPlayer(contentsOfURL: url)
  bombSoundEffect = sound
  sound.play()
```
7. 将枚举类型映射为Int类型，然后可以使用 rawValue
```
enum SequenceType: Int {
    case OneNoBomb, One, TwoWithOneBomb, Two, Three, Four, Chain, FastChain
}
    let nextSequence = SequenceType(rawValue: Int(RandomInt(2, max: 7)))!  //使用rawValue需要强制解包
```
8. 使用 SKAction 的 group、sequence
```
let scaleOut = SKAction.scaleTo(0.001, duration: 0.02)
let fadeOut = SKAction.fadeOutWithDuration(0.2)
let group = SKAction.group([scaleOut, fadeOut])
let seq = SKAction.sequence([group, SKAction.removeFromParent()])
node.runAction(seq)
```
9. userInteractionEnabled 是否停止用户交互
```
physicsWorld.speed = 0
userInteractionEnabled = false
```
### Project 18 iAd and Debugging
1. ADBannerView 的使用，设置约束
```
import iAd      
  var bannerView: ADBannerView!
  bannerView = ADBannerView(adType: .Banner)
  bannerView.translatesAutoresizingMaskIntoConstraints = false
  bannerView.delegate = self
  bannerView.hidden = true
  view.addSubview(bannerView)
  let viewsDictionary = ["bannerView": bannerView]
  view.addConstraints(NSLayoutConstraint.constraintsWithVisualFormat("H:|[bannerView]|", options: [], metrics: nil, views: viewsDictionary))
  view.addConstraints(NSLayoutConstraint.constraintsWithVisualFormat("W:|[bannerView]|", options: [], metrics: nil, views: viewsDictionary))
```
2. AppDelegate means that we can easily use it to store app-wide information. 获得 AppDelegate 的引用
```
let appDelegate = UIApplication.sharedApplication().delegate as! AppDelegate
view.addSubview(appDelegate.bannerView)
```
3. 上传到App Store时，Xcode自动将 assert 禁用掉
4. 可以在断点上 Edit Breakpoint，可以在 breakpoint navigator 里添加 Exceptions
5. 调试 Debug > View Debugging > Show View Frames / Capture View Hierarchy