# Core Data

- Core Data is an object-graph management and persistence framework provided by Apple.
- It allows data organized by the relational entity–attribute model to be serialized into XML, binary, or SQLite stores.
- Allows to efficiently fetch model objects from a persistent store and save changes back to the store.
- Provides an infrastructure for tracking changes to our model objects. It gives automatic support for undo and redo, and for maintaining relationships between objects.
- SKIP : Has an infrastructure for data store versioning and migration. This infrastructure lets you easily upgrade an old version of the data file to the current version.

## How Does It Differ From SQLite?
### SQLite:
    - Have Data Constrains feature.
    - Manages and updates objects automatically in the memory. Can handle a complex graph of objects without any pressure/load on the memory management system of the device.
    - Can Drop table and Edit data without loading them in memory.
    - Slow as compared to core data.

### Core Data:
    - Don’t have Data Constraints,if required need to implement by business logic.
    - Operates on in memory.(data needs to be loaded from disk to memory)
    - Need to load entire data if we need to drop table or update.
    - Fast in terms of record creation.(saving them may be time consuming)
    - It could be used for more lightweight models since its memory management will be dependent on the CocoaPods or Frameworks that have been used.

### Core Data Limitations
    - Core Data can only do its magic because it keeps the object graph it manages in memory.
    - Threading model of Core Data.

## Core data stack
###  Managed Object Model 
    - It describes the schema that you use in the app. 
    - the schema is represented by a collection of objects (also known as entities)
###  Managed Object Context
    - A managed object context represents a single object space, or scratch pad, in a Core Data application.
    - It is the central object in the Core Data stack.
    - A managed object context is an instance of NSManagedObjectContext.
    - Its primary responsibility is to manage a collection of managed objects.
    - The context is a powerful object with a central role in the life-cycle of managed objects, with responsibilities from life-cycle management (including faulting) to validation, inverse relationship handling, and undo/redo.

    - How it works
        - A context is connected to a parent object store. (This is usually a persistent store coordinator, but may be another managed object context.)
        - When you fetch objects, the context asks its parent object store to return those objects that match the fetch request.
        - Changes that you make to managed objects are not committed to the parent store until you save the context.
    - In some applications, you may have just a single context. In others, however, you might have more than one. You might want to maintain discrete sets of managed objects and edits to those objects; or you might want to perform a background operation using one context while allowing the user to interact with objects in another.
###  Persistent Store Coordinator 
    - The Persistent Store Coordinator is the party responsible to manage different persistent object stores and save the objects to the stores. 
    - SQLite is the default persistent store in iOS.
    - However, Core Data allows developers to setup multiple stores containing different entities. 


￼![Core Data overview](https://github.com/AnandKore91/Notes/blob/master/Images/CoreDataOverview.jpg "Core Data overview")

1. A managed object model that describes the entities in the stores.
2. A managed object context that provides a scratch pad for managed objects.
3. A persistent store coordinator that aggregates all the stores.
4. A persistent object store that maps between records in the store and objects in your application.
5. An external persistent store that contains saved records.



## Create Records to Core Data
The process of adding the records to Core Data has following tasks
- Refer to persistentContainer from app-delegate
- Create the context from persistentContainer
- Create an entity i.e User
- Create new record with this newly created User Entity
- Set values for the records for each key
￼

## Retrieve Data
The process of fetching the saved data is very easy as well. It has the following task
- Prepare the request of type NSFetchRequest for the entity (User in our example)
- if required use predicate for filter data
- Fetch the result from context in the form of array of [NSManagedObject]
- Iterate through an array to get value for the specific key
￼

## Update Data
For update record first we have to fetch/Retrieve data with predicate as same as above Retrieve data process. Then below few steps to follow
- Prepare the request with predicate for the entity (User in our example)
- Fetch record and Set New value with key
- And Last Save context same as create data.
￼


## Delete Data
For delete record first we have to find object which we want to delete by fetchRequest. then follow below few steps for delete record
- Prepare the request with predicate for the entity (User in our example)
- Fetch record and which we want to delete
- And make context.delete(object) call (ref image attached below)

￼


    - Multiple threading in Core data. 
        - https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreData/Concurrency.html#//apple_ref/doc/uid/TP40001075-CH24-SW2
		let privateMOC = NSManagedObjectContext(concurrencyType: .PrivateQueueConcurrencyType). // MainQueueConcurrencyType and PrivateQueueConcurrencyType.
		privateMOC.parentContext = moc
		 
		privateMOC.performBlock {
		}

		OR

		let jsonArray = …
		let container = self.persistentContainer
		container.performBackgroundTask() { (context) in
		    for jsonObject in jsonArray {
		        let mo = EmployeeMO(context: context)
		        mo.populateFromJSON(jsonObject)
		    }
		    do {
		        try context.save()
		    } catch {
		        fatalError("Failure to save context: \(error)")
		    }
		}