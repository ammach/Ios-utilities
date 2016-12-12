# Ios-utilities

====> Firebase :

func startObservingDb(){
      dbrefUsers.parent!.child("filieres").child("fifa").observeEventType(.Value) { (snapshot:FIRDataSnapshot) in
            self.users=[User]()
            for id in snapshot.children{
                self.users=[User]()
                let snap=id as! FIRDataSnapshot
                let userid=snap.key
             print(userid)
                self.dbrefUsers.child("\(userid)").observeEventType(.Value, withBlock: { (snapUser:FIRDataSnapshot) in
                    let name=snapUser.value!["name"] as! String
                    let age=snapUser.value!["age"] as! String
                    self.users.append(User(email: name, age:age))
                    self.tableView.reloadData()
                })
                
                }
        
        print( (snapshot.childrenCount))
        }
        }
        
====> AnyObject :

func toAnyObject()->AnyObject{
    return ["name":self.email,"age":self.age]
    }
    
====> pick image :

UINavigationControllerDelegate,UIImagePickerControllerDelegate

func imagePickerController(picker: UIImagePickerController, didFinishPickingImage image: UIImage, editingInfo: [String : AnyObject]?) {
        self.dismissViewControllerAnimated(true, completion: nil)
        
        img.image=image
    }
	
	
	@IBAction func pick(sender: AnyObject) {
        
        let image=UIImagePickerController()
        image.delegate=self
        image.sourceType=UIImagePickerControllerSourceType.PhotoLibrary
        image.allowsEditing=false
        
        self.presentViewController(image, animated: true, completion: nil)
    }
  
====> regex :

import UIKit
import Foundation

func regMatchGroup(regex: String, text: String) -> [String] {
    do {
        let regex = try NSRegularExpression(pattern: regex, options: [])
        let nsString = text as NSString
        let results = regex.matchesInString(text,
                                            options: [], range: NSMakeRange(0, nsString.length))
         var internalString = [String]()
        for result in results {
           
            for var i = 1; i < result.numberOfRanges; ++i{
                internalString.append(nsString.substringWithRange(result.rangeAtIndex(i)))
            }
        }
        return internalString
    } catch let error as NSError {
        print("invalid regex: \(error.localizedDescription)")
        return []
    }
}
// USAGE:
let textsearch = "mohamed amine ammach <img alt='http://fb.com' /> hhhhhhhhhhh <img alt='http://google.com' />"
let matches = regMatchGroup("alt='(.*?)'", text: textsearch)
if (matches.count > 0) // If we have matches....
{ 
    for (var i=0;i < matches.count;i++) {
     
       print(matches[i])
    
    }
}

====> Core Data :

extension Friend {

    @NSManaged var name: String?
    @NSManaged var messages: NSSet?

}

************************************

extension Message {

    @NSManaged var date: String?
    @NSManaged var text: String?
    @NSManaged var friend: Friend?

    
    convenience override init(entity: NSEntityDescription, insertIntoManagedObjectContext context: NSManagedObjectContext?) {
        
    }
}

************************************

  @IBAction func save(sender: AnyObject) {
    
        
        
      if messageLabel.text==""{
        print("enter message")
        }else{
            let appDel:AppDelegate=UIApplication.sharedApplication().delegate as! AppDelegate
            let context:NSManagedObjectContext=appDel.managedObjectContext
            
            let message=NSEntityDescription.insertNewObjectForEntityForName("Message", inManagedObjectContext: context)
            message.setValue(messageLabel.text, forKey: "text")
            message.setValue("dimanche", forKey: "date")
            let friendEntity=NSEntityDescription.entityForName("Friend", inManagedObjectContext: context)
        let friend=NSManagedObject(entity: friendEntity!, insertIntoManagedObjectContext: context)
            friend.setValue(friendLabel.text, forKey: "name")
            message.setValue(friend, forKey: "friend")
            do{
                try context.save()
                print("message inserted")
            }catch let error{
                print(error)
            }
        }
    }
	
*****************************************
	
	@IBAction func jocker(sender: AnyObject) {
        
        let appDel=UIApplication.sharedApplication().delegate as! AppDelegate
        let context=appDel.managedObjectContext
        
        let request=NSFetchRequest(entityName: "Message")
        request.predicate=NSPredicate(format: "friend.name=%@", "Kamal")
        request.returnsObjectsAsFaults=false
        do{
        let results=try context.executeFetchRequest(request)
            if results.count>0{
            print("there are some results")
                for result in results{
                print(result.valueForKey("text"))
                }
            }else{
               print("no result found")
            }
        }catch{
         print("oops problem")
        }
    }
