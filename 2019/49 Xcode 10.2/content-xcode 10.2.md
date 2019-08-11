
> 苹果在这次春季发布会后，正式发布了 Swift 5 ，正式开启了 Swift ABI 稳定时代。与 Swift 5 一起，苹果发布了 Xcode 10.2，以支持 Swift 5 的开发。这个版本的 Xcode 新增了不少特性，同时解决了大量问题。我们翻译了 Xcode 10.2 Release Notes 文档，以方便大家了解 Xcode 10.2。
> 
> 文章比较长，建议主要了解新特性部分。由于整理匆忙，翻译有误之处还请留言指正。

**Xcode 10.2**

• 包含的 SDK：iOS 12.2, watchOS 5.2, macOS 10.14.4, tvOS 12.2

• 支持设备上调试的系统：iOS 8+, tvOS 9+, watchOS 2+

• macOS 系统要求：10.14.3+

## 通用

**新特性**

• 支持使用 macOS 内容缓存进行下载。

**已解决的问题**

• 解决了上传到 App Store 不包含符号信息的问题。

## Apple Clang Compiler

**新特性**

• `-Watomic-implicit-seq-cst` 是一个新的警告标识，默认情况下是关闭的，当以隐式的、顺序一致的方式使用 C 语言的 `_Atomic` 或 `__sync_*`，会发出警告。大多数代码库默认使用顺序一致性(sequential consistency)，但有些要求开发人员在r所有地方使用显式排序。此警告适用于后一种情况。（28172966）

• 使用引用包含(quote includes)的新诊断标识 framework header 代替了样式包含(style includes)的 framework。默认情况下警告是关闭，但可以设置 clang 的 `-Wquoted-include-in-framework-header` 标识来启用它。（37077034）

• `-Wmemset-transposed-args` 是一个新的警告标识，用于诊断对转换了第二个和第三个参数的 `memset` 的调用。例如，`memset(buf, sizeof(buf), 0)` 这个调用会使用新警告诊断。（42360478）

• `std::pair` 的构造函数有条件的标记为 `noexcept`，依据是其成员的相应构造函数是否为 `noexcept`。这是一个符合标准的扩展，具有潜在的性能优势，在那些构造时不会抛出异常的类型上执行更快的构造操作。（29537079）

• 在 `std::map` 或 `std::set` 中使用 `non-const` 可调用谓词的警告现在显示了错误容器的实例化信息而不是不相关的实现细节。（41370747）

• 不推荐使用 `<experimental/any>` 和 `<experimental/optional>` headers，而使用新的 `C++ 17` 的 header：`<any>` 和 `<optional>`。它们将在 Xcode 的未来版本中删除，不应该依赖它们的存在。（46903112）

• 已删除使用内联宏来控制 `libc++` headers 中符号的可见性，以支持更好的解决方案。这将优化包含 `libc++` headers 的代码的大小和性能，以及显著改善使用 `libc++` 时的调试体验。（47259325）

• 框架中的公共 headers 可能会错误地 `#import` 或 `#include` 私有 header，这会导致分层违规和潜在的模块引用循环。有一种新的诊断报告了这种违规行为。默认情况下它在 clang 中是关闭的，由 `-Wframework-include-private-from-public` 标识控制。 （38712182）

• 在框架 headers 中使用 `@import` 可防止在没有模块的情况下使用 headers。一个新的诊断标识在你传递 `-fmodules` 标识时会检测框 headers 中是否使用 `@import`。默认情况下，这个诊断在 clang 中是关闭的，并使用 `-Watimport-in-framework-header` 标志进行控制。（39192894）

• 以前，在为框架声明模块时省略 `framework` 关键字不会影响编译，但是默默地做了错误的事情。一个新的诊断（`-Wincomplete-framework-module-declaration`）和一个新的修复建议添加适当的关键字。默认情况下，将 `-fmodules` 标志传递给 clang 时，此警告处于启用状态。（39193062）

**已解决的问题**

