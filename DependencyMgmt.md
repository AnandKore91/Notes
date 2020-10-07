# Cocoapods and SPM (Swift Package Manager)

### Cocoapods
- https://medium.com/flawless-app-stories/create-your-own-cocoapods-library-da589d5cd270
- https://samwize.com/2017/10/01/creating-a-swift-framework-cocoapods/

### High-level steps
- New Project -> Framework -> Name
- Namspacing -> Struct -> Class(Module)
- Create a pod spec
    - “pod spec create MyFramework.” | This will create a default MyFramework.podspec.
    - Update podspecs
    - Name, version, source,  etc.
- Validate the pod
    - “pod lib lint”
- Publish the pod
    - “pod trunk push”
