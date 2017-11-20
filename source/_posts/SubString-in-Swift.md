---
title: Substring in Swift
date: 2017-11-14 16:23:05
tags: [Swift]
---
Swift 的 string 替代了之前 OC 中的 NSString，但是取子串的方法却没有得到很好的延续，都需要涉及 String.Index 的转换，写起来很不方便，增加了很多的代码，所以我们来简化下它。   
<!-- more -->  
比如我们有一个字符串 str = "Hello, World", 想要取 “,” 以前的字符串，在 OC 中，我们可以这么写   

	subStr = [str substringToIndex: 5];  

很写意，很简洁。我们再来看看 Swift 的写法，我们需要先得到 “,” 的Index，然后再通过Index来获取 subStr   

	let index = str.index(str.startIndex, offsetBy: 5)
	subStr = str.substring(to: index)

硬生生多了这么多代码，如果是中间某段字符串利用Range来获取就更麻烦了。比如这么一个字符串 str = "Hang in there"，我们想获取中间的 "in" 字符串，在 OC 中，我们可以这么写  

	subStr = [str substringWithRange:NSMakeRange(5, 2)];

虽然多了 NSRange 的获取，代码依然不是很多，写起来也简单明了。换成 Swift 呢，同样，我们也需要获取 "in" 的 Range  

	let startIndex = str.index(str.startIndex, offsetBy: 5)
	let endIndex = str.index(str.startIndex, offsetBy: 7)
	let range = startIndex ... end
	subStr = str[range]
	
实在是。。。有点儿多呢，相比之下，还是之前 NSString 的 通过 Int 型变量来获取子字符串方便的多，所以就写了一个自定义下标的方法 

    subscript(range:CountableClosedRange<Int>) -> String? {
        get {
            if let startIndex = self.index(self.startIndex, offsetBy: range.lowerBound, limitedBy: self.endIndex),
            let endIndex = self.index(self.startIndex, offsetBy: range.upperBound, limitedBy: self.endIndex) {
                return String(self[startIndex ... endIndex])
            }
            return nil
        }
        set {
            if let subStr = newValue {
                if let startIndex = self.index(self.startIndex, offsetBy: range.lowerBound, limitedBy: self.endIndex),
                    let endIndex = self.index(self.startIndex, offsetBy: range.upperBound, limitedBy: self.endIndex) {
                    self.replaceSubrange(startIndex ... endIndex, with: subStr)
                }
            }else {
                print("index out of bounds")
            }
        }
    }
    
开区间的下标方法同理，修改下区间设置的部分代码就可以。这里不允许传入长度以外的区间，当然也可以根据自己的需求，修改 else 部分。