• 修复了在检查 future 是否已附加到 `std::async` 中的 `promise` 时发生的数据竞争情况。解决了 `std::async` 返回一个 non-void future 的问题，但对于返回 `std::future<void>` 的调用，该问题仍然存在。（42548261）

• 即使从命令行调用 clang 以在单个调用中进行编译和链接时使用 `-flto=thin` 启用增量 LTO，链接也会成功。（47297739）

• 现在可以正确处理 `std::regex` 中的反转字符类，例如 `[\S]`。（43060054）

• `dsymutil` 不再耗尽大型项目的系统内存。（41422573）

## Asset Catalog

**已解决的问题**

• 解决了在为本地或企业分发应用程序时影响应用程序与 iOS 9.0、9.1 和 9.2 上的兼容性问题。使用 Xcode 10 构建的应用程序其 Asset Catalog（部署目标为 iOS 9.0，9.1 或 9.2）在使用本地或企业发布分发时生成的内容与这些 iOS 版本的运行时不兼容。使用 Xcode 10.2 重新构建应用程序可以解决此问题。（46893768,44535967）

• 改善了 Dark Mode 下的图像切片模式。（39388416）

## 构建系统

**新特性**

• `Implicit Dependencies` 现在支持在 `Other Linker Flags` 中查找使用 `-framework`，`-weak_framework`，`-reexport_framework`，`-lazy_framework`，`-weak-l`，`-reexport-l`，`-lazy-l` 和 `-l` 指定的链接框架和库的依赖关系。（7879587）

**已知问题**

• 如果您正在构建包含 Swift 代码的 framework 并使用 lipo 创建支持设备和模拟器平台的二进制文件，则还必须合并为每个平台生成的 `Framework-Swift.h` 头文件以创建支持设备和模拟器的头文件。（48635615）

例如，如果您已经构建：

```
- iOS/Framework.framework
- iOS Simulator/Framework.framework
```

得到：

```
- iOS/Framework.framework/Headers/Framework-Swift.h
- iOS Simulator/Framework.framework/Framework-Swift.h
```

创建一个新的：

```
- iOS + iOS Simulator/Framework.framework/Headers/Framework-Swift.h
```

新 Framework-Swift.h 的内容应该是：

```c
#if TARGET_OS_SIMULATOR
<contents of original iOS Simulator/Framework.framework/Framework-Swift.h>
#else
<contents of original iOS/Framework.framework/Framework-Swift.h>
#endif
```

**已解决的问题**

• 当用作目标依赖项时，外部目标是正确排序的。（44775299）

• 解决了在启用 `COMBINE_HIDPI_IMAGES` 和 `APPLY_RULES_IN_COPY_FILES` 设置时导致 Xcode 将 `PNG` 和 `JPEG` 文件作为 `TIFF` 文件处理的问题。（44623214）

• `OTHER_INPUT_FILE_FLAGS` 构建设置（传播源文件的自定义标志）现在可用于使用新构建系统时的自定义规则脚本。（46067251）

• `.xcconfig` 文件中的递归包含循环不再使构建系统崩溃。（42023748）

• 现在，目标构建阶段中为 `Core Data` 模型文件定义的每个文件标志将传递给 `Core Data` 编译器。（42919919）

## Clang 静态分析器

**已解决的问题**

• 静态分析器现在会在使用内容被移动后的 C++ 对象时发出警告，除非在使用对象之前将其重置为已知状态。（41349073）

• 静态分析器现在检查是否违反了 `IOKit` 和 `libkern` 的引用计数规则。这些违规行为可能导致泄密和 `use-after-free` 的问题。（46359592）

## 调试

**新特性**

• UIStackView 属性现在可以显示在视图调试器对象检查器中。（36351873）

• 如果在调试时遇到内存资源异常，Xcode 现在可以自动捕获内存图。您可以在方案的运行设置 “`Diagnostics`” 选项卡中启用内存图捕获。（45285932）

• 在 iOS 和 watchOS 上，当接近内存限制时，Xcode 会在 `Memory Report` 中显示运行应用程序的内存限制。使用 `Instruments` 和 `Xcode Memory Debugging` 来优化您的应用程序，以尽可能减少内存占用。（40556954）

