JLRoutes
========

### What is it? ###
JLRoutes is advanced URL parsing with a block-based callback API. It is designed to make it very easy to handle complex URL schemes in your application without having to do any URL or string parsing of any kind.

### Features ###
* Simple API with minimal impact to existing codebases
* Parse any number of parameters interleaved throughout the URL
* Seamlessly parses out GET URL parameters and passes them along as part of the parameters dictionary
* Routes prioritization
* Return NO from a handler block for JLRoutes to look for the next matching route
* No dependencies other than Foundation

### Simple Example ###
```objc
[JLRoutes addRoute:@"/user/view/:userID" handler:^BOOL(NSDictionary *parameters) {
  NSString *userID = parameters[@"userID"]; // defined in the route by specifying ":userID"
  // present UI for viewing user with ID 'userID'
  return YES; // return YES to say we have handled the route
}];

// in your app delegate:
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation {
  return [JLRoutes routeURL:url];
}
```

After having set that route up, at any point something (including a different application) could call this to fire the handler block:
```objc
NSURL *viewUserURL = [NSURL URLWithString:@"myapp://user/view/joeldev"];
[[UIApplication sharedApplication] openURL:viewUserURL];
```

In this example, the userID object in the parameters dictionary passed to the block would have "userID" : "joeldev", which could then be used to present a UI or do whatever else is needed.

### The Parameters Dictionary ###

The parameters dictionary always contains at least the following two keys:
```json
{
  "JLRouteURL" : "(the NSURL that caused this block to be fired)",
  "JLRoutePattern" : "(the actual route pattern string)"
}
```

These are defined as constants in JLRoutes.h for easy use.

```objc
static NSString *const kJLRoutePatternKey = @"JLRoutePattern";
static NSString *const kJLRouteURLKey = @"JLRouteURL";
```

### Handler Block Return Value ###

As you may have noticed, the handler block is expected to return a BOOL for if it has handled the route or not. If the block returns NO, JLRoutes will behave as if that route is not a match and it will continue looking for a match. A route is considered to be a match if the pattern string matches AND the block returns YES.

### More Complex Example ###

```objc
[JLRoutes addRoute:@"/:object/:action/:primaryKey" handler:^BOOL(NSDictionary *parameters) {
  NSString *object = parameters[@"object"];
  NSString *action = parameters[@"action"];
  NSString *primaryKey = parameters[@"primaryKey"];
  // stuff
  return YES;
}];
```

This route would match things like /user/view/joeldev or /post/edit/123. Let's say you did /post/edit/123 with some URL params as well:

```objc
NSURL *editPost = [NSURL URLWithString:@"myapp://post/edit/123?debug=true&foo=bar"];
[[UIApplication sharedApplication] openURL:editPost];
```

The parameters dictionary that the handler block receives would contain the following key/value pairs:
```json
{
  "object": "post",
  "action": "edit",
  "primaryKey": "123",
  "debug": "true",
  "foo": "bar",
  "JLRouteURL" : "myapp://post/edit/123?debug=true&foo=bar",
  "JLRoutePattern" : "/:object/:action/:primaryKey"
}
```

### Apps using JLRoutes ###
* None that I know of so far! Feel free to create an issue asking me to add your app, if you're making use of JLRoutes.

### Installation ###
JLRoutes is available for installation via CocoaPods. The Releases folder in the repo has binary builds as well, if you'd rather just drop something in.

### Requirements ###
ARC. Only tested on iOS 6, but I don't think there's any reason why it wouldn't work on previous versions. It should also work seamlessly on OS X.

### License ###
BSD

