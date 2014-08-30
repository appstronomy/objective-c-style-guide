# Appstronomy Objective-C Style Guide

This style guide outlines the coding conventions of the iOS team at Appstronomy. It has been forked from the original style guide published by The New York Times. 

See the original [New York Times Style Guide](https://github.com/NYTimes/objective-c-style-guide) if you're looking for the unchanged inspiration to this one.

As we touch on some areas of style here that are not relevant to many organizations, including the New York Times, we are not planning to create pull requests to merge back into their original. As such, we have also removed the localized versions of this guide, as we do not have the resources to keep these at par, nor a present need to do so.

## A Work in Progress

We are still bringing our existing and active codebases inline with this newly codified style guide, inspired by the great suggestions and best practicies of others. As such, know that we want you to follow the guidance here, as opposed to patterns in historical code created before we had a formal style guide.

## Introduction

Here are some of the documents from Apple that informed the original New York Times style guide. If something isn't mentioned here, it's probably covered in great detail in one of these:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Table of Contents

* [Dot-Notation Syntax](#dot-notation-syntax)
* [Braces](#braces)
* [Spacing](#spacing)
  * [Basics](#basics)
  * [Pragma Marks](#pragma-marks)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Error handling](#error-handling)
* [Methods](#methods)
  * [Target-Action](#target-action)
* [Variables](#variables)
* [Naming](#naming)
* [Comments](#comments)
* [Pragma Headings](#pragma-headings)
* [Constants](#constants)
* [Init & Dealloc](#init-and-dealloc)
* [Literals](#literals)
* [CGRect Functions](#cgrect-functions)
* [Constants](#constants)
* [Enumerated Types](#enumerated-types)
* [Bitmasks](#bitmasks)
* [Private Properties](#private-properties)
* [Image Naming](#image-naming)
* [Booleans](#booleans)
* [Singletons](#singletons)
* [Xcode Project](#xcode-project)

## Dot-Notation Syntax

Dot-notation should **always** be used for accessing and mutating properties. Bracket notation is preferred in all other instances.

###### For example:
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

###### Not:
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## Braces

A hotly contested and subjective area. Our take:

* **Methods**: Place the opening brace on a line of its own. This provides clarity in reading the method signature and finding where the method body starts for long methods or methods with many parameters.
* **Conditional statements**: Place the opening brace on the same line as the statement. 

In both cases, closing braces go on a line of their own, unless dealing with a one-line conditional statement.

###### For example:
```objc
- (void)prepareSurpriseGift:(APPSGift *)gift forUser:(APPSUser *)user;
{
    if (user.isHappy) {
        // Do something
    }
    else {
        // Do something else
    }
	
    if (gift) { NSLog(@"A gift was present."); }
}
```



## Spacing

### Basics

* Indent using 4 spaces. Never indent with tabs (unless you've mapped tab to produce 4 spaces in your IDE's preferences). Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

###### For example:
```objc
if (user.isHappy) {
    // Do something
}
else {
    // Do something else
}
```

Leave **2 lines** of vertical space between methods. The internals of methods often have one line of vertical space to demarcate groups of statements. We want methods to be visually scannable and distinguishable from each other.

### Pragma Marks

* Leave **3 lines** of vertical space above a `#pragma mark - Heading`. It will help with quick visual parsing and appear more pleasant in Xcode than it does in this markdown rendering.
	* If there are no methods above the heading, just leave 1 line of space above the pragma mark.
	* Leave 1 line of space *after* the pragma mark heading and the start of a method.

###### For example:
```objc
#pragma mark - Configuration

- (void)applyDefaultConfiguration
{
    // Some method content here...
}



#pragma mark - Public Interface

- (void)loadModel:(NSString *)modelName;
{
    // Some method content here...
}


- (void)loadModel:(NSString *)modelName logResults:(BOOL)logResults;
{
    // Some method content here...
}
```

* Don't leave any vertical space between a `#pragma mark -` that produces a separator line (has the '-' in it) and any `#pragma mark` right below it, which does not.

###### For example:
```objc
#pragma mark - Find Using: Default Managed Object Context
#pragma mark Multiple: This Class

+ (NSArray *)findAll;
{
    return [[self dataStore] findAllForEntityNamed:[self entityName]];
}
```



## Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent [errors](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256). These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

###### For example:
```objc
if (!error) {
    return success;
}
```

###### Not:
```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```

### Ternary Operator

The Ternary operator, ? , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement, or refactored into instance variables.

###### For example:
```objc
result = a > b ? x : y;
```

###### Not:
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Error handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

###### For example:
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

###### Not:
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## Methods

In method signatures, there should be a space after the scope (-/+ symbol). There should be a space between the method segments.

**For Example**:
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```

### Target-Action

These sorts of methods generally go under the pragma section with name 'User Actions'. For such methods, always use the prefix 'handle'. Generally, the appropriate suffix to use will be 'Tapped'. If the control that was tapped or otherwise interacted with has an IBOutlet property, use the name of that property in the method. 

Generally, IBOutlet properties will be named so that their type is included in the name, e.g. `myServiceButton`, `usernameTextField` etc.

**For Example**:
```objc
- (IBAction)handleMyServiceButtonTapped:(id)sender;
- (IBAction)handlePlayground2ButtonTapped:(id)sender;
```


## Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants.

Property definitions should be used in place of naked instance variables whenever possible. Direct instance variable access should be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

###### For example:

```objc
@interface NYTSection: NSObject

@property (strong, nonatomic) NSString *headline;

@end
```

###### Not:

```objc
@interface NYTSection : NSObject {
    NSString *headline;
}
```

#### Variable Qualifiers

When it comes to the variable qualifiers [introduced with ARC](https://developer.apple.com/library/ios/releasenotes/objectivec/rn-transitioningtoarc/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4), the qualifer (`__strong`, `__weak`, `__unsafe_unretained`, `__autoreleasing`) should be placed before the type.

###### For example:
```objc
__weak NSString *text;
```

When indicating block shared variables with the `__block` syntax, have that prefix everything else.

###### For example:
```objc
__block __weak NSString *text;
```


## Naming

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good.

###### For example:

```objc
UIButton *settingsButton;
```

###### Not:

```objc
UIButton *setBut;
```

A three or four letter prefix (e.g. `NYT`, `APPS`, etc.) should always be used for class names and constants, including for Core Data entity names. Two letters are accepted, but discouraged for new code.


### Properties and local variables 
These should be camel-case with the leading word being lowercase.

Instance variables should be camel-case with the leading word being lowercase, and should be prefixed with an underscore. This is consistent with instance variables synthesized automatically by LLVM. Instance variables without a public property declaration, should always be declared in the implementation.

_Don't manually synthesize a property if LLVM can synthesize the variable automatically. Unless you're doing something unusual, LLVM will in fact synthesize it for you._

###### For example:

```objc
@property (strong, nonatomic) NSString *descriptiveVariableName;
```


###### Not:

```objc
id varnm; // truncated, not in camel-case and not descriptive
```

If other classes don't need to invoke a method or access a property, then put those methods and properties in the implementation file. This includes `IBOutlet` properties wired to the Xib/Storyboard. 


## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define` statements *unless* explicitly being used as a macro *or* where it obviates the need to cast parameters throughout your code (to avoid type mismatch compiler warnings). 

Constants should be camel-case with all words in initial caps and prefixed with a lowercase 'k', followed by the related class name for clarity. Separate the class name from the rest of the constant's name with an underscore. The part that follows the underscore should begin with a capital letter.

###### For example:

```objc
static const NSTimeInterval kAPPSArticleViewController_FadeAnimationDuration = 0.3f;
```

###### Not:

```objc
static const NSTimeInterval fadetime = 1.7;
```

* If the constant is not tied to a specific class, then create your own placeholder concept name to use as if it was a class name.

###### For example:

```objc
static const NSTimeInterval kAPPSAnimation_DefaultDelay = 0.0f;
```


Understandably, having to cast every use of a constant font object to `(UIFont *)` because a parameter in another method is not expecting a strong `const UIFont *`, clutters the code. So use a `#define` in these situtations.


###### For example:

```objc
static const NSString *kNYTAboutViewController_CompanyName = @"The New York Times Company";

static const CGFloat kNYTImageThumbnail_Height = 50.0;

#define kRPFont_HeadingSmall [UIFont fontWithName:@"HelveticaNeue-Bold" size:13.0f]
```

###### Not:

```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2

#define smallHeadingFont [UIFont fontWithName:@"HelveticaNeue-Bold" size:13.0f]

```

## Enumerated Types

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types — `NS_ENUM()`

Each option in the enumerated type should start with the type's name, followed by an underscore and then a word or phrase in initial caps.

**Example:**

```objc
typedef NS_ENUM(NSInteger, APPSRequestState) {
    APPSRequestState_Inactive,
    APPSRequestState_Loading
};
```


## Comments

### As Documentation

Comments meant to generate documentation should be formatted using the [appledoc](https://github.com/tomaz/appledoc) syntax, insofar as it is compatible with our [documentation generator tool de jour](https://github.com/realm/jazzy).

##### Header Files
Use block comments in header files for documentation of the class itself as well as public properties and methods. 

###### For example:

```objc
/**
 Represents the single object from which other facilities in the app can be reached, 
 such as the data store class, or back-end services.
 
 An instance of this class is meant to be injected onto key base classes in the app,
 using static variables, so that they are effectively available to all subclasses.
 
 We are meant to be used as a singleton, hence the -sharedInstance method. 
 However, you may instantiate individual instances and configure them should you wish 
 to have more control in your unit tests with dependency injection.
 */
@interface APPSService : NSObject
```


If a method is present in a header file, provide a block comment there for documentation. 

Separate declared properties in an interface file from declared methods, with a `#pragmar mark -` statement, as a heading for the method(s) that follow.

###### For example:

```objc
// This is an example interface (header file) already in progres...

/**
 The bundle in which reference data files can be found. 
 If not set, we'll use the default bundle.
 */
@property (strong, nonatomic) NSBundle *dataSetBundle;



#pragma mark - Public Interface

/**
 Loads flat file data for the specified model. 
 Makes use of the current dataSetBundle and dataSetPrefix
 and dataTypeName properties set.
 
 @param modelName The name of the model is the name of the entity 
 as known to Core Data (i.e. without any Objective-C name spacing prefix).
 */
- (void)loadModel:(NSString *)modelName;
```

##### Implementation Files
If a non-trivial method is only present in an implementation file, provide a block comment above the method with appledoc like formatting. The goal here, is to help a future maintainer of the class. Class maintainers can benefit from good method documentation too; even when it's just looking at source code.

### For Explaining Internals
When they are needed, internal code comments should use the single line syntax.

###### For example:

```objc
// Defensive check: Is there even any content in the set to be sorted?
if ([relationSet count] > 0) {
    // YES: We have content. So filter it now.
    NSSet *filteredSet = [relationSet filteredSetUsingPredicate:predicate];
}
```

Comments should be used to explain intent and motivation. Where helpful, comments can be used to help a developer follow the logical thought process by paraphrasing the approach taken. Such descriptions can provide guideposts to a developer trying to understand the code months or years later.

Any comments that are used **must** be kept up-to-date or deleted. Left in, they'll create more confusion than they will any value.

Block comments within a method body should be avoided (use multiple lines of the single line `//` syntax if you must). Code should be as self-documenting as possible, with only the need for short, intermittent, inline explanations to clarify intent. This does not apply to those comments used to generate documentation, as described [above](#as-documentation).


## Pragma Headings

We use the `#pragam mark` directive quite frequently to help more easily visualize sections of a source file, whether viewing the raw source or the structure of a file as given from a pulldown control in Xcode.

If you place a dash ('-') after the `#pragam mark` directive, it will create a ruled line above when, for when viewed in an Xcode structure list. Use that to separate top level sections. Use a `#pragma mark` without the dash to delineate sub-sections or provide headings to organize property types.

###### For example:
```objc
#pragma mark - Find Using: Default Managed Object Context
#pragma mark Multiple: This Class

+ (NSArray *)findAll;
{
    return [[self dataStore] findAllForEntityNamed:[self entityName]];
}
```

Typical top-level pragma mark headings you'll see and create in Appstronomy code follow. Where used, try to follow this general ordering:

* **Instantiation:** Where singleton and public designated initializers go.
* **Lifecycle:** Where dealloc, init and view controller lifecycle methods go (e.g. `viewDidLoad:`, `viewWillAppear:` etc.). Memory warning handlers also go in this section (e.g. `didReceiveMemoryWarning`).
* **&lt;superclass name&gt;:** When you override a method in a superclass that doesn't belong under a different heading, such as *Lifecycle*, create a heading using the name of that superclass, and implement the overidden methods here.
* **Property Overrides:** If you implement accessor or mutator methods for properties, they go under this heading.
* **Configuration:** One time or recuring setup and configuration methods go here.
* **User Actions:** Action methods, generally wired from Interface Builder.
* **Protocol: &lt;protocol name&gt;:** Methods you implement for a given protocol should be colocated near the end of your implementation file, with the name of the protocol called out in the #pragma mark directive.
* **Notification: &lt;notification name&gt;:** Methods you implement for a notification type should be colocated near the end of your implementation file, with the name of the notification called out in the #pragma mark directive. It's a good idea to colocate your register, deregister and handler methods for the notification here.

Of course, your class will have its own needs beyond the common ones called out above. Group similar methods by purpose and provide them a section heading with a pragma mark directive.

You should also use simple `#pragam mark` headings to separate a class' properties by type. This becomes really valuable for classes with a large number of properties. 

###### For example:
```objc
@interface APPSMyClassName : NSObject

#pragma mark scalar
@property (assign, nonatomic, getter = isStepEnabled) BOOL stepEnabled;

#pragma mark weak
@property (weak, nonatomic) APPSScreenContainerView *containerView;

#pragma mark strong
@property (strong, nonatomic) NSString *widgetName;
@property (strong, nonatomic) NSString *descriptionText;

@end
```


## init and dealloc

`dealloc` methods, if needed with ARC (say, to de-register from notifications) should be placed near the top of the implementation file. `init` should be placed directly below the `dealloc` methods of any class. Of course, place these under the appropriate [#pragma heading](#pragma-headings).

`init` methods should be structured like this:

```objc
- (instancetype)init {
    self = [super init]; // or call the designated initalizer
    if (self) {
        // Custom initialization
    }

    return self;
}
```

Remember to use the `instancetype` return type, instead of hard-coding in the class type.

## Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

###### For example:

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

###### Not:

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

###### For example:

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

###### Not:

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## Bitmasks

When working with bitmasks, use the `NS_OPTIONS` macro. The naming is similar to enums, where each of the unique elements has an underscore in the name to separate the bitmask type name and the particular option.

**Example:**

```objc
typedef NS_OPTIONS(NSUInteger, APPSBodyPartCategory) {
  APPSBodyPartCategory_Back      = 1 << 0,
  APPSBodyPartCategory_Chest     = 1 << 1,
  APPSBodyPartCategory_Legs  	 = 1 << 2,
  APPSBodyPartCategory_Abs	 = 1 << 3
};
```

## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `APPSPrivate` or `private`) are discouraged, unless extending another class.

###### For example:

```objc
@interface NYTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## Image Naming

Images should be named consistently to preserve organization and developer sanity. For the most part, they should be named using initial caps for adjacent words. Components of the name should be separated by dashes when there exists an element to both the left and right of the component. The components of the name are as follows:

`[series abbreviation]-[component type]-[proper or conceptual name]-[state]-[bgs]`

* An optional series abbreviation, such as `"b-"` or `"gadget-"`.
* The component type or topic area in initial caps, e.g. `"Button"`, `"ViewBackground"`.
* The class and/or property name to which this image is associated, otherwise, a conceptual name. Always in initial caps.
* The state of the component if applicable, e.g. `normal`, `selected`. This is always in lowercase.
* An indication on whether this image is meant for stretching. If it is, it will have the suffix `bgs`, for *background stretchable*.



###### For example:

* `Button-Info-normal` / `Button-Info-normal@2x` 
* `a-ViewBackground-HorizontalRule-bgs` / `a-ViewBackground-HorizontalRule-bgs@2x`

Images that are used for a similar purpose should be grouped in the same [Asset Catalog](https://developer.apple.com/library/ios/recipes/xcode_help-image_catalog-1.0/Recipe.html) folder. The Appstronomy Designer on the team will generally handle image creation and naming.

## Booleans

Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

###### For example:

```objc
if (!someObject) {
}
```

###### Not:

```objc
if (someObject == nil) {
}
```

-----

**For a `BOOL`, here are two examples:**

```objc
if (isAwesome)
if (![someObject boolValue])
```

###### Not:

```objc
if (isAwesome == YES) // Never do this.
if ([someObject boolValue] == NO)
```

-----

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objc
@property (assign, getter=isEditable) BOOL editable;
```

Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE), published by Apple.

## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.
```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```

This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Xcode project

The physical files should be kept in sync with the top-level folders in the Xcode project files in order to avoid file sprawl. Xcode groups created below these top level folders do not have to be reflected by physical folders in the filesystem. Code should first be grouped type. For greater clarity, virtual sub-group folders are excellent for organizing by feature.

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

# Other Objective-C Style Guides

While those hired on for projects at Appstronomy are strongly encouraged to follow our style guide where a concept is explicitly covered here, you may wish to look at other style guides for comparison, inspiration and suggestions to help us improve our own guide.

* [The New York Times](https://github.com/NYTimes/objective-c-style-guide)
* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