• 视图调试器呈现更紧凑的3D布局。（43523921）

**已解决的问题**

• 在 `Assistant Editor` 中显示反汇编的速度得到了改进。（31633031）

## 文档查看器

**新功能**

• 可以通过 SDK 可用性、引入版本和弃用来过滤符号文档。还可以过滤文档以仅显示文章或示例代码。例如，您可以过滤文档以显示 UIKit 等框架所有示例代码。（45236860）

## Instruments

**已知的问题**

• 在 watchOS 应用程序中 profile Swift 代码时，`Instruments` 可能会崩溃。（47368181）

## Interface Builder

**新功能**

• 双击 storyboard 不再缩放。相反，使用触控板上的捏合手势或按住 Option 并滚动来进行缩放。 （29139870）

• Apple TV 的 Interface Builder 支持 TVUIKit 框架公开的用户界面元素。 （35868606）

**已解决的问题**

• 修复了在重新打开 storyboard 后选中 `Bindings inspector` 中的 `Bind to` 复选框时可能发生的崩溃。（33348238）

• Interface Builder 预览中的旋转按钮在 Dark Mode 下可见。 （42396497）

• 使用 `@objc` `@IBAction` 注释时，Interface Builder 可以正确解析 Swift 文件中的 Actions。 （25465675）

• 在资源目录中指定的对齐矩形的图像在 Interface Builder 画布中正确呈现。 （46595020）

• 改进了如果 `asset catalog` 中的文件名不以 @2x 或 @3x 结尾，在 Interface Builder 画布中的 2x 和 3x 插槽中图像的固有大小。（44759471）

• 使用检查器对 `NSImageView` 所做的更改现在可以毫无延迟地可靠地反映在画布中。 （30196881）

• `ibtool --export-string-file` 包含在具有 NSCell 实例的控件上指定的本地化提示。（24421623）

• 解决了导致图像在 storyboards 中显示为问号的问题。（42475635）

• 在 Interface Builder 画布中呈现的图像使用与所选设备匹配的比例因子进行渲染。（18703159）

• 在 asset catalog 中使用模板呈现模式标记的图像在 Interface Builder 画布中正确呈现。（29049562）

## 链接

**已解决的问题**

• 当主项目没有用 Swift 编写时，现在可以在 dyld 缓存中找到 Swift 库。 （48385698）

• 解决了导致链接器错误在问题导航器中显示为“Linker command failed with exit code 1”而不是显示实际错误消息的问题。 （39141740）

## LLDB调试器

**新功能**

• 现在可以在闭包内的LLDB表达式评估中使用 `$0`，`$1`，...。（20719448）

• LLDB 现在支持 C 变长数组。（39606394）

• LLDB调试器有一个新的命令别名 `v`，用于“`frame variable`”命令，用于在当前堆栈帧中打印变量。因为它绕过表达式求值程序，所以 v 可以快得多，并且应优先于 p 或 po。（40066460）

**已解决的问题**

• 调试器现在可以解析绑定到私有类型的泛型变量的类型。（38231646）

• 在 Swift 中使用 po 调试 watchOS 应用程序时，现在返回正确的结果。（47162433）

• 调试器正确支持内联泛型上下文中的泛型变量。（28859432）

• Swift 词典和集合的数据格式化程序更可靠。 （43045289）

## 本地化

**新功能**

• 打开使用任何已弃用的本地化标识符的项目现在会为每个使用的标识符生成警告。选择其中一个警告会提供一个助手，用于将关联的旧“`lproj`”目录中的文件迁移到以等效新标识符命名的“`lproj`”目录。如有必要，此过程还会将项目的开发区域更新为新标识符。迁移的项目与旧版本的 Xcode 兼容。（9777671）

• 现在可以为项目开发区域导出和导入本地化信息。（41878212）

**已解决的问题**

• Xcode 现在更仔细地区分遗留的本地化标识符（如“English”）和现代本地化标识符（如“en”），并在项目文件和用户界面中同时表示它们。（45469882）

