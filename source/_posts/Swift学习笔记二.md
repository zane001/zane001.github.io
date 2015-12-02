title: "Swift学习笔记二"
date: 2015-11-02
tags: "swift"
categories: "编程"
---
### Project 1 & Project 2

1. 从Cell(而不是整个Table View)跳转到Detail View
在Storyboard里，用control 拖动建立IB时，需要在各自组件的Custom Class的Class添加关联，Cell选择相应的Cell
2. Could not insert new outlet connection ……
  1. 关闭xcode.
  2. 进入~/Library/Developer/XCode/DerivedData目录，删除名字为 工程名+一连串字母的文件夹
3. cp使用-R参数后可以复制文件或文件夹，不加-R参数只能复制文件
4. NSFileManager的使用
```
  let fm = NSFileManager.defaultManager()
  let path = NSBundle.mainBundle().resourcePath!
  let items = try! fm.contentsOfDirectoryAtPath(path)

  for item in items {
    if (item.hasPrefix("nssl")) {
        objects.append(item)
    }
  }
```
5. In iOS, sizes are measured in “points”, not pixels.
6. GameplayKit的使用，以及数组元素的随机排序，设置生成随机数的上限。
```
import GameplayKit
countries = GKRandomSource.sharedRandom().arrayByShufflingObjectsInArray(countries) as! [String]
correctAnswer = GKRandomSource.sharedRandom().nextIntWithUpperBound(3)
```
7. 3个button共用一个sender，使用不同的tag来区分
8. 设置button的宽度及颜色，UIColor位于上层，CGColor继承自UIColor
```
button.layer.borderWidth = 1
button.layer.borderColor = UIColor.lightGrayColor().CGColor
```
9. 弹窗提醒的代码
```
let ac = UIAlertController(title: title, message: "You score is \(score)", preferredStyle: .Alert)
ac.addAction(UIAlertAction(title: "Continue", style: .Default, handler: askQuestion))
presentViewController(ac, animated: true, completion: nil)
```
### Project 3 Social Media
1. import Social 引入社交包, 设置右上角的button
```
navigationItem.rightBarButtonItem = UIBarButtonItem(barButtonSystemItem: .Action, target: self, action: "shareTapped")
```
2. UIActivityViewController 分享功能，示例为调用Twitter
```
let vc = SLComposeViewController(forServiceType: SLServiceTypeTwitter)
vc.setInitialText("Look at this amazing picture!")
vc.addImage(detailImageView.image!)
vc.addURL(NSURL(string: “http://www.123.com/"))
presentViewController(vc, animated: true, completion: nil)
```
### Project 4 Easy Browser
1. 使用WebKit WebView
```
override func loadView() {
    webView = WKWebView()
    webView.navigationDelegate = self
    view = webView
}
```
2. 使用NSURL打开网址，允许返回
```
let url = NSURL(string: "https://m.baidu.com")!
webView.loadRequest(NSURLRequest(URL: url))
webView.allowsBackForwardNavigationGestures = true  打开为空白页
func openPage(action: UIAlertAction!)
```
3. 添加ToolBar,刷新键
```
let refresh = UIBarButtonItem(barButtonSystemItem: .Refresh, target: webView, action: "reload")
toolbarItems = [spacer, refresh]
navigationController?.toolbarHidden = false
```
4. 将UIProgressView包裹进UIBarButtonItem
```
progressView = UIProgressView(progressViewStyle: .Default)
progressView.sizeToFit()
let progressButton = UIBarButtonItem(customView: progressView)
```
5. KVO (Key-Value Observer)
```
   webView.addObserver(self, forKeyPath: "estimatedProgress", options: .New, context: nil)
	 override func observeValueForKeyPath(......) {
    ......
}
```
6. 阻止用户访问某些链接，使用rangeOfString方法
```
  func webView(webView: WKWebView, decidePolicyForNavigationAction…) {
      let url = navigationAction.request.URL

      if let host = url!.host {
          for website in websites {
              if host.rangeOfString(website) != nil {
                  decisionHandler(.Allow)
                  return
              }
          }
      }
      decisionHandler(.Cancel)
}
```
### Project 5 Word Scramble
1. 寻找文本路径、载入文本、处理文本
```
 if let startWordsPath = NSBundle.mainBundle().pathForResource("start", ofType: "txt") {
      if let startWords = try? String(contentsOfFile: startWordsPath, usedEncoding: nil) 	{
             allWords = startWords.componentsSeparatedByString("\n")
      }

      } else {
         allWords = [“silkworm"]      
}
```
2. 清空、重载TableView数据
```
objects.removeAll(keepCapacity: true)
tableView.reloadData()
```
3. 无主引用 (an unowned reference is one you’re certifying cannot be nil and so doesn’t need to be unwrapped.)
```
let ac = UIAlertController(title: "Enter answer", message: nil, preferredStyle: .Alert)
ac.addTextFieldWithConfigurationHandler(nil)
let submitAction = UIAlertAction(title: "Submit", style: .Default) { [unowned self, ac](action: UIAlertAction!) in
    let answer = ac.textFields![0]
    self.submitAnswer(answer.text!)
}
ac.addAction(submitAction)
presentViewController(ac, animated: true, completion: nil)
```
4. 仅插入指定的行到table view，而不是整个reload
```
objects.insert(lowerAnswer, atIndex: 0)
let indexPath = NSIndexPath(forRow: 0, inSection: 0)
tableView.insertRowsAtIndexPaths([indexPath], withRowAnimation: .Automatic)
tempWord.removeAtIndex() 移除并且返回该元素
```
5. 检查单词是否是一个正确的单词，使用NSMakeRange
```
func wordIsReal(word: String) -> Bool {
    let checker = UITextChecker()
    let range = NSMakeRange(0, word.characters.count)
    let misspelledRange = checker.rangeOfMisspelledWordInString(word, range: range, startingAt: 0, wrap: false, language: "en")

    return misspelledRange.location == NSNotFound
}
```
6. !objects.contains(word) 报错，待修改
```
Cannot convert value of type 'String' to expected argument type '@noescape (AnyObject) throws -> Bool’
```
### Project 6 Auto Layout
1. 隐藏状态栏
```
override func prefersStatusBarHidden() -> Bool {
    return true
}
```
2. 关闭自动布局 `label.translatesAutoresizingMaskIntoConstraints = false`
3. Auto Layout Visual Format Language (VFL)
 1. H表示水平方向 The pipe symbol | means “the edge of the view.”
 ```
for label in viewsDictionary.keys {
     view.addConstraints(NSLayoutConstraint.constraintsWithVisualFormat("H:|[\(label)]|", options: [], metrics: ["labelHeight": 88], views: viewsDictionary))
}
```
 2. V表示竖直方向 -symbol, which means “space”. It’s 10 points by default
```
"V:|[label1(labelHeight@999)]-[label2(label1)]-[label3(label1)]-[label4(label1)]-[label5(label1)]-(>=10)-|"
```
4. TL;DR (Too Long, Didn’t Read) 太长不看
