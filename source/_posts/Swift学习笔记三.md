title: "Swift学习笔记三"
date: 2015-11-04
tags: "swift"
categories: "编程" 
---
### Project 7 Whitehouse Petitions
1. 使用NSURL、NSData
```
if let url = NSURL(string: urlString) {
    if let data = try? NSData(contentsOfURL: url, options: []) {
        let json = JSON(data: data)
        
        if json["metadata"]["responseInfo"]["status"] == 200 {
            print("200")
        }
    }
    
}
```
2. 使用开源SwiftyJSON，解析JSON字符串
```
func parseJSON(json: JSON) {
    for result in json["results"].arrayValue {
        let title = result["title"].stringValue
        let body = result["body"].stringValue
        let sigs = result["signatureCount"].stringValue
        let object = ["title": title, "body": body, "sigs": sigs]
        objects.append(object)
    }
    tableView.reloadData()
}
```
3. “NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9802)”
解决办法：在Info.plist里添加
```
<key>NSAppTransportSecurity</key>
  <dict>
  <key>NSExceptionDomains</key>
  <dict>
    <key>whitehouse.gov</key>
    <dict>
       <key>NSIncludesSubdomains</key>
       <true/>
       <key>NSThirdPartyExceptionAllowsInsecureHTTPLoads</key>
       <true/>
    </dict>
  </dict>
</dict>
```
4. 在cellForRowAtIndexPath方法里，显示标题、内容，这里使用默认Cell样式。
```
cell.textLabel!.text = object["title"]
cell.detailTextLabel!.text = object[“body"]
```
5. 将webView设置为当前view，载入html网页
```
import WebKit
override func loadView() {
    webView = WKWebView()
    view = webView
}
webView.loadHTMLString(html, baseURL: nil)
```
6. 使用guard保证detailItem不为空，否则返回
```
guard detailItem != nil else { return }
```
7. 使用当前main的storyboard，代码添加一个TabBar，使用系统默认的tabBarSystemItem
```
let tabBarController = splitViewController.viewControllers[0] as! UITabBarController
let storyboard = UIStoryboard(name: "Main", bundle: nil)
let vc = storyboard.instantiateViewControllerWithIdentifier("NavController") as! UINavigationController
vc.tabBarItem = UITabBarItem(tabBarSystemItem: .TopRated, tag: 1)
tabBarController.viewControllers?.append(vc)
```
8. 判断TabBarItem的标签 navigationController?.tabBarItem.tag == 0
9. 有if的地方，记得考虑else的情况。
### Project 8 Seven Swifty Words
1. 可以取消 Use Size Classes，手动指定一个size
2. 默认TextField无法设置高度， that’s because it has a rounded rectangle border around it that must be an exact size. 需要设置borderless
3. 将Label的Lines置为0，表示可以有多行
4. 遍历view里面的子view的tag，将button和action建立连接，其中letterTapped: 冒号表示接受一个参数
```
for subview in view.subviews where subview.tag == 1001 {
   let btn = subview as! UIButton
   letterButtons.append(btn)
   btn.addTarget(self, action: "letterTapped:", forControlEvents: .TouchUpInside)
}
```
5. 使用enumerate进行遍历，使用componentsSeparatedByString字符串分割
```
for(index, line) in lines.enumerate() {
    let parts = line.componentsSeparatedByString(": ")
    let answer = parts[0]
    let clue = parts[1]
    ..
}
```
6. 字符串替换
```
answer.stringByReplacingOccurrencesOfString("|", withString: “")
```
7. 字符统计 solutionWord.characters.count
8. 字符串去除空格、换行符
```
clueString.stringByTrimmingCharactersInSet(.whitespaceAndNewlineCharacterSet())
```
9. 循环 注意 ..< 是一个整体，之间没有空格
```
for i in 0 ..< letterBits.count
```
10. 获得button的titleLabel，hidden为true表示不可点击
```
btn.titleLabel!.text!
activatedButtons.append(btn)
btn.hidden = true
```
11. 字符串拼接 joinWithSeparator(“\n”)
12. 属性观察器 Property Observers，使用didSet
```
var score = 0 {
    didSet {
        scoreLabel.text = "Score: \(score)"
    }
}
```
### Project 9 Grand Central Dispatch
1. 所有的UI工作都在主线程发生，dispatch_get_main_queue获得主线程。
it’s never OK to do user interface work on the background thread.
2. QoS level set，线程优先级。
  1. User Interactive 
  2. User Initiated 
  3. The Utility queue
  4. The Background queue
3. 让代码后台执行，Qos为User Initiated，使用unowned self无主引用，避免强引用。
```
dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0)) { [unowned self] in
在闭包里的方法必须使用self，否则报错
```
4. Note that because our code is now inside a closure, we need to prefix our method calls with self. otherwise Swift complains.