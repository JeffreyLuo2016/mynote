<h3 align="center">iOS导入.ttf字体文件<h3>
### 1. 将xx.ttf字体库加入工程里面
### 2. 首先info.plist中加入属性Fonts provided by application，在item 0 处填写导入的ttf文件名.

### 3. 查出你导入字体的font name  
  
```objective-c
NSArray * fontArrays = [[NSArray alloc] initWithArray:[UIFont familyNames]];
for (NSString * temp in fontArrays) {
	NSLog(@"Font name  = %@", temp);
}
```

### 4. 使用

```objc
UILabel * tempTwoLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 100, 300, 40)];
UIFont * fontTwo = [UIFont fontWithName:@"DFPWaWaW5-GB" size:15];
[tempTwoLabel setFont:fontTwo];
[tempTwoLabel setText:@"这是新字体——ONe----->华康娃娃体"];
[self.view addSubview:tempTwoLabel]
```
