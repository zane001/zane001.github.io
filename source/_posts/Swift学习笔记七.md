title: "Swift学习笔记七"
date: 2015-11-30
tags: "swift"
categories: "编程" 
---
### Project 19  Capital Cities
1. 将 view controller 设置为 map view的代理：Ctrl-dragging from the map view to the orange and white view controller button just above the layout area
2. 使用地图的 Annotation
```
import MapKit
import UIKit

class Capital: NSObject, MKAnnotation {
    var title: String?
    var coordinate: CLLocationCoordinate2D
    var info: String
    
    init(title: String, coordinate: CLLocationCoordinate2D, info: String) {
        self.title = title
        self.coordinate = coordinate
        self.info = info
    }
}
mapView.addAnnotations([london, oslo, paris, rome, washington])
```
3. 复用AnnotationView
```
func mapView(mapView: MKMapView!, viewForAnnotation annotation: MKAnnotation!) -> MKAnnotationView! {
        let identifier = "Capital"
        if annotation.isKindOfClass(Capital.self) {
            var annotationView = mapView.dequeueReusableAnnotationViewWithIdentifier(identifier)
            if annotationView == nil {
                annotationView = MKPinAnnotationView(annotation: annotation, reuseIdentifier: identifier)
                annotationView!.canShowCallout = true
                let btn = UIButton(type: .DetailDisclosure)
                annotationView!.rightCalloutAccessoryView = btn
            } else {
                annotationView!.annotation = annotation
            }
            return annotationView
        }
        return nil
    }
```
4. 
```
func mapView(mapView: MKMapView!, annotationView view: MKAnnotationView!, calloutAccessoryControlTapped control: UIControl!) {
        let captial = view.annotation as! Capital
        let placeName = captial.title
        let placeInfo = captial.info
        
     }
```
### Project 21 Local Notifications
1. 注册Notifications
```
@IBAction func registerLocal(sender: AnyObject) {
        let notificationSettings = UIUserNotificationSettings(forTypes: [.Alert, .Badge, .Sound], categories: nil)
        UIApplication.sharedApplication().registerUserNotificationSettings(notificationSettings)
    }
```
2. 定时执行Notification
```
@IBAction func scheduleLocal(sender: AnyObject) {
        
        guard let settings = UIApplication.sharedApplication().currentUserNotificationSettings() else { return }
        if settings.types == .None {
            let ac = UIAlertController(title: "Can't schedule", message: "permission", preferredStyle: .Alert)
            ac.addAction(UIAlertAction(title: "OK", style: .Default, handler: nil))
            presentViewController(ac, animated: true, completion: nil)
            return
        }
        
        let notification = UILocalNotification()
        notification.fireDate = NSDate(timeIntervalSinceNow: 5)
        notification.alertBody = "Hey man, swipe to unlock!"
        notification.alertAction = "WeChat"
        notification.soundName = UILocalNotificationDefaultSoundName
        notification.userInfo = ["CustomField1": "w00t"]
        UIApplication.sharedApplication().scheduleLocalNotification(notification)
    }
```
3. didFinishLaunchingWithOptions
```
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        if let options = launchOptions {
            if let notification = options[UIApplicationLaunchOptionsLocalNotificationKey] as? UILocalNotification {
                if let userInfo = notification.userInfo {
                    let customField1 = userInfo["CustomField1"] as! String
                    print("didFinishLaunchingWithOptions: \(customField1)")
                }
            }
        }
        return true
    }
  ``` 
4. didFinishLaunchingWithOptions
```
    func application(application: UIApplication, didReceiveLocalNotification notification: UILocalNotification) {
        if let userInfo = notification.userInfo {
            let customField1 = userInfo["CustomField1"] as! String
            print("didReceiveLocalNotification: \(customField1)")
        }
    }
```
### Project 24 Swift Extensions
1. static means: do you want to call this method on the type itself, or do you want to call this on an instance of the type?
```
extension Int {
    mutating func plusOne() {
        ++self
    }
static func random(min min: Int, max: Int) -> Int {
       return 0
    }
}

```
2. *a method belongs to a particular class, struct or enum. Functions, however, are global beasts: they don't belong to a particular class, so they can be called anywhere.*
3. 如果经常要用到去除字符串的空格，可以使用extension
命名为String+Additions.swift
```
extension String {
    mutating func trim() {
        self = stringByTrimmingCharactersInSet(NSCharacterSet.whitespaceAndNewlineCharacterSet())
    }
}
```
### 11-18
 iBYR  北邮人论坛iOS客户端Swift实现
>设计界面，包括十大、分区、收藏、用户4个Tab Bar，申请开放平台的app key.

### 11-19
继续丰富界面，添加一些tab view cell的跳转

### 11-21
遵守UIWebViewDelegate协议，但忘记写webView.delegate = self

### 11-22 Getting Started with Core Data Tutorial
1. Ctrl-drag from the table view to the yellow view controller icon, click on dataSource
2. override func viewDidLoad() {
        super.viewDidLoad()
        title = "\"The List\""
        tableView.registerClass(UITableViewCell.self, forCellReuseIdentifier: "Cell")
    }
3. // MARK:  加标记
```
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  numberOfRowsInSection ~~~~~~~~~~~~~~~~~~~~~~    
func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCellWithIdentifier("Cell")
        let person = people[indexPath.row]
        cell!.textLabel!.text = person.valueForKey("name") as? String
        return cell!
    }
```
4. 显示TextField 的输入框
```
  alert.addTextFieldWithConfigurationHandler { (textField: UITextField) -> Void in
  
  }    
```
5. 添加数据，重载tableView的数据
```
  let textField = alert.textFields!.first
  self.names.append(textField!.text!)
  self.tableView.reloadData()   
```
6. 存数据
```
func saveName(name: String) {
        let appDelegate = UIApplication.sharedApplication().delegate as! AppDelegate
        let managedContext = appDelegate.managedObjectContext
        
        let entity = NSEntityDescription.entityForName("Person", inManagedObjectContext: managedContext)
        let person = NSManagedObject(entity: entity!, insertIntoManagedObjectContext: managedContext)
        
        person.setValue(name, forKey: "name")
        
        do {
            try managedContext.save()
            people.append(person)
        } catch let error as NSError {
            print("Could not save \(error), \(error.userInfo)")
        }
    }
```
7. 取数据
```
  override func viewWillAppear(animated: Bool) {
        let appDelegate = UIApplication.sharedApplication().delegate as! AppDelegate
        let managedObjectContext = appDelegate.managedObjectContext
        
        let fetchRequest = NSFetchRequest(entityName: "Person")
        
        do {
            let results = try managedObjectContext.executeFetchRequest(fetchRequest)
            people = results as! [NSManagedObject]
        } catch let error as NSError {
            print("Could not fetch \(error), \(error.userInfo)")
        }
    }
```
8.  tableView 返回true表示可以编辑
```
  func tableView(tableView: UITableView, canEditRowAtIndexPath indexPath: NSIndexPath) -> Bool {
        return true
  }
```