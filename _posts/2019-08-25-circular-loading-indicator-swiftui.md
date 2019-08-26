---
layout: post
title: Creating a Circular Progress Indicator in SwiftUI
tags: ["ios", "swift", "swiftui"]
---

Under Construction...

```swift

struct CircularProgressIndicator: View {
    static private let _animation = Animation
        .linear(duration: 2)
        .repeatForever(autoreverses: false)

    @State var active: Bool = false

    var body: some View {
        ZStack {
            Circle()
                .stroke()
            Circle()
                .fill(Color.blue)
                .scaleEffect(CGFloat(0.10))
                .offset(x: 0, y: -40)
                .rotationEffect(.degrees(active ? 0 : -360))
                .animation(CircularProgressIndicator._animation)
        }
        .frame(height:CGFloat(80))
        .onAppear { self.active.toggle() }
    }
}

struct CircularProgressIndicator_Previews: PreviewProvider {
    static var previews: some View {
        CircularProgressIndicator()
    }
}

```
