These guidelines build on Apple's existing [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html).
Unless explicitly contradicted below, assume that all of Apple's guidelines apply as well.

## Whitespace

 * Tabs, not spaces.
 * End files with a newline.
 * Make liberal use of vertical whitespace to divide code into logical chunks.

## Documentation and Organization

 * All method declarations should be documented.
 * Comments should be hard-wrapped at 80 characters.
 * Comments should be [appledoc](http://nshipster.com/documentation/)-style.
 * Document whether object parameters allow `nil` as a value.
 * Use `#pragma mark -`s to categorize methods into functional groupings and protocol implementations (note that all methods inherited from the superclass and properties are not grouped with `-`; all other groupings should include it.), following this general structure:

```objc
#pragma mark Properties

@dynamic someProperty;

- (void)setCustomProperty:(id)value {}

#pragma mark Lifecycle

+ (instancetype)objectWithThing:(id)thing {}
- (instancetype)init {}

#pragma mark Drawing

- (void)drawRect:(CGRect) {}

#pragma mark - Another functional grouping

#pragma mark - LNSuperclass

- (void)someOverriddenMethod {}

#pragma mark - NSCopying

- (id)copyWithZone:(NSZone *)zone {}

#pragma mark - NSObject

- (NSString *)description {}
```

## Declarations

 * Never declare an ivar unless you need to change its type from its declared property.
 * Don’t use line breaks in method declarations.
 * Prefer exposing an immutable type for a property if it being mutable is an implementation detail. This is a valid reason to declare an ivar for a property.
 * Always declare memory-management semantics even on `readonly` properties.
 * Declare properties `readonly` if they are only set once in `-init`.
 * Don't use `@synthesize` unless the compiler requires it. Note that optional properties in protocols must be explicitly synthesized in order to exist.
 * Declare properties `copy` if they return immutable objects and aren't ever mutated in the implementation. `strong` should only be used when exposing a mutable object, or an object that does not conform to `<NSCopying>`.
 * Instance variables should be prefixed with an underscore (just like when implicitly synthesized).
 * Don't put a space between an object type and the protocol it conforms to.
 
```objc
@property (attributes) id<Protocol> object;
@property (nonatomic, strong) NSObject<Protocol> *object;
```
 
 * C function declarations should have no space before the opening parenthesis, and should be namespaced just like a class.

```objc
void LNAwesomeFunction(BOOL hasSomeArgs);
```

 * Constructors should generally return [`instancetype`](http://clang.llvm.org/docs/LanguageExtensions.html#related-result-types) rather than `id`.
 * Prefer C99 struct initialiser syntax to helper functions (such as `CGRectMake()`).

```objc
  CGRect rect = { .origin.x = 3.0, .origin.y = 12.0, .size.width = 15.0, .size.height = 80.0 };
   ```

## Expressions

 * Don't access an ivar unless you're in `-init`, `-dealloc` or a custom accessor.
 * Use dot-syntax when invoking idempotent methods, including setters and class methods (like `NSFileManager.defaultManager`).
 * Use object literals, boxed expressions, and subscripting over the older, grosser alternatives.
 * Comparisons should be explicit for everything except `BOOL`s.
 * Prefer positive comparisons to negative.
 * Long form ternary operators should be wrapped in parentheses and only used for assignment and arguments.

```objc
Blah *a = (stuff == thing ? foo : bar);
```

* Short form, `nil` coalescing ternary operators should avoid parentheses.

```objc
Blah *b = thingThatCouldBeNil ?: defaultValue;
```

 * Separate binary operands with a single space, but unary operands and casts with none:

```c
void *ptr = &value + 10 * 3;
NewType a = (NewType)b;

for (int i = 0; i < 10; i++) {
    doCoolThings();
}
```

## Control Structures

 * Always surround `if` bodies with curly braces if there is an `else`. Single-line `if` bodies without an `else` should be on the same line as the `if`. 
 * All curly braces should begin on the same line as their associated statement. They should end on a new line.
 * Put a single space after keywords and before their parentheses.
 * Return and break early.
 * No spaces between parentheses and their contents.

```objc
if (somethingIsBad) return;

if (something == nil) {
	// do stuff
} else {
	// do other stuff
}
```

## Exceptions and Error Handling

 * Don't use exceptions for flow control.
 * Use exceptions only to indicate programmer error.
 * To indicate errors, use an `NSError **` argument or send an error on a [ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa) signal (if applicable).

## Blocks

 * Blocks should have a space between their return type and name.
 * Block definitions should omit their return type when possible.
 * Block definitions should omit their arguments if they are `void`.
 * Parameters in block types should be named unless the block is initialized immediately.

```objc
void (^blockName1)(void) = ^{
    // do some things
};

id (^blockName2)(id) = ^ id (id args) {
    // do some things
};
```

## Literals

 * The contents of array literals shouldn't have a space on either side.
 * The contents of dictionary literals should have a space on both sides.
 * Dictionary literals should have no space between the key and the colon, and a single space between colon and value.

``` objc
NSArray *theStuff = @[@1, @2, @3];

NSDictionary *keyedStuff = @{ LNDidCreateStyleGuide: @YES };
```

 * Longer or more complex literals should be split over multiple lines (optionally with a terminating comma):

``` objc
NSArray *theStuff = @[
    @"Got some long string objects in here.",
    [AndSomeModelObjects too],
    @"Moar strings."
];

NSDictionary *keyedStuff = @{
    @"this.key": @"corresponds to this value",
    @"otherKey": @"remoteData.payload",
    @"some": @"more",
    @"JSON": @"keys",
    @"and": @"stuff",
};
```

## Categories

 * Categories should be named for the sort of functionality they provide. Don't create umbrella categories.
 * Category methods should always be prefixed.
 * If you need to expose private methods for subclasses or unit testing, create a class extension named `Class+Private`.

## Constants

 * Constants should always be declared on the top of the file (on the `.m` if is used only in the implementation; on the `.h` if used as an identifier or notification, etc).
 * All constants should be prefixed with the lowercase letter `k` and the "global" classes prefix. e.g.:
 
``` objc
/// Assume the global prefix is LN
NSString *const kLNConstant;
```
 
 * All constants should have the `const` keyword after its type; if its an object it shouldn't be any empty spaces between the `const` and the `*` as follows:
 
``` objc
NSString *const kLNConstantString = @"value";
 
CGFloat const kLHConstantHeight = 50.0f;
```
 
 * For constants used in places other than the class defining they should be declared as `extern` on the `.h` and initialised on the `.m` as follows:
 
``` objc
/// Interface.h

extern NSString const* kLHReloadDataNotification;



/// Implementation.m

NSString const* kLHReloadDataNotification = @"LHReloadDataNotification";

```
 * Never use `#define` instead of constants; this declarations do not add type check at compile time (the compiler does a "literal" replace of whatever is on the define).

## Imports

 * For all "native" imports prefer the use of modules instead of `.h` files. e.g.:

``` objc
@import UIKit;
```

 * Separate all headers logically with comments to make it easier to read the code. e.g.:

``` objc
// Header
#import "LNHeader.h"

// Models
#import "DataModels.h"

// Utilities
#import "LNNetwork.h"

// Native Frameworks
@import Social;
@import Accounts;

// 3rd Party Frameworks
#import <AFNetworking/AFNetworking.h>
```

 * When importing 3rd party frameworks (either via static libraries or [CocoaPods](http://cocoapods.org/)) include the notation of `<LibraryName/HeaderFile.h>`

 
