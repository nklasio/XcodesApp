<h1><img src="icon.png" align="center" width=50 height=50 /> Xcodes.app</h1>

The easiest way to install and switch between multiple versions of Xcode.

_If you're looking for a command-line version of Xcodes.app, try [`xcodes`](https://github.com/RobotsAndPencils/xcodes)._

![CI](https://github.com/RobotsAndPencils/Xcodes.app/workflows/CI/badge.svg)

![](screenshot.png)

## Features

- List all available Xcode versions from [Xcode Releases'](https://xcodereleases.com) data or the Apple Developer website.
- Install any Xcode version, fully automated from start to finish. Xcodes uses [`aria2`](https://aria2.github.io), which uses up to 16 connections to download 3-5x faster than URLSession.
- Just click a button to make a version active with `xcode-select`.
- View release notes, OS compatibility, included SDKs and compilers from [Xcode Releases](https://xcodereleases.com).

## Installation

Xcodes.app runs on macOS Big Sur 11.0 or later.

### Homebrew Cask

```sh
brew install --cask xcodes

# These are Developer ID-signed and notarized release builds and don't require Xcode to already be installed in order to use.
```

### Download a release

1. Download the latest version [here](https://github.com/RobotsAndPencils/XcodesApp/releases/latest) using the **Xcodes.zip** asset. These are Developer ID-signed and notarized release builds and don't require Xcode to already be installed in order to use.
2. Move the unzipped `Xcodes.app` to your `/Applications` directory

## Development

You'll need macOS 11 Big Sur and Xcode 12 in order to build and run Xcodes.app.

If you aren't a Robots and Pencils employee you'll need to change the CODE_SIGNING_SUBJECT_ORGANIZATIONAL_UNIT build setting to your Apple Developer team ID in order for code signing validation to succeed between the main app and the privileged helper.

Notable design decisions are recorded in [DECISIONS.md](./DECISIONS.md). The Apple authentication flow is described in [Apple.paw](./Apple.paw), which will allow you to play with the API endpoints that are involved using the [Paw](https://paw.cloud) app.

[`xcode-install`](https://github.com/xcpretty/xcode-install) and [fastlane/spaceship](https://github.com/fastlane/fastlane/tree/master/spaceship) both deserve credit for figuring out the hard parts of what makes this possible.

<details>
<summary>Releasing a new version</summary>

Follow the steps below to build and release a new version of Xcodes.app. For any of the git steps, you can use your preferred tool, but please sign the tag.

```sh
# Update the version number in Xcode and commit the change, if necessary

# Question: Did anything in XPCHelper change? 
# - com.robotsandpencils.XcodesApp.Helper folder and HelperXPCShared
# - if so, bump the version number in com.robotsandpencils.XcodesApp.Helper target. 
# Note: you do not have to bump the version number if nothing has changed.
# Note2: If you do bump the version, the end user, must re-install the XPCHelper and give permission again.

# Increment the build number
scripts/increment_build_number.sh

# Commit the change
git add Xcodes/Resources/Info.plist
git commit -asm "Increment build number"

# Tag the latest commit
# Replace $VERSION and $BUILD below with the latest real values
git tag -asm "v$VERSIONb$BUILD" "v$VERSIONb$BUILD"

# Push to origin
git push --follow-tags

# Build the app
# Make sure you have the Xcode Selected you want to build with
scripts/package_release.sh

# Notarize the app
# Do this from the Product directory so the app is zipped without being nested inside Product
# Create a app specific password on appleid.apple.com if you haven't already
# % xcrun altool --store-password-in-keychain-item "AC_PASSWORD" -u "<appleiduseremail>" -p <app_specific_secret>

pushd Product
../scripts/notarize.sh "test@example.com" "@keychain:AC_PASSWORD" <MyOrg> Xcodes.zip

# Sign the .zip for Sparkle, note the signature in the output for later
# If you're warned about the signing key not being found, see the Xcodes 1Password vault for the key and installation instructions.
../scripts/sign_update Xcodes.zip
popd

# Go to https://github.com/RobotsAndPencils/XcodesApp/releases
# If there are uncategorized PRs, add the appropriate label and run the Release Drafter action manually
# Edit the latest draft release
# Set its tag to the tag you just pushed
# Set its title to a string with the format "$VERSION ($BUILD)"
# Polish the draft release notes, if necessary
# Add the signature to the bottom of the release notes in a comment, like:
<!-- sparkle:edSignature=$SIGNATURE -->
# Attach the zip that was created in the Product directory to the release
# Publish the release

# Update the [Homebrew Cask](https://github.com/RobotsAndPencils/homebrew-cask/blob/master/Casks/xcodes.rb).
```
</details>

## Contact

<a href="http://www.robotsandpencils.com"><img src="R&PLogo.png" width="153" height="74" /></a>

Made with ❤️ by [Robots & Pencils](http://www.robotsandpencils.com)

[Twitter](https://twitter.com/robotsNpencils) | [GitHub](https://github.com/robotsandpencils)
