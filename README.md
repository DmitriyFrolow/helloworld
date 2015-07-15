#ANTableController

ANTableController – controller that make work with tableview more easy. You do not need write a large number of classes just use it. 

The main classes/protocols:

- ANTableController – class that implements protocols UITableViewDelegate and UITableViewDatasource. 
- ANMemoryStorage – class that incapsulate data storage and join data update with tableview update.
-  ANTableViewFactory – factory class that create cell. 
- ANModelTransfer – protocol, that should implement every cell for right work.

ANTableController offer you a lot of functionality by default, such as:

* Keyboard events handler, change tableview position when keyboard appear/disappear.
You can change its behavior by setting a new value to property, for example:
```objc
    self.tableController.isHandlingKeyboard = NO;//(By default YES)
```
Or setting your own keyboard handler with open property ANKeyboardHandler* keyboardHandler.

* If it is necessary  you can change default animation with: 
```objc
@property (nonatomic, assign) UITableViewRowAnimation insertSectionAnimation;
@property (nonatomic, assign) UITableViewRowAnimation deleteSectionAnimation;
@property (nonatomic, assign) UITableViewRowAnimation reloadSectionAnimation;
@property (nonatomic, assign) UITableViewRowAnimation insertRowAnimation;
@property (nonatomic, assign) UITableViewRowAnimation deleteRowAnimation;
@property (nonatomic, assign) UITableViewRowAnimation reloadRowAnimation;
```

ANTableController initialize example:
```objc
@property (strong,nonatomic) ANTableController *tableController;
@property (nonatomic,strong) ANMemoryStorage *storage;

self.tableController = [[ANTableController alloc] initWithTableView:self.tableView];
self.storage = [ANMemoryStorage storage];
//After that just initialize memory storage wit data model:

self.tableController.memoryStorage = self.storage;
 ANTestModelClass* testModel = [[ANTestModelClass alloc]init];
    testModel.firstName = @"John";
    testModel.lastName = @"Snow";

[self.storage addItem:testModel];
```
###ANMemoryStorage
Allow you update table view data with single model or model arrays, or send configuration block with needs update. 
For example to add/remove item in first section example:
```objc
[self.storage removeItem:testModel];
[self.storage addItem:testModel];
```
To add item in certain section example:
```objc
[self.storage addItems:@[testModel] toSection:1];
```
Send batch update block example:
```objc
[self.storage batchUpdateWithBlock:^{
      [self.storage addItems:@[testModel] toSection:1];
 }];
 ```
 
###TableViewCell 
Every table view cell should implement protocol ANModelTransfer.

TableViewCell example:
 ```objc
#import <UIKit/UIKit.h>
#import <ANStorage/ANModelTransfer.h>
#import "ANTestModelClass.h"
@interface ANTestTableViewCell : UITableViewCell <ANModelTransfer>
@property (nonatomic,strong) FDTestModelClass* testModel;
@end

@implementation  ANTestTableViewCell

#pragma mark ANModelTransfer protocol methods
- (void)updateWithModel:(id)model {
        self.testModel = (ANTestModelClass *)model;
        self.topLabel.text = self.testModel.firstName;
        self.bottomTextField.text = self.testModel.lastName;
}
- (id)model {
    return self.testModel;
}
@end
  ```
  
###Search Bar
For search some data in table view just initialize seach bar and searchingStorage. 
Before search set you own stroragePredicateBlock, where you can specify your search.
```objc
    self.tableController.searchBar = self.searchBar;
    self.tableController.searchingStorage = self.memoryStorage;
    self.memoryStorage.storagePredicateBlock = ^NSPredicate *(NSString* searchString, NSInteger scope){
        return [NSPredicate predicateWithFormat:@"firstName CONTAINS[cd] %@",searchString];
    };
```
