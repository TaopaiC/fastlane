# fastlane danger Device Grid

Ever dream of testing your app straight from a pull request? Well now you can! With [fastlane](https://fastlane.tools), [danger](https://github.com/danger/danger) and [appetize.io](https://appetize.io/), you can stream your latest changes right from the browser.

No more manually installing and testing your app just to review a PR.

![assets/GridExampleScreenshot.png](assets/GridExampleScreenshot.png)

## Requirements

- [fastlane](https://fastlane.tools)
- [danger](https://github.com/danger/danger)
- [appetize.io](https://appetize.io/) account
- A Continuous Integration system

## Getting started

### Install fastlane and danger

Create a `Gemfile` in your project's directory with the following content

```ruby
gem "fastlane"
gem "danger"
```

and run

```
bundle install
```

### Setup `fastlane`

Skip this step if you're already using `fastlane` (which you should)

```
fastlane init
```

### Setup `danger`

```
danger init
```

Follow the `danger` guide to authenticate with GitHub

### Configure [fastlane](https://fastlane.tools)

Edit `fastlane/Fastfile`. Feel free to remove the auto-generated lanes. Add the following lane:

```ruby
desc "Build your app and upload it to Appetize to stream it in your browser"
lane :upload_to_appetize do
  build_and_upload_to_appetize(
    xcodebuild: {
      workspace: "YourApp.xcworkspace",
      scheme: "YourScheme"
    }
  )
end
```

Make sure to fill in your actual workspace and scheme, or replace those 2 parameters with `project: "YourApp.xcworkspace"`

### Configure `danger`

Edit `Dangerfile` and replace the content with

```ruby
puts "Running fastlane to generate and upload an ipa file..."
puts `fastlane upload_to_appetize` # this will generate and upload your ipa file

import "https://raw.githubusercontent.com/fastlane/fastlane/master/fastlane/lib/fastlane/actions/device_grid/device_grid.rb"

device_grid(
  languages: ["en", "de"],
  devices: ["iphone5s", "iphone6splus", "ipadair"]
)
```

### Try it

Push everything to GitHub in its own branch and create a PR to trigger your CI system. 

### Make use of deep linking

When you submit a PR you usually know what part of your app should be reviewed. Make it easier for everyone by providing a deep link, launching the app at the right point. To do so, use emojis (what else):

Add this to the bottom of your PR-body:

```
:link: com.krausefx.app://bacons/show/937
```

### Make use of `NSUserDefaults`

To do a runtime check if if the app is running on `Appetize`, just use:

```objective-c
[[NSUserDefaults standardUserDefaults] objectForKey:@"isAppetize"]
```
