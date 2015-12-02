title: "Swift学习笔记四"
date: 2015-11-05
tags: "swift"
categories: "编程" 
---
### Project 10 Names to Faces
1. 把 collection view 直接拖拽到 view controller，它就能自动占满整个空间。
2. collection view cell 的背景默认为透明，可以改为白色（错误的选成了imageView,导致显示不正常）
3. 把collectionView的DataSource、Delegate设置为ViewController，需要conform协议
```
class ViewController: UIViewController, UICollectionViewDataSource, UICollectionViewDelegate {
  func collectionView(collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
     return people.count
}

 func collectionView(collectionView: UICollectionView, cellForItemAtIndexPath indexPath: NSIndexPath) -> UICollectionViewCell {
   let cell = collectionView.dequeueReusableCellWithReuseIdentifier("Person", forIndexPath: indexPath) as! PersonCell
   return cell
 }
 ```
4. 使用UIImagePickerController必须同时遵守 UIImagePickerControllerDelegate, UINavigationControllerDelegate协议。
```
func addNewPerson() {
  let picker = UIImagePickerController()
  picker.allowsEditing = true
  picker.delegate = self
  presentViewController(picker, animated: true, completion: nil)
}

func imagePickerControllerDidCancel(picker: UIImagePickerController) {
  dismissViewControllerAnimated(true, completion: nil)
}

func imagePickerController(picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : AnyObject]) {
  var newImage: UIImage

  if let possibleImage = info["UIImagePickerControllerEditedImage"] as? UIImage {
    newImage = possibleImage
  } else if let possibleImage = info["UIImagePickerControllerOriginalImage"] as? UIImage {
    newImage = possibleImage
  } else {
    return
  }

  let imageName = NSUUID().UUIDString
  let imagePath = getDocumentsDirectory().stringByAppendingPathComponent(imageName)

  if let jpegData = UIImageJPEGRepresentation(newImage, 80) {
    jpegData.writeToFile(imagePath, atomically: true)
  }

  dismissViewControllerAnimated(true, completion: nil)
}
```
5. 得到文件的路径
```
func getDocumentsDirectory() -> NSString {
  let paths = NSSearchPathForDirectoriesInDomains(.DocumentDirectory, .UserDomainMask, true)
  let documentsDirectory = paths[0]
  return documentsDirectory
}
```
6. String! and String? 可以为nil, 但是 String 必须有 value。
```
var name: String
init(name: String, image: String) {
self.name = name
self.image = image //can only mean one thing: assign the parameter to the class's property.
}
```
7. 使用reloadData()方法更新数据。
```
var people = [Person]()
let person = Person(name: "Unknown", image: imageName)
people.append(person)
collectionView.reloadData()
```
8. 设计cell的边界颜色、宽度、圆角
```
func collectionView(collectionView: UICollectionView, cellForItemAtIndexPath indexPath: NSIndexPath) -> UICollectionViewCell {
  let cell = collectionView.dequeueReusableCellWithReuseIdentifier("Person", forIndexPath: indexPath) as! PersonCell

  let person = people[indexPath.item]

  cell.name.text = person.name

  let path = getDocumentsDirectory().stringByAppendingPathComponent(person.image)
  cell.imageView.image = UIImage(contentsOfFile: path)
  cell.imageView.layer.borderColor = UIColor(red: 0, green: 0, blue: 0, alpha: 0.3).CGColor
  cell.imageView.layer.borderWidth = 2
  cell.imageView.layer.cornerRadius = 3
  cell.layer.cornerRadius = 7

  return cell
}
```
9. 在UIAlertController里传值，使用addTextFieldWithConfigurationHandler(nil)方法。
```
func collectionView(collectionView: UICollectionView, didSelectItemAtIndexPath indexPath: NSIndexPath) {
  let person = people[indexPath.item]

  let ac = UIAlertController(title: "Rename person", message: nil, preferredStyle: .Alert)
  ac.addTextFieldWithConfigurationHandler(nil)

  ac.addAction(UIAlertAction(title: "Cancel", style: .Cancel, handler: nil))
  ac.addAction(UIAlertAction(title: "OK", style: .Default) { [unowned self, ac] _ in
    let newName = ac.textFields![0]
    person.name = newName.text!

    self.collectionView.reloadData()
  })

  presentViewController(ac, animated: true, completion: nil)
}
```
10. 使用照相机
`UIImagePickerController.isSourceTypeAvailable(.Camera)`