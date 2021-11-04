# SwiftUI-MediaPicker

iOS/macOS media picker for importing images and videos in SwiftUI.

## Sample Code

```swift
import SwiftUI
import AVKit
import MediaPicker

struct ContentView: View {
    @State var urls: [URL] = []
    @State var isShowingMediaPicker = false
    
    var selectButton: some View {
        Button("Select Media") {
            isShowingMediaPicker = true
        }
        .mediaImporter(isPresented: $isShowingMediaPicker,
                       allowedMediaTypes: .all,
                       allowsMultipleSelection: true) { result in
            switch result {
            case .success(let urls):
                self.urls = urls
            case .failure(let error):
                print(error)
                self.urls = []
            }
        }
    }
    
    var body: some View {
        List {
            Section {
                ForEach(urls, id: \.absoluteString) { url in
                    switch try! url.resourceValues(forKeys: [.contentTypeKey]).contentType! {
                    case let x where x.conforms(to: .image):
                        AsyncImage(url: url) { image in
                            image
                                .resizable()
                                .scaledToFit()
                        } placeholder: {
                            ProgressView()
                        }
                    case let x where x.conforms(to: .audiovisualContent):
                        VideoPlayer(player: AVPlayer(url: url))
                            .scaledToFit()
                    default:
                        Text("Can't display \(url.lastPathComponent)")
                    }
                }
            } header: {
                selectButton
            }
        }
    }
}
```