• 建议对所有项目启用 `Base Internationalization`，并且为任何当前不使用 `Base Internationalization` 的项目提供升级，即使它们只有一个本地化。升级后的项目与以前版本的 Xcode 向后兼容。（15160454）

• 现在可以将本地化添加到没有任何本地化文件的项目中，并且不会提示您将文件复制到新的本地化目录。（42771349）

## Playgrounds

**新功能**

• Playgrounds 现在在运行时执行内存安全检查。违反对内存陷阱的独占访问的代码，会给出诊断消息：“Simultaneous accesses to […], but modification requires exclusive access.”（SR-8126）（33820622）

**已解决的问题**

• 解决了阻止 Playgrounds 执行的问题。（47226381）

• 修复了使用辅助源码编辑 Playgrounds 时可能发生的崩溃。（42097728）

• 修复了编辑包含占位符的片段时可能发生的崩溃问题。（43242401）

• 修复了一个问题，该问题可能会影响 Interface Builder 文档中的更改在不关闭工作区窗口的情况下反映在 Playgrounds 中。（46830864）

## 重构

**已解决的问题**

• 重命名重构现在正确地重命名带有外部参数标签的单个参数的函数，并且具有将相应参数作为尾随闭包传递的调用点。（42162571）

• 使用 `Refactoring > Rename` 重命名 document 现在会更新应用程序的 Info.plist 文件以作匹配 （41327509）

## 模拟器

**已解决的问题**

• 改善了与模拟设备交互的性能和响应能力。（47864185）

• 解决了无法在具有大量模拟设备的 Mac 上启动模拟设备的问题。（47712686）

• 解决了将多个联系人，照片或视频项目同时拖动到模拟设备窗口时发生的故障。（46736098）

• macOS 和模拟 iOS 设备之间的粘贴板同步更可靠。（46817121）

• 现在，您只需提示一次授权麦克风访问，就可以使用所有模拟器设备。（45715977）

• iPhone XR 模拟器的交互性能和响应能力已得到改进。 （44657262）

## Source Control

**新功能**

• Xcode 使用 SSH 配置输出来确定应该使用哪个 SSH 密钥对来验证给定的远程仓库。 （47302670）

**已解决的问题**

• 除了用于连接到 Git 服务器的 PEM 格式之外，Xcode 现在还支持使用 `OpenSSH` 格式的 SSH 私钥。 （40867126）

• 解决了导致 SSH 密钥密码 keychain 查找失败的问题。（47578552）

## 代码编辑

**新功能**

• “Fold Methods & Functions” 编辑器菜单项可以折叠 Swift 中的计算属性。（43428274）

• Code completion 在计算属性声明中提供 get，set，didSet 和 willSet 作为可能的实现。（20957182）

• 在可选枚举类型的上下文中，除了 `Optional.none` 和 `Optional.some(_:)` 之外，code completion 会提示枚举的其它 case。（23549753）

**已解决的问题**

• 重写 UITableViewController 方法时，Code completion 不会出现重复的委托方法名称。（21161476）

• 引用不同的文件的 Fix-its 操作将不适用于当前文件。（31371021）

• 被拖动的文本显示为透明图像。（31890166）

• 代码编辑器现在使用系统颜色作为占位符。（32307338）

• 在占位符之前直接键入换行符时，编辑器不会填充占位符。（32853933）

• 修复了如果包含标记的行已被编辑，则使用 Mark 在 Swap 中发生崩溃 （41874263）

• 打开折叠功能区时，编辑器中的打字和滚动性能得到改进。 （42941556）

• 修复了换行的一致性。 （44520372）

• 修复了显示三个助理编辑器时发生的崩溃。 （45230485）

• 修复了输入具有多个游标的换行符时发生的崩溃。 （45601228）

• 当关闭换行时，提高了使用折叠代码滚动源文件的速度。 （45712602）

• 改进了使用黑暗主题时警告和问题的显示。 （44925116）

（略）

## Swift

