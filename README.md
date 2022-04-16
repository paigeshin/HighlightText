# HighlightText

```swift
struct HighlightedText: View{
    let text: Text
    
    private static let regularExpression = try! NSRegularExpression(
        pattern: "#(?<content>((?!\\#).)*)\\#"
    )
    
    private struct SubstringRange {
        let content: NSRange
        let full: NSRange
    }
    
    init(_ string: String,
         foregroundColor: Color = .primary,
         highlightForegroundColor: Color = .primary) {
        let ranges = Self.regularExpression
            .matches(
                in: string,
                options: [],
                range: NSRange(location: 0, length: string.count)
            )
            .map { match in
                SubstringRange(
                    content: match.range(withName: "content"),
                    full: match.range(at: 0)
                )
            }
        var nextNotProcessedSymbol = 0
        var text = Text("")
        let nsString = string as NSString
        func appendSubstringStartingNextIfNeeded(until endLocation: Int) {
            if nextNotProcessedSymbol < endLocation {
                text = text + Text(nsString.substring(
                    with: NSRange(
                        location: nextNotProcessedSymbol,
                        length: endLocation - nextNotProcessedSymbol
                    )
                ))
                .foregroundColor(foregroundColor)
            }
        }
        for range in ranges {
            appendSubstringStartingNextIfNeeded(until: range.full.location)
            text = text + Text(nsString.substring(with: range.content))
                .foregroundColor(highlightForegroundColor)
                .bold()
            nextNotProcessedSymbol = range.full.upperBound
        }
        appendSubstringStartingNextIfNeeded(until: string.count)
        self.text = text
    }
    
    var body: some View {
        text
    }
}

```

# Usage

```swift
   HighlightedText("Hello #World#")
```
