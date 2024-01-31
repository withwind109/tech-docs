在 Dart 中调用 C 语言代码通常涉及到使用 FFI（Foreign Function Interface）库。以下是一个简单的步骤来实现 Dart 和 C 语言的混合编程：

### 步骤 1: 编写 C 代码

创建一个 C 文件（例如`native_add.c`）:

```c
#include <stdint.h>

// 一个简单的C函数，用于计算两个整数的和
int32_t native_add(int32_t a, int32_t b) {
    return a + b;
}
```

### 步骤 2: 编译 C 代码为共享库

使用 gcc 或其他 C 编译器将 C 代码编译为动态链接库（.dll、.so、.dylib，取决于你的操作系统）:

```sh
# 对于Linux或macOS:
gcc -shared -o libnative_add.so native_add.c -fPIC

# 对于Windows:
gcc -shared -o native_add.dll native_add.c
```

### 步骤 3: 在 Dart 中使用 FFI 调用 C 函数

在 Dart 代码中，你需要导入`dart:ffi`库，并定义与 C 函数对应的 Dart 函数。

创建一个 Dart 文件（例如`ffi_example.dart`）:

```dart
import 'dart:ffi' as ffi;
import 'dart:io' show Platform, Directory;

// 定义一个typedef，对应C中的函数签名
typedef NativeAddFunc = ffi.Int32 Function(ffi.Int32 a, ffi.Int32 b);
// 定义一个Dart表示，对应上面的C函数签名
typedef NativeAdd = int Function(int a, int b);

void main() {
  // 加载共享库
  var path = './libnative_add.so';
  if (Platform.isWindows) {
    path = 'native_add.dll';
  } else if (Platform.isMacOS) {
    path = './libnative_add.dylib';
  }
  final dylib = ffi.DynamicLibrary.open(path);

  // 获取C函数的引用
  final NativeAdd nativeAdd = dylib
      .lookup<ffi.NativeFunction<NativeAddFunc>>('native_add')
      .asFunction();

  // 调用C函数
  int result = nativeAdd(10, 20);
  print('10 + 20 = $result');
}
```

### 步骤 4: 运行 Dart 代码

使用 Dart 命令行工具运行你的 Dart 程序:

```sh
dart ffi_example.dart
```

这将输出结果 `10 + 20 = 30`，表示 Dart 成功调用了 C 语言函数。

请注意，你需要确保共享库文件与 Dart 代码在同一目录下，或者提供正确的路径。此外，当你在不同的操作系统上运行时，共享库的名称可能需要相应地更改。