• 请参阅 Swift 5 Release Notes for Xcode 10.2 https://developer.apple.com/documentation/xcode_release_notes/xcode_10_2_release_notes/swift_5_release_notes_for_xcode_10_2?language=objc。

## 测试

**新功能**

• `xccov` 支持将多个覆盖报告及其关联的归档合并到一个汇总报告和归档中。将报表合并在一起时，对于在生成原始报表之间发生更改的源文件，聚合报表可能不准确。如果没有代码更改，则汇总报告和存档会是准确的。有关更多信息，请参阅 xccov 手册页。 （38050969）

• xccov 现在支持区分 Xcode 覆盖率报告，可用于计算覆盖范围随时间的变化。 （43439165）

• 静态库和框架目标现在作为顶级条目显示在 coverage 报告中，其中行覆盖值在包含静态库或框架的所有目标中聚合。这也解决了静态库或框架目标的源文件将包含在 coverage 报告中的问题，即使目标本身已从方案中的代码覆盖范围中排除。（22578123）

**已知的问题**

• Swift initializers 显示在覆盖率报告中，没有名称。（47467864）

• 启用 `Parallelization` 时，`Clones` 中的录制无效。 （43699252）

• 如果同一 `PRODUCT_NAME` 存在多个测试主机目标，则会为测试目标选择错误的测试主机应用。（46475115）

• 启用测试并行化时，性能分析测试不正常。 （44836817）

**解决方法**：导航到 Product > Scheme > Edit Scheme > Test > Info，选择测试目标旁边的 Options ，并禁用“`Execute in parallel`”，以在分析时禁用并行测试。

**已解决的问题**

• 解决了导致 Swift 源文件中的方法在 coverage 报告中命名为“`Definition at <line>:<column>`”的问题。（46432533）

• `XCUIScreen` 现在正确实现了 `isEqual:` 和 `hash`。（32179407）

• 当单击代码编辑器以获取存在于多个测试目标中的测试方法或类时，或者对于由子类继承的测试方法时，Xcode 现在会显示一个菜单，允许选择要运行的单个目标或类（或全部）选定的测试。（45975871）

• 解决了可能阻止在 coverage 报告视图中展开文件的问题。（44458167）

• 如果由于某种原因（例如运行时链接失败）在测试期间无法加载测试包，Xcode 现在会报告描述失败原因的描述性错误消息。如果您正在使用 xcodebuild，则此失败信息存在于测试活动日志中并显示在 stdout 中。结果包中包含的结构化日志中也存在该错误。 （45242409）

• 如果由于测试运行器在启动时崩溃而导致测试失败，Xcode 会尝试生成描述失败的详细错误消息。如果您正在使用 xcodebuild，则此失败存在于测试活动日志中并显示在 stdout 中。结果包中包含的结构化日志中也存在该错误。（29148418）

• 如果在测试运行时 xcodebuild 被 SIGINT 信号终止，则会将有效的结果包写入磁盘，并包含在终止之前完成的测试的结果。同样，如果取消在 Xcode 中运行测试，则会生成一个包含已完成测试结果的有效结果包。（45022325）

• xcodebuild 或 Xcode 的第二个实例不会删除在并行分布式测试期间创建的模拟器拷贝。（40738122）

• 解决了可能导致多个目标中包含的文件的代码覆盖率不正确的问题。（40409346）

• 在测试期间收集的崩溃报告不再遗漏重要字段，例如终止原因和描述。（44405884）

• 未明确包含在目标的 `Headers Build Phase` 中的 headers 不再出现在 coverage 报告中的目标条目中。这解决了一个问题，其中不需要的 headers 可能出现在目标的覆盖率报告中 - 例如来自链接的框架或库。如果您发现覆盖率报告缺少 header，请确保它们包含在相应目标的 Headers Build Phase 中。 （36187447）

• 具有多个测试目标的项目（每个测试目标包含一个继承自共享 XCTestCase 子类的测试类）不再显示来自其他目标的不存在的运行时（“rT”）测试。（46042417）

